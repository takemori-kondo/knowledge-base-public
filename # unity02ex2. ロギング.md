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
        [Conditional("UNITY_EDITOR"), Conditional("DEVELOPMENT_BUILD"), Conditional("DEBUG")]
        public static void Log(string message, Object context = null, string color = null)
        {
            message = FixColorTag(message, color);
            UnityEngine.Debug.Log(message, context);
        }

        [Conditional("UNITY_EDITOR"), Conditional("DEVELOPMENT_BUILD"), Conditional("DEBUG")]
        public static void LogWarning(string message, Object context = null, string color = null)
        {
            message = FixColorTag(message, color);
            UnityEngine.Debug.LogWarning(message, context);
        }

        [Conditional("UNITY_EDITOR"), Conditional("DEVELOPMENT_BUILD"), Conditional("DEBUG")]
        public static void LogError(string message, Object context = null, string color = null)
        {
            message = FixColorTag(message, color);
            UnityEngine.Debug.LogError(message, context);
        }

        static string FixColorTag(string message, string color)
        {
            if (!string.IsNullOrWhiteSpace(color))
            {
                message = $"<color={color}>{message}</color>";
            }
            return message;
        }

        [Conditional("UNITY_EDITOR"), Conditional("DEVELOPMENT_BUILD"), Conditional("DEBUG")]
        public static void LogException(System.Exception exception, Object context = null)
        {
            UnityEngine.Debug.LogException(exception, context);
        }
```