## 概要

Y!ISUCON を実施します。

以下開催詳細を記載します。

## 参加事項

1チームは1-3人とします。ポータルサイトのチーム登録フォームよりご登録下さい。


## アプリケーションサーバ構成

### OS
CentOS 7.2

### アプリケーション

Ruby, Node.js, Go, PHP, Java によるWebアプリケーションが用意されています。
ただし各々の初期性能は一致していません。どれをベースに用いても構いません。独自で実装してもOKです。

### Nginx
WebサーバとしてデフォルトでNginxが起動しています。
こちらも systemd で管理されています。

### MariaDB
3306番ポートで MariaDB が起動しています。

ユーザーは`root`、パスワードなし。


## アプリケーション起動

初期設定ではNode.js実装のアプリケーションが起動しています。

ブラウザから80番ポートにアクセスして動作確認しましょう。

例として、「アカウント名」は kazuto、 「パスワード」は lbavup を入力することでログインが行えます。

## 言語の切り替え

リファレンス実装として、Node.js, Ruby, Go, PHPが用意されています。

各言語実装は systemd で管理されています。基本的には `systemctl stop/start` ならびに `systemctl enable/disable` で制御します。

### PHPの場合
systemd での切り替えのほか、nginxの設定変更が必要です。`/etc/nginx/php-fpm.conf` が置いてあるのでそれを使用してください。

## ベンチマーク実行
ポータルサイトから実行できます。
チームごとログインし、アプリケーションサーバの設定とベンチマーク実行を行ってください。



## ルール詳細
指定されたサーバー上のアプリケーションのチューニングをおこない、それに対するベンチマークのスコアで競技をおこないます。
そのサーバー1台のみでアプリケーションの動作が可能であればどのような変更を加えても構いません。

ベンチマーカーとブラウザの挙動に差異がある場合、ベンチマーカーの挙動を正とします。また初期実装毎に若干の挙動の違いはありますが、ベンチマーカーに影響のない挙動に関しては仕様とします。

与えられたアプリケーションから、以下の機能は変更してはいけません。

- アクセス先のURI（ポート、およびHTTPリクエストパス）
- レスポンス(HTML)のDOM構造
- JavaScript/CSSファイルの内容


### スコアについて
基本スコアは以下のルールで算出されます。
```
成功レスポンス数(GET) x 1 + 成功レスポンス数(POST) x 2 - (サーバエラー(error)レスポンス数 x 10 + リクエスト失敗(exception)数 x 20 + 遅延POSTレスポンス数 x 100)
```
ただし、基本スコアとベンチマーカーの出すスコアが異なっている場合は、ベンチマーカーの出すスコアが優先されます。

### 減点対象
以下の事項に抵触すると減点対象となります。

- 存在するべきDOM要素がレスポンスHTMLに存在しない
- リクエスト失敗（通信エラー等）が発生する
- サーバエラー(Status 5xx)・クライアントエラー(Status 4xx)をアプリケーションが返す
- 他、ベンチマーカーが検出したケース

### 注意事項

- リダイレクトはリダイレクト先が正しいレスポンスを返せた場合に、1回レスポンスが成功したと判断します
- POSTの失敗は大幅な減点対象です

### 制約事項

以下の事項に抵触すると点数が無効となります。

- GET /initialize へのレスポンスが10秒以内に終わらない
- JavaScript/CSSファイルがアクセスできない
