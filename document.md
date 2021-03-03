# クイックスタート。コンポーズとWordPress
読書時間の目安。3分

Docker Composeを使って、Dockerコンテナで構築された隔離された環境でWordPressを簡単に実行することができます。このクイックスタートガイドでは、Compose を使って WordPress をセットアップして実行する方法を説明します。始める前に、Compose がインストールされていることを確認してください。

## プロジェクトの定義
1. 空のプロジェクトディレクトリを作成します。

    このディレクトリには、覚えやすい名前をつけてください。このディレクトリはアプリケーションイメージのコンテキストです。このディレクトリには、そのイメージを構築するためのリソースのみを格納してください。

    このプロジェクトディレクトリには docker-compose.yml ファイルが含まれています。

    ```
    ヒント: このファイルの拡張子は .yml か .yaml のどちらかを使うことができます。どちらも有効です。
    ```

1. プロジェクトディレクトリに変更します。

    例えば、ディレクトリ名を my_wordpress としたとします。

    ```
    cd my_wordpress/
    ```

1. WordPress ブログを起動するための docker-compose.yml ファイルと、データ永続化のためのボリュームマウントを持つ別の MySQL インスタンスを作成します。

    ```
    version: '3.3'

    services:
      db:
        image: mysql:5.7
        volumes:
          - db_data:/var/lib/mysql
        restart: always
        environment:
          MYSQL_ROOT_PASSWORD: somewordpress
          MYSQL_DATABASE: wordpress
          MYSQL_USER: wordpress
          MYSQL_PASSWORD: wordpress

      wordpress:
        depends_on:
          - db
        image: wordpress:latest
        ports:
          - "8000:80"
        restart: always
        environment:
          WORDPRESS_DB_HOST: db:3306
          WORDPRESS_DB_USER: wordpress
          WORDPRESS_DB_PASSWORD: wordpress
          WORDPRESS_DB_NAME: wordpress
    volumes:
        db_data: {}
    ```

    ```
    注意事項。

    注意: docker ボリューム db_data は、WordPress が行った更新をデータベースに永続化します。docker ボリュームについての詳細はこちら

    WordPress Multisiteはポート80と443でのみ動作します。
    ```

## プロジェクトを構築する
次に、プロジェクトディレクトリから `docker-compose up -d` を実行します。

これにより、`docker-compose up`がデタッチドモードで実行され、必要なDockerイメージがプルされ、以下の例のようにwordpressとデータベースコンテナが起動します。

```
$ docker-compose up -d
Creating network "my_wordpress_default" with the default driver
Pulling db (mysql:5.7)...
5.7: Pulling from library/mysql
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
...
Digest: sha256:34a0aca88e85f2efa5edff1cea77cf5d3147ad93545dbec99cfe705b03c520de
Status: Downloaded newer image for mysql:5.7
Pulling wordpress (wordpress:latest)...
latest: Pulling from library/wordpress
efd26ecc9548: Already exists
a3ed95caeb02: Pull complete
589a9d9a7c64: Pull complete
...
Digest: sha256:ed28506ae44d5def89075fd5c01456610cd6c64006addfe5210b8c675881aff6
Status: Downloaded newer image for wordpress:latest
Creating my_wordpress_db_1
Creating my_wordpress_wordpress_1
```

```
注: WordPress Multisite はポート 80 および/または 443 でのみ動作します。0.0.0.0.0 をポート 80 または 443 にバインドすることについてエラーメッセージが表示された場合（どちらを指定したかによって異なります）、WordPress に設定したポートが既に他のサービスで使用されている可能性があります。
```

## Web ブラウザで WordPress を起動する
この時点で、WordPressはDocker Hostの8000番ポートで動作しているはずなので、WordPress管理者として「有名な5分間のインストール」を完了させることができます。

```
注意：コンテナがまだ初期化中であり、最初のロードまでに数分かかる場合があるため、8000番ポートですぐにWordPressサイトが利用できるわけではありません。
```

Docker Machineを使っている場合は、docker-machine ip MACHINE_VMというコマンドを実行してマシンのアドレスを取得し、Webブラウザで http://MACHINE_VM_IP:8000 を開いてください。

Docker Desktop for MacやDocker Desktop for Windowsを使用している場合は、http://localhost をIPアドレスとして実行し、Webブラウザでhttp://localhost:8000 を開きます。

![language](wordpress-lang.png)
![welcome](wordpress-welcome.png)

## シャットダウンとクリーンアップ
`docker-compose down` コマンドは、コンテナとデフォルトのネットワークを削除しますが、WordPress データベースは保存されます。

`docker-compose down --volumes` コマンドは、コンテナ、デフォルトのネットワーク、WordPress データベースを削除します。

www.DeepL.com/Translator（無料版）で翻訳しました。