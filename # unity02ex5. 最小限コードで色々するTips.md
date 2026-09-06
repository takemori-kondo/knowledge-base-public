# unity02ex5. 最小限コードで色々するTips
________________________________________
## 1. シーン間で互いのObjectを知る
________________________________________
ScriptableObjectを経由する

1. 参照をプロパティ保持するScriptableObjectを用意
2. ↑を初期化するための、UnityEvent _onAwakeを公開したRegisterを配置
    -  onAwakeにScriptableObjectをアサインし、さらにプロパティに対してオブジェクトをアサイン
3. 利用側はScriptableObjectを参照してそこから取得


