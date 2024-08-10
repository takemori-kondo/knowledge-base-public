# service-03. OpenAI
________________________________________
OpenAI API リファレンス  
https://platform.openai.com/docs/api-reference/introduction
________________________________________
## 1. サンプル
________________________________________
### 1.1. APIキー用意

補足：OpenAI APIは、playgroundとsettingsの2つのページ群に分かれてる

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