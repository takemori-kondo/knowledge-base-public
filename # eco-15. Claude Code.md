# eco-15. Claude Code
________________________________________
## 1. CUI版準備

準備

1. アカウント用意 (ProかMaxを購入おすすめ)
2. node.jsインストール(必要に応じて以下管理者で実行)
    - Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
3. npm install -g @anthropic-ai/claude-code
4. シェルを再起動

起動

```powershell
$dir = "D:\Dropbox\repos_unity\Uft.AdvTools\Assets\Uft.AdvTools\Runtime"
cd $dir
claude
```

________________________________________
## 2. 用語やコマンド


用語やコマンド          |内容
------------------------|------------------------------------------
/stats                  |コマンド。サブスク版での使用状況を確認する
/clear                  |コマンド。コンテキストをクリアする
Enterprice memory       |?
Claude memory           |?
Common memory           |?
project rule/user memory|?
MCP                     |?

________________________________________
## 3. 例

例1

CmdTween に関係するファイルだけ見て。新しくWaitType列挙型(Default, NoWait)を追加して、NoWait時はTween完了を待たず次のコマンドへ進む差分を出して。

例2

Powershellでスクショ取らせて実行状態認識させて、プログラムを修正させる