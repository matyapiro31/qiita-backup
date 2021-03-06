1. 事の始まり
---
---
私はドメインの購入/管理にVALUE-DOMAINを利用しているのですが、新しくドメインを購入し、Whoisの情報を代理公開してもらおうとすると、サービスの規約の変更でもあったのか、同意を求めるポップアップが出てきたので、同意して代理公開にしておきました。その時点で出ていた警告は

---

`全項目を当社名義に統一するには、 ここをクリックしてください。※名義を代理公開する際、ユーザー情報が適正かチェックを行います。正しくない場合は、エラーが出ますので修正ください。`
という文章と、

---
`代理名義になされた状態で、広告メール等の迷惑・規約違反・違法行為・違法情報の提供を行われた場合は、`
`弊社の判断でドメインを停止させていただきます。`
`また、ICANNの定める規約に違反する場合、ドメイン紛争が起きた場合は、ユーザー情報に戻ります。`
`宜しければOKを押してください。`
という警告だけでした。しかし...

---
2. 突然、自サイトに繋がらなくなった
---
---
後日、突然サイトに接続できなくなりました。
何回やっても
ブラウザは**ERR_NAME_NOT_RESOLVED**を返すので、頑張って原因を突き止めようとしてみました。

---
3. 原因究明までにやったこと
---
---
1. サーバの再起動
2. サーバのアップデート
3. CDNサービスの利用の中断
4. ネームサーバの変更
5. DNSレコードのチェック
6. メールの確認

---
まずサーバを再起動し、sshで接続してlocalhostなら表示できるか試してみました。

→ 表示可能
ではサーバがバグで外部と通信できないのか?と思い、アップデート。

→ これも問題なし。

---
グローバルIPアドレスで叩いてみる

→ 問題なし。

???

この時点では何一つ問題がなかったので、VALUE-DOMAINとCDNサービスのコンソールを確認

→ 何もエラーなどは表示されておらず、正常

---
おそらくサービス側で検出できないこちらの設定ミスの類か?

→ 全てのキャッシュのリセットや、ネームサーバの変更などを試してみる。

→ 効果なし

---
💡この問題は自分が持っている全てのドメインに同じように発生している。
→ VALUE-DOMAINの問題に違いない!
しかし、コンソールではエラー無し。

---
ではメールは?
→ **【重要】[バリュードメイン] ドメイン利用制限設定　完了通知** なるメールが来ていた。
ここでメールの内容を紹介します。

---
<div class="small"><div class="small"><div class="small"><div class="small"><div class="small"><div class="small"><div class="small"><div class="small">From:
verification-noreply@onamae.com<br/>
(本文一部抜粋、変更あり)<br/>
<br/>
━━━━━━━ GMO INTERNET, INC. DBA ONAMAE.COM  ━━━━━━━━━━<br/>
━━━━━━━━━━━━━━━━━━━━━━ http://www.onamae.com/ ━<br/>
<br/>
────────────────────────────────── <br/>
■ドメイン利用制限　設定完了■<br/>
────────────────────────────────── <br/>
<br/>
当社サービスをご利用いただき、まことにありがとうございます。<br/>
本メールは、先日お送りしているドメイン登録者（Registrant）情報のメールアドレス有効性認証をご対応いただけていない方に送信しております。
対応期日までにドメイン登録者（Registrant）情報のメールアドレス有効性認証をご対応いただけませんでしたので、本メールアドレスが設定されているドメインの利用を制限させていただきます。<br/>
制限解除をご希望の場合は、以下URLへアクセスし有効性確認をお願いいたします。<br/>
https://www.onamae.com/domain/verification?hogehoge<br/>
なお、有効性を確認した後の利用制限解除につきましては、順次対応させていただき、制限解除完了時には別途通知させていただきます。<br/>
※メールアドレス以外にも、Whois情報が不正確なドメインは、登録抹消や使用停止の対象となることがございますのでご注意ください。<br/>
※各種情報の確認・修正等はご利用のドメイン管理会社へご相談くださいますようお願いいたします。</div></div></div></div></div></div></div></div>

---
その後、メール内のURLにアクセスして認証したのですが、次に来たメールがこれ。

---
<div class="small"><div class="small"><div class="small">From:
verification-noreply@onamae.com<br/>
ドメイン登録者（Registrant）情報のメールアドレス有効性認証をご対応
いただけなかったため、該当ドメインに対して利用を制限させていただいて
おりましたが、有効性の確認がとれましたので制限を解除いたしました。<br/>
<br/>
なお、インターネット全体に反映するまで最大72時間程度かかる場合が
ございますので、今しばらくお待ちくださいますようお願いいたします。</div></div></div></div>

---
私が持っているドメインは3つあったのですが、うち2つはすぐに復活しました。しかし、最後の1つは3日目になってようやく復活しました。

---
3. 理不尽なわけ
---
---
+ 全く何も通知が来なかった
+ いきなり全てドメイン利用の停止
+ メールがvalue-domain.comではなく、onamae.com
+ 3日間もアクセスできないまま

まさに迷惑千万。ちなみに前に取った2つの場合は何もメールなどは来ませんでした。

---
というわけで、DDoS攻撃の件といいGMOという企業は最悪です。ブラック企業という噂もあるしね。それでも使うかどうかはアナタ次第...
