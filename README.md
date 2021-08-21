# Firebase Authenticationでログイン機能を追加

## 前提

* 有効なGoogleアカウントを持っている
* Googleアカウントで [Firebase コンソール](https://firebase.google.com/firebase/console) にログインできる

## Firebaseコンソールでの作業

Firebaseコンソールにログインしておく。

### プロジェクト作成

ここではFirebaseを利用するにあたって最初に作成するプロジェクトの作成方法を説明する。

1. トップで **プロジェクトを追加** をクリック

1. 名前をつけて **続行** クリック

1. サンプルなので **Googleアナリティクス** は無効にして **プロジェクト作成** クリック

### アプリの追加

ここではFirebaseアプリを作成するための準備として、アプリの追加方法を説明する。

#### ウェブ

1. プロジェクトトップページで、ウェブの追加をクリック

1. **アプリのニックネーム** をつける

1. **このアプリのFirebase Hostingも設定します。** はオフにしておく

1. **アプリを登録** をクリック

1. **コンソールへ進む** をクリック

## 開発環境準備

ここではローカルでの開発環境を作成して、デプロイする方法を説明する。

### 開発環境を初期化

**firebasesandbox** を使ってFirebaseの開発する準備を行う。

1. Visual Studio Codeを使う

1. セットアップは[getting started steps](https://aka.ms/vscode-remote/containers/getting-started)を参照

1. **firebasesandbox** を開く

1. <kbd>F1</kbd> を押してコマンドパレットを表示して、 **Remote-Containers: Reopen Folder in Container** を実行

1. Firebaseへプロジェクトを作成したGoogleアカウントでログインする

   ```bash
   firebase login
   ```

1. 初期化する

   ```bash
   firebase init
   ```

   1. Hosting: Configure files for Firebase Hosting and (optionally) set up GitHub Action deploys

   1. Use an existing project

   1. 対象のプロジェクトを選択する

   1. 残りはデフォルト設定

1. デプロイして確認する

   ```bash
   firebase deploy
   ```

   デプロイが成功すると、**Hosting URL** が表示されるのでブラウザで確認する

## Firebase Authentication

Firebase Authenticationを使ったログイン機能を追加する。

### メールアドレスを使った認証

#### ページを追加

ログイン画面と、ログイン成功したら表示する画面を以下の手順で作成する。

1. index.htmlをコピーして、success.htmlを作成

1. index.htmlをログイン、ログイン成功後のページをsuccess.html

1. index.htmlの方はscriptタグに指定してある **difer** を削除する

1. 不要なJavaScriptファイルのロードを無くす

```JavaScript
  <script src="/__/firebase/8.10.0/firebase-database.js"></script>
  <script src="/__/firebase/8.10.0/firebase-firestore.js"></script>
  <script src="/__/firebase/8.10.0/firebase-functions.js"></script>
  <script src="/__/firebase/8.10.0/firebase-messaging.js"></script>
  <script src="/__/firebase/8.10.0/firebase-storage.js"></script>
  <script src="/__/firebase/8.10.0/firebase-analytics.js"></script>
  <script src="/__/firebase/8.10.0/firebase-remote-config.js"></script>
  <script src="/__/firebase/8.10.0/firebase-performance.js"></script>
```

#### Firebaseを使う準備

プロジェクトページの **プロジェクトの設定** から、**firebaseConfig** をコピーする

今回はJava Scriptで実装するので、CDNを指定する

コピーする対象はfirebase-app.jsもあるが、index.htmlにはすでにあるので、ここはコピーしない

index.htmlからbody部にあるscriptを削除する

同じ場所にコピーしたscriptを貼り付ける

デプロイして、JavaScriptエラーがないことをコンソールで確認する

#### FirebaseUIでログイン機能追加

FirebaseUIでログイン機能を追加する手順を説明する。

1. FirebaseUIをindex.htmlにロードする

   1. 公式ページからCDNをコピーする
   1. index.htmlのhead部に貼り付ける

1. ログイン用のコンポーネントを貼り付ける

   ```html
   <div id="message">
      <h2>Hello, Firebase Hosting!</h2>
      <h1>Login</h1>
      <div id="firebaseui-auth-container"></div>
      <div id="loader">Loading...</div>
   </div>
   ```

1. ログインするためのコードをFirebaseの初期化の後に貼り付ける

   ```JavaScript
   var ui = new firebaseui.auth.AuthUI(firebase.auth());

   var uiConfig = {
   callbacks: {
      signInSuccessWithAuthResult: function (authResult, redirectUrl) {
         return true;
      },
      uiShown: function () {
         document.getElementById('loader').style.display = 'none';
      }
   },
   signInFlow: 'popup',
   signInSuccessUrl: './success.html',
   signInOptions: [
      firebase.auth.EmailAuthProvider.PROVIDER_ID,
   ],
   };
   ui.start('#firebaseui-auth-container', uiConfig);
   ```

1. デプロイしてログインページが表示されることを確認する

詳細は公式ページを参考にする。

[FirebaseUI でセブアプリに簡単にログイン機能を追加する](https://firebase.google.com/docs/auth/web/firebaseui?hl=ja)

#### ログインできるユーザを追加する

Firebaseコンソールからユーザを追加する手順を説明する。

コンソールのauthenticationをクリック

ユーザ追加
