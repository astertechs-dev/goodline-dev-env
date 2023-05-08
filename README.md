# Goodline dev-env

![Landing Page](https://user-images.githubusercontent.com/43859895/141092846-905eae39-0169-4fd7-911f-9ff32c48b7e8.png)

Docker Composeを使って構築された基本的なLAMPスタック環境です。 <br/>
構成は以下のとおりです。

- PHP
- Apache
- MySQL
- phpMyAdmin
- Redis

現在、PHPについて、以下のバージョンを用意しています。 <br/>
必要に応じて適切なバージョンを使用してください。

- 5.4.x
- 5.6.x
- 7.1.x
- 7.2.x
- 7.3.x
- 7.4.x
- 8.0.x
- 8.1.x

## インストールについて

```shell
# リポジトリをクローンする
git clone git@github.com:astertechs-dev/goodline-dev-env.git
cd goodline-dev-env/
# sample.env を必要に応じて編集する
cp sample.env .env
# docker-compose で環境を構築する
docker-compose up -d
```

アプリケーションが起動しているかを確認するために、以下のURLにアクセスしてください。 
`http://localhost:8090`.

## 設定について

### 一般情報
このDocker Stackはローカル開発用にビルドされたもので、本番用ではありません。
本番環境として利用するためには、最低限以下の項目を設定する必要があります。

- PHPハンドラー: mod_php => php-fpm
- 適切なソースIP制限で、MySQLユーザーを保護する。

### 構成

本パッケージには、デフォルトの設定オプションが付属しています。これらの設定は、ルートディレクトリに `.env` ファイルを作成することで変更することができます。
ここでは、`sample.env`ファイルの内容をコピーし、必要な環境変数の値を更新することで簡単に変更できます。

### 設定変数

以下の設定変数が用意されており、自分の `.env` ファイルに上書きすることでカスタマイズすることができます。

---

#### PHP

---

- _**PHPVERSION**_ <br/>
使用するPHPのバージョンを指定するために使用します。デフォルトは常に最新のPHPバージョンです。

- _**PHP_INI**_  <br/>
必要に応じて、`php.ini` ファイルを独自に定義してください。

---
#### Apache

---

- _**DOCUMENT_ROOT**_<br/>
Apacheサーバーのドキュメントルートである。<br/>
デフォルトは `./www` です。すべてのサイトがここに置かれ、自動的に同期されます。

- _**APACHE_DOCUMENT_ROOT**_<br/>
Apacheの設定ファイルの値です。初期値は /var/www/html です。

- _**VHOSTS_DIR**_<br/>
デフォルトは `./config/vhosts` です。<br/>
バーチャルホストの conf ファイルはここに置くことができます。

> 各バーチャルホストのために、システムの `hosts` ファイルにエントリを追加することを確認してください。

- _**APACHE_LOG_DIR**_<br/>
Apacheのログ保存用ディレクトリ。デフォルト値は `./logs/apache2` です。

---

#### Database

---

_**DATABASE**_<br/>

使用するMySQLまたはMariaDBバージョンを定義します。

- _**MYSQL_INITDB_DIR**_<br/>
コンテナを初めて起動したときに、このディレクトリにある拡張子 `.sh`、`.sql`、`.sql.gz`、`.sql.gz` のファイルが表示されます。<br/>
`.sql.xz` はアルファベット順で実行されます。ファイル実行権限のない `.sh` ファイルは実行されず、ソースされる。
デフォルト値は `./config/initdb` です。

- _**MYSQL_DATA_DIR**_<br/>
MySQLのデータディレクトリです。<br/>
デフォルト値は `./data/mysql` です。<br/>
すべてのMySQLデータファイルはここに保存されます。

- _**MYSQL_LOG_DIR**_<br/>
ログ保存用ディレクトリ。デフォルト値は `./logs/mysql` です。

## Web Server

Apacheは80番ポートで動作するように設定されています。<br/>
そのため、`http://localhost`経由でアクセスすることができます。

#### Apache Modules

デフォルトでは、以下のモジュールが有効になっています。

- rewrite
- headers

> `./bin/phpX/Dockerfile`を更新するだけで、より多くのモジュールを有効にすることができます。また、PRを作成し、汎用性が高そうならマージすることもできます。
> Docker-compose build`を実行してDockerイメージを再構築し、Dockerコンテナを再起動する必要があります。

#### SSH経由で接続する

docker-compose の `exec` コマンドでWebサーバに接続し、様々な処理を行うことができます。<br/>
SSHでコンテナにログインする場合は、以下のコマンドを使用します。

```shell
docker-compose exec webserver bash
```

## PHP

インストールされるphpのバージョンは、`.env` ファイルに依存します。

#### Extensions

デフォルトでは、以下の拡張機能がインストールされています。<br/>
PHPバージョン<7.x.x>の場合は異なる場合があります。

- mysqli
- pdo_sqlite
- pdo_mysql
- mbstring
- zip
- intl
- mcrypt
- curl
- json
- iconv
- xml
- xmlrpc
- gd

> ./bin/webserver/Dockerfile`を更新することで、より多くの拡張機能をインストールすることができます。また、PRを作成し、汎用性が高そうであればマージすることも可能です。
> Docker-compose build` を実行して docker イメージを再構築し、Docker コンテナを再起動する必要があります。

## Xdebug

Xdebugはデフォルトでインストールされ、そのバージョンは `.env` ファイルで選択したPHPバージョンに依存します。

**【Xdebug versions】** <br/>

PHP <= 7.3: Xdebug 2.X.X

PHP >= 7.4: Xdebug 3.X.X

Xdebugを使用するには、`./config/php/php.ini` ファイルで、選択したバージョンのPHPに応じた設定を有効にする必要があります。

【例】

```
# Xdebug 2
#xdebug.remote_enable=1
#xdebug.remote_autostart=1
#xdebug.remote_connect_back=1
#xdebug.remote_host = host.docker.internal
#xdebug.remote_port=9000

# Xdebug 3
#xdebug.mode=debug
#xdebug.start_with_request=yes
#xdebug.client_host=host.docker.internal
#xdebug.client_port=9003
#xdebug.idekey=VSCODE
```

**【Xdebug VS Code】**<br/>
Xdebugの拡張機能「PHP Debug」をインストールする必要があります。<br/>
インストール後、Debugに移動して起動ファイルを作成し、IDEがリッスンして正しく動作するようにします。

【例】

**【重要】** <br/>
`pathMappings`は、VS Codeでフォルダをどのように開いたかに依存します。<br/>
各フォルダには独自の起動設定があり、`.vscode/launch.json` で確認することができます。

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Listen for Xdebug",
      "type": "php",
      "request": "launch",
      // "port": 9000, // Xdebug 2
      "port": 9003, // Xdebug 3
      "pathMappings": {
        "/var/www/html": "${workspaceFolder}" // if you have opened VSCODE in ./www folder
      }
    }
  ]
}
```

Now, make a breakpoint and run debug.

**Tip!** After theses configurations, you may need to restart container.

## Redis

Redisが付属しています。デフォルトのポート `6379` で実行されます。

## SSL (HTTPS)

https ドメインのサポートは内蔵されていますが、デフォルトでは無効になっています。<br/>
SSL を有効化し設定する方法は 3 つあり、最も簡単なのは `localhost` での https` です。<br/>
本番環境に近いテスト環境の再現であれば、より多くの設定を行えば、どのようなドメイン名でもサポートできます。

**【注意】** <br/>
ローカルホスト以外のドメイン名で、https を使用したい場合は、コンピュータの [hosts file](https://en.wikipedia.org/wiki/Hosts_%28file%29) を変更し、ドメイン名を `127.0.0.1` に向ける必要があります。この作業を怠ると、SSLは機能せず、ローカルでそのドメイン名にアクセスしようとするたびに、インターネットにルーティングされることになります。



### ローカル環境でHTTPSを利用する場合

localhost で `https` を有効にするためには、次のことが必要です。

1. [mkcert](https://github.com/FiloSottile/mkcert#installation) のようなツールを使用して、`localhost` の SSL 証明書を作成します：
   - 端末で以下のコマンドを実行します。
   ```shell
   mkcert localhost 127.0.0.1 ::1
   ```
   - 生成されたファイルをそれぞれ `cert.pem` と `cert-key.pem` に名称変更します。
   - これらのファイルを `config/ssl` ディレクトリに配置し、Docker セットアップに移動します。
2. `config/vhosts/default.conf` の `443` vhost のコメントを解除します。

これで、LAMPコンテナの電源を入れると、いつでも `https` が `localhost` で動作するようになります。


### 1枚の証明書から複数のドメインでHTTPSを利用する場合

ローカルテストに通常のドメイン名を使用し、かつhttpsのサポートが必要な場合、最も単純な解決策は、すべてのドメイン名をカバーするSSL証明書を使用することです：

1. [mkcert](https://github.com/FiloSottile/mkcert#installation) のようなツールを使って、必要なドメイン名をすべてカバーするSSL証明書を作成します：
   - 端末で以下のコマンドを実行し、ドメイン名とIPアドレスをすべてサポートしたいものに置き換えます。 
     ```shell
     mkcert example.com "*.example.org" myapp.dev localhost 127.0.0.1 ::1
     ```
   - 生成されたファイルをそれぞれ `cert.pem` と `cert-key.pem` にリネームします。
   - これらのファイルを `config/ssl` ディレクトリに配置し、Docker セットアップに移動します。
2. `config/vhosts/default.conf` の `443` vhost のコメントを解除します。

すべてのドメイン名を1つの証明書にまとめたので、vhostファイルはそれ以上修正する必要がなく、あなたのセットアップをサポートします。<br/>
しかし、必要であれば、ドメイン固有のルールを追加することができます。<br/>
これで、LAMPコンテナを起動すると、指定したすべてのドメインで「https」が動作するようになります。


### 複数の証明書を持つドメインで HTTPS を利用する場合

もし、ローカルのテスト環境を本番環境と完全に一致させ、かつ、「https」のサポートが必要な場合、サポートしたいドメインごとにSSL証明書を作成することができます：

1. [mkcert](https://github.com/FiloSottile/mkcert#installation) のようなツールを使用して、必要なドメイン名をカバーする SSL 証明書を作成します：
   - 端末で`mkcert [your-domain-name(s)-here]` を実行し、括弧の部分を自分のドメイン名に置き換えます。
   - 生成されたファイルを `[name]-cert.pem` や `[name]-cert-key.pem` のように、括弧の部分をユニークな名前に変更する。
   - これらのファイルを `config/ssl` ディレクトリに置き、Docker セットアップに移動します。
2. vhostファイル（`config/vhosts/default.conf`）の`443`の例を使って、ドメイン名と証明書ファイル名に一致する新しいルールを作成します。

LAMPコンテナは、起動時に`config/ssl`にあるSSL証明書を自動的に引き込みます。<br/>
vhostsファイルを正しく設定し、SSL証明書を `config/ssl` に配置していれば、LAMPコンテナの電源を入れたときに、指定したドメインでいつでも `https` が機能するようになります。

