## Requirements

1. Partnerアカウント作成 & development Store作成
2. Node.js >= 14.13.1 and package manager(npm, yarn, pnpm)

## 新しいアプリ作成

```shell
yarn create @shopify/app --template=ruby
```

### Setup Ruby on Rails

```shell
bundle install
bundle exec rails app:template LOCATION=./template.rb
```

今回は検証用なのでSQLiteを使用

## ngrokでtunnelを作成

1. Create account for ngrok
  - ref: https://ngrok.com/
2. Download and start ngrok
```shell
$ unzip /path/to/ngrok.zip
$ ngrok config add-authtoken <token>
$ ngrok http 80
```

## Start local develop server

```shell
yarn run dev
```

1. Shopifyのログインページに遷移し、Shopify CLIを認証する
2. アプリ名を設定
3. 検証ストアを設定
4. 先程取得したngrokのアクセストークンを登録

```shell
yarn run v1.22.18
$ shopify app dev
✔ Dependencies installed

To run this command, log in to Shopify Partners.
👉 Press any key to open the login page on your browser

Auto-open timed out. Open the login page: Log in to Shopify Partners

✔ Logged in.

Looks like this is the first time you're running dev for this project.
Configure your preferences by answering a few questions.

✔ Create this project as a new app on Shopify? · Yes, create it as a new app
✔ App Name · stamps-app-demo
✅ Success! stamps-app-demo has been created on your Partners account.
✔ Which development store would you like to use to view your project? · 認証テスト

To make your local code accessible to your dev store, you need to use a Shopify-trusted tunneling service called ngrok. To sign up and get an auth token: https://dashboard.ngrok.com/get-started/your-authtoken

✔ Enter your ngrok token. · *************************************************
✅ Success! The tunnel is running and you can now view your app.
```

## アプリインストールフロー

### 未インストールショップの場合

1. ShopifyApp::SessionsController#new (GET "/api/auth")
    - ログイン画面表示
2. ShopifyApp::SessionsController#create (POST "/api/auth")
    - ログイン処理
    - parameter: [authenticity_token, shop_domain, password]
3. ShopifyApp::SessionsController#new (GET "/api/auth")
    - ログイン画面表示
    - shop_domainからDBにsecret_keyが存在するか確認
    - なければ、Shopify管理画面にリダイレクト
4. Shopify側でインストールボタンクリック
5. ShopifyApp::CallbackController#callback (GET "/api/auth/callback?code=[code]&hmac=[hmac]&host=[host]&shop=[shop].myshopify.com&state=[state]&timestamp=1660142869")
    - ref: https://shopify.dev/apps/auth/oauth/getting-started#step-3-confirm-installation
    - ここで、shop_domain, shopify_token, access_scopeをDBに登録

## 参照

- https://shopify.dev/apps/getting-started/create
- https://github.com/Shopify/shopify-app-template-ruby
- https://ngrok.com/
- [yarn run dev](https://shopify.dev/apps/tools/cli/commands#dev)