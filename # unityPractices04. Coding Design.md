# unityPractices04. Coding Design
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
5. Viewがパッドやキーボード入力に反応する必要がある場合は、ViewとControllerを分けたほうが良さげ。メニューなど
    - 例：SelectionList、SelectionListView
    - ViewはPassive View化し、常にControllerがViewの状態を決定する
        - クリックイベント等もControllerへ通知だけする

________________________________________
## 2. 各種Manager
________________________________________
ISoundManager

- PlayBgm
- StopBgm
- PlaySe
- StopSe
- PlayVoice
- StopVoice

IInputProxy

- 略

IEffectManager

- PlayAsync(effectType)
- IsEnd()
- Finish()

________________________________________
## 3. 実践プラクティス
________________________________________
### 3.1. DialogueDataの管理

台詞テキストとボイスは対になるので、Csvで管理し、コードもDtoを用意でまとめるて管理する