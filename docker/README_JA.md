## Docker デプロイ用 README

この `docker` ディレクトリは、Docker Compose を使用した Dify のデプロイ用に設計されています。この README では、更新内容、デプロイ手順、および既存ユーザー向けの移行ガイドを紹介します。

### 更新内容

- **Certbot コンテナ**: `docker-compose.yaml` に `certbot` が追加され、SSL 証明書の管理が可能になりました。このコンテナは証明書を自動更新し、HTTPS 接続を安全に保ちます。
  詳細は `docker/certbot/README.md` を参照してください。

- **永続的な環境変数**: 環境変数は `.env` ファイルを通じて管理され、デプロイ間で設定が保持されるようになりました。

  > `.env` とは？ </br> </br>
  > `.env` ファイルは Docker および Docker Compose 環境における重要な構成ファイルであり、コンテナ実行時に利用可能な環境変数を一元管理できます。これにより開発・テスト・本番環境において設定の一貫性と容易な管理が可能となります。

- **統合されたベクターデータベースサービス**: すべてのベクターデータベースサービスは `docker-compose.yaml` で一元的に管理されます。`.env` ファイルで `VECTOR_STORE` を設定することで使用するベクターデータベースを切り替えられます。
- **`.env` ファイルの必須化**: `.env` ファイルが存在しないと `docker compose up` は実行できません。このファイルは設定の中心となり、アップグレード後もカスタム設定を保持するために必要です。

### `docker-compose.yaml` による Dify デプロイ手順

1. **前提条件**: Docker と Docker Compose をインストールしてください。
2. **環境構築**:
    - `docker` ディレクトリに移動します。
    - `.env.example` を `.env` にコピーします（`cp .env.example .env`）。
    - `.env` ファイルを必要に応じて編集します（`.env.example` を参考にしてください）。
3. **サービスの起動**:
    - `docker` ディレクトリで `docker compose up` を実行してサービスを開始します。
    - 使用するベクターデータベースを `.env` の `VECTOR_STORE` に設定します（例: `milvus`, `weaviate`, `opensearch`）。
4. **SSL 証明書の設定**:
    - `docker/certbot/README.md` を参照して Certbot による証明書の設定を行ってください。
5. **OpenTelemetry Collector の設定**:
    - `.env` の `ENABLE_OTEL` を `true` に設定。
    - `OTLP_BASE_ENDPOINT` を正しく設定してください。

### Dify 開発用ミドルウェアのデプロイ方法

1. **ミドルウェア設定**:
    - `docker-compose.middleware.yaml` を使用して、データベースやキャッシュなどのミドルウェアを構成します。
    - `docker` ディレクトリに移動します。
    - `middleware.env.example` を `middleware.env` にコピーします（`cp middleware.env.example middleware.env`）。
2. **ミドルウェアサービスの起動**:
    - `docker` ディレクトリで以下を実行します：
      `docker compose -f docker-compose.middleware.yaml --profile weaviate -p dify up -d`
      ※ weaviate 以外のベクターデータベースを使用する場合は `--profile` を変更してください。

### 既存ユーザー向け移行ガイド

旧 `docker-legacy` セットアップからの移行ユーザー向け:

1. **変更点の確認**: 新しい `.env` 設定と Docker Compose の構成を確認してください。
2. **カスタマイズの移行**:
    - `docker-compose.yaml`、`ssrf_proxy/squid.conf`、`nginx/conf.d/default.conf` などをカスタマイズしていた場合は、それらの設定を `.env` に反映させてください。
3. **データ移行**:
    - データベースやキャッシュなどのサービスに保存されたデータは、必要に応じてバックアップ・移行してください。

### `.env` の概要

#### 主なモジュールとカスタマイズ

- **ベクターデータベースサービス**: `VECTOR_STORE` の種類に応じて、エンドポイントや認証情報などの設定を行います。
- **ストレージサービス**: `STORAGE_TYPE` に応じて、S3、Azure Blob、Google Storage などの設定が可能です。
- **API / Webサービス**: API やフロントエンドの URL 設定などが可能です。

#### 主な環境変数のセクション

1. **共通変数**:
    - `CONSOLE_API_URL`, `SERVICE_API_URL`, `APP_WEB_URL`, `FILES_URL` など。
2. **サーバー構成**:
    - `LOG_LEVEL`, `DEBUG`, `FLASK_DEBUG`, `SECRET_KEY`。
3. **データベース設定**:
    - `DB_USERNAME`, `DB_PASSWORD`, `DB_HOST`, `DB_PORT`, `DB_DATABASE`。
4. **Redis 設定**:
    - `REDIS_HOST`, `REDIS_PORT`, `REDIS_PASSWORD`。
5. **Celery 設定**:
    - `CELERY_BROKER_URL`。
6. **ストレージ設定**:
    - `STORAGE_TYPE`, `S3_BUCKET_NAME`, `AZURE_BLOB_ACCOUNT_NAME`。
7. **ベクターデータベース設定**:
    - `VECTOR_STORE`, `WEAVIATE_ENDPOINT`, `MILVUS_URI` など。
8. **CORS 設定**:
    - `WEB_API_CORS_ALLOW_ORIGINS`, `CONSOLE_CORS_ALLOW_ORIGINS`。
9. **OpenTelemetry 設定**:
    - `ENABLE_OTEL`, `OTLP_BASE_ENDPOINT`。
10. **その他のサービス固有の環境変数**:
    - `nginx`, `redis`, `db`、各種ベクターデータベース固有の変数。

### 補足情報

- **継続的改善フェーズ**: 現在この新しいデプロイ方法は改善中であり、コミュニティからのフィードバックを歓迎します。今後の改善に活かしていきます。
- **サポート**: 詳細な設定については `.env.example` や `docker` ディレクトリの Docker Compose ファイルを参照してください。

この README は、新しい Docker Compose 構成を使用して Dify をデプロイするためのガイドです。問題や質問がある場合は、公式ドキュメントまたはサポートにお問い合わせください。
