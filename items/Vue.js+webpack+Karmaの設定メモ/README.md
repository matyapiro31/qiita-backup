# インストール
通常の手順でインストールします。単体テストにはKarma+mochaを使います。

```bash
npm i -g vue-cli
vue init webpack vue-example
cd ./vue-example
git init 
```
# .gitignoreの設定
`node_modules/`と`dist/`がきちんと含まれていることを確認します。

# パッケージインストール
yarnを使います。パッケージ数が多い上にサイズもそこそこあるのでかなり時間がかかります。

```bash
yarn install
```
追加でこれらも入れておきます。

* sw-precache-webpack-plugin
* Vuetify
* gettext-parser

# index.htmlを設定する

PWAとして使えるよう、metaタグなどを設定します。

```diff:index.html
    <!DOCTYPE html>
    <html>
      <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width,initial-scale=1.0">
+       <meta http-equiv="X-UA-Compatible" content="IE=edge">
+       <link rel="icon" type="image/png" sizes="32x32" href="/static/img/icons/favicon-32x32.png">
+       <link rel="icon" type="image/png" sizes="16x16" href="/static/img/icons/favicon-16x16.png">
+       <!--[if IE]><link rel="shortcut icon" href="/static/img/icons/favicon.ico"><![endif]-->
+       <link rel="manifest" href="/static/manifest.json">
+       <meta name="theme-color" content="aliceblue">
        <title>vue-example</title>
      </head>
      <body>
        <div id="app"></div>
        <!-- built files will be auto injected -->
      </body>
    </html>
```

# ドキュメントのタイトルを設定できるようにする

`src/utils`ディレクトリを作成し、拡張用モジュールを作ります。

```diff:src/main.js
    import Vue from 'vue'
    import App from './App'
    import router from './router'
+   import utils from './utils'
```
ディレクトリ内にタイトル変更用のファイルを作り、読み込めるようにします。

```javascript:src/utils/index.js
import Vue from 'vue'
import title from './title'

Vue.mixin(title)
```
`src/utils/title.js`を作成し、コンポーネントごとにタイトルを変更できるようにします。
サイト名は一段上のコンポーネントで統一できるようにします。

```javascript
export default {
  created() {
    if (this.$parent) {
      let site = this.$parent.$options.site
      let title = this.$options.title
      if (site && title) {
        document.title = `${site} - ${title}`
      }
    }
  }
}
```

# テストの設定

## 1. `static/`に置いたファイルを認識しない
これはKarmaになぜか設定がないだけなのでプロキシを設定します。

```diff:test/unit/karma.conf.js
     preprocessors: {
       './index.js': ['webpack', 'sourcemap']
     },
+    proxies: {
+      '/static/': '../../static/'
+    },
     webpack: webpackConfig,
     webpackMiddleware: {
       noInfo: true

```

## 2. router-linkを書くとエラーが出る
テストでrouterをimportしていないのが原因です。デフォルトのテストの例の場合は、以下のように付け加えます。

```diff:test/unit/specs/HelloWorld.spec.js
    import Vue from 'vue'
    import HelloWorld from '@/components/HelloWorld'
+   import router from '@/router'

    describe('HelloWorld.vue', () => {
      it('should render correct contents', () => {
        const Constructor = Vue.extend(HelloWorld)
-       const vm = new Constructor().$mount()
+       const vm = new Constructor({router}).$mount()
        expect(vm.$el.querySelector('.hello h1').textContent)
        .to.equal('Welcome to Your Vue.js App')
      })
    })
```

# 参考ページ

https://github.com/vuejs-templates/webpack/issues/709
https://stackoverflow.com/questions/21067710/how-to-fix-404-warnings-for-images-during-karma-unit-testing
