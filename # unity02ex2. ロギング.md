# unity02ex2. ロギング
________________________________________
Unity向けのロギング

- Debug.Log(message, context)
- Debug.LogWarning(message, context)
- Debug.LogError(message, context)

補足

- \<color=#ffffff>で色付け可能
- contextにGameObjectを渡すと、Consoleで選択時にHierarchy側も強調表示される
- Console ウィンドウは検索できるため、検索しやすいようにフォーマットを決めておくと便利

注意点

- Unity6時点で、Debug.Logはリリースビルドにしても削除されないため、コストが馬鹿に出来ない
- 必ず、チームの方針に合わせたConditional属性付きログラッパーを作ること

定番

```cs
public static class DevLog
{
    [Conditional("UNITY_EDITOR"), Conditional("DEVELOPMENT_BUILD")]
    public static void Log(Func<string> msg, UnityEngine.Object context = null) => UnityEngine.Debug.Log(msg(), context);
    
    [Conditional("UNITY_EDITOR"), Conditional("DEVELOPMENT_BUILD")]
    public static void LogWarning(Func<string> msg, UnityEngine.Object context = null) => UnityEngine.Debug.LogWarning(msg(), context);

    [Conditional("UNITY_EDITOR"), Conditional("DEVELOPMENT_BUILD")]
    public static void LogError(Func<string> msg, UnityEngine.Object context = null) => UnityEngine.Debug.LogError(msg(), context);
}
```