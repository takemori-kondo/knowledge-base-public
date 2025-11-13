# unityPractices04. Codning Design
________________________________________
## 1. コード設計
________________________________________
### 1.1. 設計と要約

- 1979 Smalltalk-80 mvc
- 1991 コードビハインド + コンポーネントモデル
- 1992 3-tier (クライアント、サーバ、DB)
- 2003 DDD 4-layer (UI、アプリケーション(UseCase)、ドメイン、インフラ)

```text
   Ｃ  ◁┐       Ｃ   
  ↙  ↘  │      ↗  ↘  
Ｍ ← Ｖ┘    Ｍ    Ｖ
└──▷               

Behind Code(View) ←→ UI
↓
Application (Controller, UseCase)
↓
Domain (Model)
```

基本的なプラクティス

1. 初期データとステータスを分離する
2. GUI、コアロジック、キャラクターは疎結合にする
3. GeneratorあるいはSpawner。オブジェクトの生成管理するScript
4. ManagerあるいはDirector。空のオブジェクトにアタッチして全体管理する純粋なScript