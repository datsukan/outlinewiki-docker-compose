# Outline wiki docker-compose

It is a setting for server construction of [Outline] (https://github.com/outline/outline) which is an OSS Wiki application.  
It is based on [chsasank / outline-wiki-docker-compose] (https://github.com/chsasank/outline-wiki-docker-compose).  
Start the application (outline) / DB (PostgreSQL, Redis) with Docker and docker-compose, create a separate AWS S3 bucket for storage, and prepare and set an SMTP server separately.  
Authentication is done by OAuth of Slack, Google, or AZURE.  
Since the certificate settings etc. are separated, it is assumed that the reverse proxy of the Web server application (Nginx etc.) will communicate with `http: localhost: 3000` after starting the Docker container locally or in the server.  
When using a certificate, it is recommended to set "Let's Encrypt" etc. on the Web server application side.

OSSのWikiアプリケーションである[Outline](https://github.com/outline/outline)のサーバー構築用設定です。  
[chsasank/outline-wiki-docker-compose](https://github.com/chsasank/outline-wiki-docker-compose)をベースにしています。  
Dockerおよびdocker-composeでアプリ（outline）・DB（PostgreSQL、Redis）を起動、ストレージはAWS S3のバケットを別途作成、SMTPサーバーを別途用意して設定します。  
認証はSlack・Google・AZUREいずれかのOAuthで行います。  
証明書設定などは切り離しているので、ローカルもしくはサーバー内でDockerコンテナ起動後に`http:localhost:3000`に対してWebサーバーアプリケーション（Nginxなど）のリバースプロキシで通信させることを想定しています。  
証明書を使用する場合はWebサーバーアプリケーション側で「Let’s Encrypt」などを設定することをおすすめします。  

# Requirement \ 前提要件

- [Docker](https://www.docker.com/get-started)
- [docker-compose](https://docs.docker.jp/compose/install.html)
- [AWS S3](https://aws.amazon.com/jp/s3/)
- SMTP Server（[AWS SES](https://aws.amazon.com/jp/ses/)など）

# Installation \ 導入

1. Get the source / ソースを取得する
```bash
git clone https://github.com/datsukan/outlinewiki-docker-compose.git
cd outlinewiki-docker-compose
```

2. Create an environment variable file / 環境変数ファイルを作成する
```bash
cp .env.example .env
```

3. Describe the setting value in the environment variable file / 環境変数ファイルに設定値を記述する  
   ※All credentials listed below are dummy / 下記で記載がある認証情報はすべてダミーです
   1. Set up time zone / タイムゾーンを設定する
    ```
    TZ="Asia/Tokyo"
    ```

   2. Set up standard language / 標準言語を設定する
    ```
    DEFAULT_LANGUAGE=ja_JP
    ```

   3. Set up URL / URLを設定する  
    Replace with any value. / 任意の値で置き換えてください。
    ```
    URL=https://exanmple.com
    CDN_URL=https://exanmple.com
    ```

   4. Set up secret key / シークレットキーを設定する  
    Replace with the value resulting from running `openssl rand -hex 32`. / `openssl rand -hex 32`を実行した結果の値で置き換えてください。
    ```
    SECRET_KEY=8a45ae9a0278486ff8188ee291e8092add450f0bd8abc709304277f7079b58ca
    UTILS_SECRET=bae98cb9299f870bcc92606abc7e9e26a2e219bab30fbd57c7a1714f5b80db0c
    ```

   5. Set up DB / DBの設定を行う  
    Replace with any value. / 任意の値で置き換えてください。
    ```
    STGRES_USER=postgres
    POSTGRES_PASSWORD=password
    POSTGRES_DB=outline
    POSTGRES_INITDB_ARGS="--encoding=UTF-8"
    DATABASE_URL=postgres://postgres:password@outline-postgres:5432/outline
    DATABASE_URL_TEST=postgres://postgres:password@outline-postgres:5432/outline-test
    ```

   6. Set up storage (S3) / ストレージ（S3）の設定を行う  
    Replace with any value. / 任意の値で置き換えてください。
    ```
    AWS_ACCESS_KEY_ID=A123456BCDE7FG890HIJ
    AWS_SECRET_ACCESS_KEY=ABC56gh/aBcDeFgHiJkLmNoPqRsTuVwXyZ
    AWS_REGION=ap-northeast-1
    AWS_S3_UPLOAD_BUCKET_URL=https://example-bucket.s3.ap-northeast-1.amazonaws.com
    AWS_S3_UPLOAD_BUCKET_NAME=example-bucket
    AWS_S3_UPLOAD_MAX_SIZE=26214400
    AWS_S3_FORCE_PATH_STYLE=false
    AWS_S3_ACL=private
    ```

   7. Set up OAuth / OAuthの設定を行う  
    You can specify how to authenticate. / 認証を行う方法を指定できます。  
    Only one can be used. / どれか一つだけ使用できます。
      1. For Slack / Slackの場合  
        Replace with any value. / 任意の値で置き換えてください。
        ```
        SLACK_KEY=1234567890.1234567890
        SLACK_SECRET=abcd1234efgh5678ijkl9012mnop
        ```

      2. For Google / Googleの場合  
        Replace with any value. / 任意の値で置き換えてください。
        ```
        GOOGLE_CLIENT_ID=1234567890-abcd1234efgh5678ijkl9012mnop.apps.googleusercontent.com
        GOOGLE_CLIENT_SECRET=aBcDeFgHiJkLmNoPqRsTuVwXyZ
        ```

      3. For Azure / Azureの場合  
        Replace with any value. / 任意の値で置き換えてください。
        ```
        AZURE_CLIENT_ID=
        AZURE_CLIENT_SECRET=
        AZURE_RESOURCE_APP_ID=
        ```

   8. Set up Slack integration (optional) / Slackの統合設定を行う（任意）  
    Replace with any value. / 任意の値で置き換えてください。  
    ※reference / 参考：https://wiki.generaloutline.com/share/be25efd1-b3ef-4450-b8e5-c4a4fc11e02a
    ```
    SLACK_VERIFICATION_TOKEN=abcd1234efgh5678ijkl9012mnop
    SLACK_APP_ID=A12345BCDE6F
    SLACK_MESSAGE_ACTIONS=true
    ```

   9. Set up Google Analytics (optional) / GoogleAnalyticsの設定を行う（任意）  
    ```
    GOOGLE_ANALYTICS_ID=
    ```

   10. Set up SMTP server / SMTPサーバーを設定する
    ```
    SMTP_HOST=email-smtp.ap-northeast-1.amazonaws.com
    SMTP_PORT=465
    SMTP_USERNAME=A123456BCDE7FG890HIJ
    SMTP_PASSWORD=ABC56gh/aBcDeFgHiJkLmNoPqRsTuVwXyZ
    SMTP_FROM_EMAIL=no-reply@example.com
    SMTP_REPLY_EMAIL=support@example.com
    ```

# Usage \ 使用方法

## Start \ 起動

```bash
docker-compose up -d
```

## Stop \ 停止

```bash
docker-compose stop
```

## Upgrading \ アップグレード

1. Stop & delete the container and display the list of images / コンテナを停止＆削除して、イメージの一覧を表示する
```bash
docker-compose down
docker images
```

2. Of the displayed images, specify the ID of `outlinewiki / outline` and delete it. / 表示されたイメージのうち`outlinewiki/outline`のIDを指定して削除する
```bash
$ docker images
REPOSITORY            TAG       IMAGE ID       CREATED        SIZE
outlinewiki/outline   latest    88b1c27b7917   3 weeks ago    471MB
minio/minio           latest    9c8125b8f022   3 months ago   264MB
postgres              latest    293e4ed402ba   3 months ago   315MB
redis                 latest    bc8d70f9ef6c   3 months ago   105MB
postgres              13.2      82b8b88e26bc   3 months ago   314MB
nginx                 latest    f0b8a9a54136   3 months ago   133MB
```

```bash
docker rmi 88b1c27b7917
```

3. Create and launch image and container / イメージとコンテナを作成して起動する

```bash
docker-compose up -d
```

# Note \ 注意事項

Please use at your own risk.  
If you have any problems please share them in the issue.

使用は自己責任でお願いします。  
問題があればissueで共有してください。

# Author \ 著者

神達 小楠 (Shonan Kandatsu)
