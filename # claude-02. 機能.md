# claude-02. 機能
________________________________________
Claude Code の設定  
https://code.claude.com/docs/ja/settings
________________________________________
## 1. 機能の概要
________________________________________
### 1.1. 全体構造の概要

主なファイル

パス                                    |概要・主な用途
----------------------------------------|--------------------------
リポジトリ/CLAUDE.md                    |コード規約、推奨ツールを指示
リポジトリ/.mcp.json                    |MCP servers 定義
リポジトリ/.claude/settings.local.json  |設定（プロジェクト内の個人用。.gitignore推奨）
リポジトリ/.claude/settings.json        |設定（プロジェクト用。gitに上げる用）
リポジトリ/.claude/agents/foo.md        |Subagent
リポジトリ/.claude/skills/foo/SKILL.md  |Skill
~/.claude.json                          |（全共通・個人用）MCP servers 定義
~/.claude/settings.json                 |（全共通・個人用）設定
~/.claude/agents/foo.md                 |（全共通・個人用）Subagents
~/.claude/skills/foo/SKILL.md           |（全共通・個人用）Skill
~/.claude/plugins/                      |（全共通・個人用）プラグイン
~/.claude/CLAUDE.md                     |（全共通・個人用）説明（全共通・個人用）
~/.claude/projects/                     |各プロジェクトごとの情報保持
~/.claude/projects/xxx/memory/...       |各プロジェクトごとのClaudeCodeが自分用に書いたメモ
~/.claude/projects/xxx/yyy.jsonl        |各セッションごとの会話履歴
~/.claude/projects/xxx/yyy/...          |各セッションごとのsubagents、tool-results

.

配置するだけで機能するもの

1. CLAUDE.md、rulesフォルダ
    - rulesはあまり上手く機能しない（Issuesも放置されている）
2. メモリ (claude code自身がメモした内容)
3. skills (≒「○○に書かれた自然言語指示内容を実施して」)
    - https://code.claude.com/docs/ja/skills
4. agents (コンテキストが独立した別agentで処理を実施)
    - https://code.claude.com/docs/ja/sub-agents

settings.json に依存する機能

1. 暗黙に許可されていないシェル操作全般
2. hooks (指定したタイミングで指定したシェル操作を自動実行)
    - https://code.claude.com/docs/ja/hooks
3. MCP サーバー 使用許可 (定義とは別に必要)
    - https://code.claude.com/docs/ja/mcp

settings.json の使い分け

優先度|その他                                |推奨される使い方
------|--------------------------------------|------------------------------
1     |リポジトリ/.claude/settings.local.json|.gitignoreして上げない。個人用設定
2     |リポジトリ/.claude/settings.json      |プロジェクトの設定
3     |~/.claude/settings.json               |自分用の全プロジェクト共通設定

※ 他にも組織向けのManagedがあるが割愛

settings.json 抜粋

jsonキー           |内容
-------------------|------------------------------------
permissions        |どんな操作・mcp利用がユーザに聞かずに行えるか
env                |環境変数
hooks              |使用可能なHook
statusline         |常時表示するカスタムステータスライン

.

________________________________________
### 1.2. 主なコマンド

コマンド   |内容
-----------|------------------------
/login     |ログイン
/logout    |ログアウト
/add-dir   |認識可能なフォルダの追加
/context   |コンテキスト状況の確認（20万が上限でその前に自動圧縮）
/status    |claude code自身が与えられている権限一覧などを表示
/init      |CLAUDE.md を対話式で作成・修正してくれる
/hooks     |hooks の使い方を教えてくれて、作ってくれる
/skills    |Skill の使い方を教えてくれて、作ってくれる
/agents    |subagents の使い方を教えてくれて、作ってくれる
/mcp       |接続可能な MCP サーバー及び認証状況の表示
/スキル名  |自作スキルの実施

.