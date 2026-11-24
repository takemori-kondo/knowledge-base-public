# python-02 FastAPI
________________________________________
## 1. 推奨の開発環境構築
________________________________________
手順

1. vm-04. WSL and Docker に従って、WSLとWSL内Dockerのインストールまで済ませる
2. マウントは/mnt/dなど、既定のマウント経由でOK

補足

- Docker内で作業するので、venvは不要

uvicorn is 何？

- python版のTomcatのようなもの。直接Http受けれる。開発時は単コンテナで済むし、本番でも直接プロキシなどの後ろに置ける

________________________________________
## 2. プロジェクトフォルダの構築
________________________________________
フォルダ構成

```text
FastAPISample/
├─ compose.yaml
├─ .vscode/settings.json
├─ app/
│  └─ main.py
└─ docker/
    └─ api/
        ├─ Dockerfile
        └─ requirements.txt
```

※ 補足

- compose.yaml以外のDocker関連ファイル（環境構築でしか使わないファイル）はdocker/以下に置く
    - requirements.txt (pip installを初回/このファイル更新時にだけ行う)
- ビルドのコンテキストはプロジェクトルートのまま
    - appとdocker/api/requirements.txtの両方をCOPYするため
- /app と /.vscode を個別にマウントする
    - プロジェクトルートを直接マウントすると余計なものまでマウントしてしまうので、必要なフォルダだけマウント
    - /scriptsなども本番環境で使うならマウント

compose.yaml

```yaml
services:
  api:
    build:
      context: .
      dockerfile: docker/api/Dockerfile
    ports:
      - "8000:8000"
    volumes:
      - ./app:/code/app
      - ./.vscode:/code/.vscode
    environment:
      WATCHFILES_FORCE_POLLING: "true"
    command: fastapi dev app/main.py --host 0.0.0.0 --port 8000
```

app/main.py

```py
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root() -> dict[str, str]:
    return {"message": "Hello World. Yeah!"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: str | None = None) -> dict[str, int | str | None]:
    return {"item_id": item_id, "q": q}
```

docker/api/Dockerfile

```text
FROM python:3.14-slim

WORKDIR /code

COPY docker/api/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app ./app

CMD ["fastapi", "run", "app/main.py", "--port", "8000"]
```

docker/api/requirements.txt

```text
fastapi[standard]
```

________________________________________
## 3. 立ち上げ
________________________________________
立ち上げ

```bash
cd /mnt/d/Dropbox/repos/FastAPISample
docker compose up -d --build
docker compose logs -f
```

確認

- ブラウザから以下にアクセス
    - http://localhost:8000
    - http://localhost:8000/docs
    - http://localhost:8000/items/1
    - http://localhost:8000/items/abc
        - これはバリデーションエラーになる

________________________________________
## 4. VSCode対応
________________________________________
### 4.1. Windowsからコンテナのアタッチ

1. 拡張の追加
    - WSL (Microsoft)
    - Dev Containers (Microsoft)
2. 共通設定側で、Execute in WSL を チェック
    - 設定画面で「Execute in WSL」で検索 してチェック
3. コンテナへアタッチ
    - Ctrl + Shift + P
    - Dev Containers: Attach to Running Container...
        - コンテナ名を選択
    - コンテナにアタッチされたVSCodeが開く(元のは閉じてOK)
4. あとはDockerfile の WORKDIRのパス を開けばOK
    - /code 

________________________________________
### 4.2. コンテナ内の拡張の整備

- Python (Python Debugger、Python Environments、Pylanceも入る)
- Ruff

プロジェクトの.vscode/settings.json

```json
{
    "python.analysis.typeCheckingMode": "strict",
    "ruff.lint.ignore": ["EXE002"]
}
```

※ 何を有効にしたか自体はWindows側に情報が残るので、コンテナを立て直しても再度自動でインストール済みになる