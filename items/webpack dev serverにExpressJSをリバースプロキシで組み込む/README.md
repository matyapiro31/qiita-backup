expressを組み込みたい時、Webpack dev serverにはwebpackd dev middlewareを使って組み込みますが、vue.js用テンプレートのwebpackの設定が面倒臭すぎてリバースプロキシを使うことにしました。

#パッケージのインストール
http-proxyパッケージを使います。

`npm install --save-dev express http-proxy`

```javascript:server.js
const express = require('express')
const app = express()
const NODE_ENV = process.env.NODE_ENV
const WEBPACK_PORT = process.env.WEBPACK_PORT || 8080
const PORT = process.env.PORT || 8081
const httpProxy = require('http-proxy')
const apiProxy = httpProxy.createProxyServer()

// for Promise debugging.
process.on('unhandledRejection', console.dir)
// Serve the files on port 8081.
app.listen(PORT, (err) => {
    if (!err) {
        console.log(`Server is running at http://localhost:${PORT}/`)
    } else {
        console.log(JSON.stringify(err))
    }
})

app.get('/api/v1/search', async (req, res) => {
// insert api code here.
})

if (NODE_ENV=="development") {
  app.all(/.*/, async (req, res) => {
      apiProxy.web(req, res, {target: `http://localhost:${WEBPACK_PORT}`})
  })
}
```

これだけで完了です。本番環境ではNginxなどを使ってリバースプロキシしてください。
