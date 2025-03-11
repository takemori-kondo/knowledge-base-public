# service-03. OpenAI
________________________________________
OpenAI API リファレンス  
https://platform.openai.com/docs/api-reference/introduction
________________________________________
## 1. サンプル
________________________________________
### 1.1. APIキー用意

補足：OpenAI APIは、playground、dashboard(これの基準URLはない)、settingsの3つのページ群に分かれてる

1. playgroundにアクセスしてプロジェクト作成
    - https://platform.openai.com/playground/
    - 新規プロジェクト作成
2. settingsにアクセスして課金
    - https://platform.openai.com/settings/
    - BillingからOpen AI APIを課金
3. playgroundに戻り、作成したプロジェクトのダッシュボードからAPIキー取得
    - API Keysから作成

________________________________________
### 1.2. お試し

chat/completions API

```bash
OPENAI_API_KEY=...
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "gpt-4o",
    "messages": [
      {
        "role": "system",
        "content": "日本の歴史について質問いいですか？"
      },
      {
        "role": "user",
        "content": "Hello!"
      }
    ]
  }'
```

________________________________________
### 1.3. ダッシュボードについて

組織のオーナーなら、既定でダッシュボードも閲覧可能

https://platform.openai.com/assistants

1. プロジェクトに紐づいたリソース全てを閲覧可能
2. デフォルトページは一番上にあるAssistantsになっている
3. 概ね、APIでできることの大半はブラウザから操作可能

________________________________________
### 2. Assistants API (filesearch)

概要

- 自然言語のテキストファイルの内容を読み取り回答を行うチャットボットを作成可能

要約

1. VectorStoreを新規作成
2. VectorStoreへテキストファイルをアップロード・アタッチ
    - 現状、UIでは既にアップロードしたファイルの再アタッチはサポートされていない(APIはある)
3. アシスタントを作成し、fileSearchをOnにしてVectorStoreを指定
    - IDを指定すれば紐づけることが可能

補足

- ダッシュボードにアクセスできるなら、ブラウザからアシスタントの用意が可能
- 同様の手順をOPENAI_API_KEYを使用してcurlで行うことも可能

________________________________________
### 3. Fine tuning

概要

- 本格的に特化させた生成modelを作りたい場合の機能。やや扱いにくい

注意点

- 2024年9月現在、プロジェクトに紐づく形で作られたモデルは削除手段が存在しない