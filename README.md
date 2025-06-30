# Dify with Ollama

## 前提条件
* OS: macOS / Linux / Windows(WSL2)
* Docker / Docker Compose: インストール済み
* Git: インストール済み

## 構築手順
### 1. リポジトリのクローン

```bash
git clone https://github.com/qa-dx/dify.git
```
---

### 2. 起動

```bash
cd dify/docker

cp .env.example .env
cp docker-compose.override.template.yaml docker-compose.override.yaml

docker compose up -d --build
```

#### 2.1. LLM モデルのダウンロード

```bash
docker compose exec ollama ollama pull gemma3:12b
docker compose exec ollama ollama pull mxbai-embed-large
```

## バックアップ手順
