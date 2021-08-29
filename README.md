# Outline wiki docker-compose

OSSのWikiアプリケーションである[Outline](https://github.com/outline/outline)のサーバー構築用設定です。  
[chsasank/outline-wiki-docker-compose](https://github.com/chsasank/outline-wiki-docker-compose)をベースにしています。  
Dockerおよびdocker-composeでアプリ（outline）・DB（PostgreSQL、Redis）を起動、ストレージはAWS S3のバケットを別途作成、SMTPサーバーを別途用意して設定します。  
認証はSlack・Google・AZUREいずれかのOAuthで行います。  

# Requirement \ 前提要件

- [Docker](https://www.docker.com/get-started)
- [docker-compose](https://docs.docker.jp/compose/install.html)
- [AWS S3](https://aws.amazon.com/jp/s3/)
- SMTPサーバー（[AWS SES](https://aws.amazon.com/jp/ses/)など）

# Installation \ 導入

1. ソースを取得する
```bash
git clone https://github.com/datsukan/outlinewiki-docker-compose.git
cd outlinewiki-docker-compose
```

2. 環境変数ファイルを作成する
```bash
cp .env.example .env
```

3. 環境変数ファイルに設定値を記述する  
   ※下記で記載がある認証情報はすべてダミーです
   1. タイムゾーンを設定する
    ```
    TZ="Asia/Tokyo"
    ```

   2. 標準言語を設定する
    ```
    DEFAULT_LANGUAGE=ja_JP
    ```

   3. URLを設定する  
    任意の値で置き換えてください。
    ```
    URL=https://exanmple.com
    CDN_URL=https://exanmple.com
    ```

   4. シークレットキーを設定する  
    `openssl rand -hex 32`を実行した結果の値で置き換えてください。
    ```
    SECRET_KEY=8a45ae9a0278486ff8188ee291e8092add450f0bd8abc709304277f7079b58ca
    UTILS_SECRET=bae98cb9299f870bcc92606abc7e9e26a2e219bab30fbd57c7a1714f5b80db0c
    ```

   5. DBの設定を行う  
    任意の値で置き換えてください。
    ```
    STGRES_USER=postgres
    POSTGRES_PASSWORD=password
    POSTGRES_DB=outline
    POSTGRES_INITDB_ARGS="--encoding=UTF-8"
    DATABASE_URL=postgres://postgres:password@outline-postgres:5432/outline
    DATABASE_URL_TEST=postgres://postgres:password@outline-postgres:5432/outline-test
    ```

   6. ストレージ（S3）の設定を行う  
    任意の値で置き換えてください。
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

   7. OAuthの設定を行う  
    認証を行う方法を指定できます。  
    どれか一つだけ使用できます。
      1. Slackの場合  
        任意の値で置き換えてください。
        ```
        SLACK_KEY=1234567890.1234567890
        SLACK_SECRET=abcd1234efgh5678ijkl9012mnop
        ```

      2. Googleの場合  
        任意の値で置き換えてください。
        ```
        GOOGLE_CLIENT_ID=1234567890-abcd1234efgh5678ijkl9012mnop.apps.googleusercontent.com
        GOOGLE_CLIENT_SECRET=aBcDeFgHiJkLmNoPqRsTuVwXyZ
        ```

      3. Azureの場合  
        任意の値で置き換えてください。
        ```
        AZURE_CLIENT_ID=
        AZURE_CLIENT_SECRET=
        AZURE_RESOURCE_APP_ID=
        ```

   8. Slackの統合設定を行う（任意）  
    任意の値で置き換えてください。  
    ※参考：https://wiki.generaloutline.com/share/be25efd1-b3ef-4450-b8e5-c4a4fc11e02a
    ```
    SLACK_VERIFICATION_TOKEN=abcd1234efgh5678ijkl9012mnop
    SLACK_APP_ID=A12345BCDE6F
    SLACK_MESSAGE_ACTIONS=true
    ```

   9. GoogleAnalyticsの設定を行う（任意）  
    ```
    GOOGLE_ANALYTICS_ID=
    ```

   10. SMTPサーバーを設定する
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

1. コンテナを停止＆削除して、イメージの一覧を表示する
```bash
docker-compose down
docker images
```

2. 表示されたイメージのうち`outlinewiki/outline`のIDを指定して削除する
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

3. イメージとコンテナを作成して起動

```bash
docker-compose up -d
```

# Note \ 注意事項

使用は自己責任でお願いします。  
問題があればissueで共有してください。

# Author \ 著者

神達 小楠 (Shonan Kandatsu)
