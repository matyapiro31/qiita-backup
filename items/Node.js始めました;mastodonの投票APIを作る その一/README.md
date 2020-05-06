#所感
非常に便利そうなライブラリが沢山ある。ただ、それらは他の言語のそれと違って標準ライブラリではない。いわゆる公式なライブラリがあまり存在しないところはJSの文化なのだろうか。個人的にはDebianの放置されすぎてるパッケージ群を見てきたのでいずれ動作しないライブラリの死骸の山ができるんじゃないか、自分のアプリが依存していて動かなくなるんじゃないかと不安が募る。C言語のライブラリの場合は.dllや.soになり、多くの言語で再利用が可能です。しかし、NodeJSではNodeJSでしか動かない。
#やりたいこと
マストドンの拡張機能の仕様がまだ定まってないので独自の拡張機能を作る。=>マストドンに必須のNodeJSなら導入難易度低いよね。
というわけで勝手に`/api/v2`を作ってやらう。
#計画
Web APIというのは要は特殊なWebページの一種(暴論)なので、Webサーバがあれば十分です。というわけでNode.js以外に導入するのはライブラリのみです。
Nginxのマストドン用の設定に以下の内容を加えてください。proxy_passのポート以外他と同じです。

```bash
location /api/v2/ {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto https;
    proxy_set_header Proxy "";

    proxy_pass http://127.0.0.1:4040;
    proxy_buffering off;
    proxy_redirect off;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;

    tcp_nodelay on;
  }
```
これで`/api/v2`を4040で待ち構えているNodeJSのサーバに流すことができます。
#APIを作る
Web APIはGET、POST、PATCH、DELETEなどのHttpメソッド、`Content-Type`で決まるボディ部分の書式、必要なデータと任意のデータ、
返すデータの書式、APIの挙動、どのくらいの回数呼び出すか、が考えるべきポイントです。特に呼び出し回数はそれだけアクセス数が増えることになるので、出来る限り一度にできることが重要です。また、マストドンはAPIを出来る限りシンプルにすることを心がけており、認証はアクセストークンのみです。ただしこれもあまりよろしくない仕様です。
というわけで投票APIを作りたいと思います。

| name             |      comment|   required| 型|初期値|
|-----------------:|------------------:|-------------------:|--:|--:|
|title             |タイトルもしくは質問文|true|string|''|
|choices|質問文|true|Array|[]|
|limit|投票期限|false|Number|0|
|type|グラフの形状|false|string|'bar'|

設定が必須なデータ以外は初期値を持たせることで省略できるようにします。
choicesは配列です。curlで指定するときは"choices=foo"ではなく、"choices[]=bar"とします。
次にメソッドです。初めの投票の作成はPOSTでいいでしょう。昔の◯acebookなどはGETでメールアドレスなどを送っていましたが、その場合ブラウザ履歴などから情報が漏れてしまいます。
この場合ほぼ流れで`Content-Type`は**application/x-www-form-urlencoded**になります。
このAPIが返す値については公開/非公開などの情報は画像アップロードAPIと同じく設定しません。<sup><sub>あ、バグ見っけた</sub></sup>
返すべき値は期限、HTMLに変換された文章、投票idです。
JSONで返すのが一番簡単です。他のAPIに習い、名前はできるだけ統一します。urlはブラウザで表示するときのもの、uriはDataURIによる埋め込み用です。

```javascript
{
  id: Number,
  limit: "YYYY-MM-DDTHH:mm:ss.sssZ",
  meta: {
    title: string,
    type: string,
    choices: [
      {
        content: "<p>answer1</p>",
        id: Number,
        vote: 5000000000000000
      },
      {
        content: "<p>answer2</p>",
        id: Number,
        vote: 10
      }
    ]
  },
  created_at : "YYYY-MM-DDTHH:mm:ss.sssZ"
  type: "poll",
  url: string,
  uri: string
}
```
さて、URLはわかりやすく`/api/v2/poll`とします。
次にPostgreSQLに保存するテーブルの書式を決めます。
選択肢のデータは別のテーブルに保存することにします。LIMITは予約語なのでtime_limitとしました。
連続値の扱いにシーケンスを用いているので先にシーケンスも作成します。

```sql
CREATE SEQUENCE poll_id_seq;
CREATE SEQUENCE choices_id_seq;
```
次にテーブルの列名、型、既定値を設定します。

```sql
--投票のテーブル
CREATE TABLE poll (
id bigint DEFAULT nextval('poll_id_seq'),
account_id int,
title text DEFAULT '',
time_limit timestamp DEFAULT (now())::timestamp ,
created_at timestamp DEFAULT (now())::timestamp ,
type text DEFAULT 'bar',
choices_id int[] DEFAULT ARRAY[]::integer[],
url text DEFAULT '',
uri text DEFAULT ''
);
-- 選択肢のテーブル
CREATE TABLE choices (
id int DEFAULT nextval('choices_id_seq'),
content text DEFAULT '',
vote int DEFAULT 0
);
```
さて、次に問い合わせの流れを確認しましょう。
1. `/api/v2/poll`にデータを送信する。
2. 受け取ったデータをチェックした後にSQLで保存する。
3. SQL文のエラーをチェックし、失敗している場合は削除する。
4. 正しい場合はJSONでデータを返し、不正な場合は400を返す。別に418でも良いんですが。 

Node.jsのコードはこうなります。

```js
const PORT=process.env.PORT||4040;
const DATABASE_URL=process.env.DATABASE_URL||'postgresql://mastodon@localhost:5432/mastodon_production';
const fs=require('fs');
const pg=require('pg');
const express=require('express');
const app=express();
const bodyParser = require('body-parser');

/* express setting. */
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
/* pg setting. */
var client=new pg.Client(DATABASE_URL);
app.post('/api/v2/poll', async (req, res) => {
    const title=req.body.title||'';
    const choices=req.body.choices||[];
    const limit=parseInt(req.body.limit)||0;
    const type=req.body.type||'bar';
    const token=(req.get('Authorization')||'').substring(7);
    const choices_id=new Array();
    const choices_data=new Array();

    await client.connect();
    //get Account ID.
    const account_id=await client.query('SELECT id FROM  oauth_access_tokens WHERE token=$1', [token]);
    console.log(account_id.rows.length===0?"Invalid AccessToken.":"");
    //check if parameter is valid.
    if (!Array.isArray(choices)) {
        res.status(400);
        res.send('Invalid object type: choices[].');
        res.end();
        return 0;
    } else if (account_id.rows.length===0) {
        res.status(400);
        res.send('Invalid object type: choices[].');
        res.end();
        return 0;
    }
    //set choices value.
    for (let i=0;i<choices.length;i++) {
        if ((typeof choices[i])!=="string") {
            res.status(400);
            res.send('Invalid parameter: choices.');
            res.end();
        } else {
            const ret=await client.query('INSERT INTO choices (content) VALUES ($1) RETURNING *', [choices[i]]);
            choices_id.push(ret.rows[0].id);
            choices_data.push({
                content: ret.rows[0].content,
                id: ret.rows[0].id,
                vote: ret.rows[0].vote
            });
        }
    }
    //set time limit as unix time.
    const time_limit=limit+Math.floor(new Date().getTime()/1000);
    const ret=await client.query(
        'INSERT INTO poll (title,time_limit,type,account_id,created_at,choices_id,url,uri) VALUES ($1,to_timestamp($2),$3,$4,now(),$5,$6,$7) RETURNING *',
        [title, time_limit, type, account_id.rows[0].id,choices_id,"/system/media_attachments/poll/"+(new Date().getTime())+"0","tag:example.com"]);
    await client.end();
    json_ret={
        "id":ret.rows[0].id,
        "limit": ret.rows[0].time_limit,
        "meta": {
            "title": ret.rows[0].title,
            "type": ret.rows[0].type,
            "choices": choices_data
        },
        "created_at": ret.rows[0].created_at,
        "type": "poll",
        "url": ret.rows[0].url,
        "uri": ret.rows[0].uri
    };
    res.json(json_ret);
    res.end();
});
```
これで質問の作成APIが完成です。
返ってきた値の例：

```json
{
    "id": "9",
    "limit": "2017-09-08T15:59:09.000Z",
    "meta": {
        "title": "test",
        "type": "circle",
        "choices": [
            {
                "content": "test",
                "id": 41,
                "vote": 0
            },
            {
                "content": "test2",
                "id": 42,
                "vote": 0
            }
        ]
    },
    "created_at": "2017-09-08T15:57:29.000Z",
    "type": "poll",
    "url": "/system/media_attachments/poll/15048862490000",
    "uri": "tag:example.com"
}
```
次は投票して票数を操作するAPIを作りたいと思います。
最後にSVGに投票を変換して、拡張でtootにSVGを表示させたいと思います。
