# eco-02. Git
________________________________________
GIT チートシート  
https://training.github.com/downloads/ja/github-git-cheat-sheet.pdf
________________________________________
## 1. Source Tree
________________________________________
補足

```text
内蔵Git/システムGit
・内蔵Gitは、SourceTree内でのみ使用されるGitを一緒にインストールして利用
・システムGitは、別途用意したGitを使う
```

SSH クライアントの設定

```text
・Putty/Plinkの場合、SSHキー
```

________________________________________
## 2. Commands
________________________________________
コマンドとSourceTreeでの等価操作

```bash
# 1. 最初にする設定（SourceTree：設定）
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
git config --global push.default simple

# 2. クローン（SourceTree：クローン）
git clone <url> ./

# 3. 状態の確認
git branch
git status
git show -s --format=%H
git diff
git diff --staged

# 4. 全てフェッチ（SoureTree：フェッチ）
git fetch

# 5. チェックアウト（SourceTree：チェックアウト）
# ローカルブランチが存在せずoriginに同名のブランチがある場合、
# それを追跡するローカルブランチを作成しつつそれをチェックアウトする
git checkout <branch>

# 6. 強制チェックアウト（SourceTree：強制チェックアウト=作業を破棄してチェックアウト）
git checkout --force <branch>

# 7. ローカルブランチを作成してチェックアウト（SourceTree：ブランチの作成）
git checkout -b <branch>

# 8. 全てコミット（SourceTree：全てコミット）
git diff HEAD
git commit -a

# 9. stageする（SourceTree：ステージする=コミット対象にする）
git add <file>

# 10. unstageする（SoureTree：ステージから除く=コミット対象から除く）
git reset HEAD <file>

# 11. stageされた内容をコミット（SourceTree：コミット）
git diff --cached
git commit

# 12. --amendオプション（SourceTree：最後のコミットを上書き）
git commit --amend

# 13. 作業の破棄（SourceTree：破棄）
git checkout -- <file>

# 14. プル（SoureTree：プル）
git fetch
git merge origin/<branch>

# 15. 初回プッシュ（SourceTree：プッシュ（初回））
git push -u <url or origin> <branch>

# 16. 2回目以降のプッシュ（SourceTree：プッシュ）
git push <url or origin> <branch>

# 17. ブランチの強制削除（SourceTree：マージされていないブランチの強制削除）
git branch -D <branch>

# 18. ブランチリセット（SourceTree：リセット）
# 全てunstageし、現ブランチの一連のコミット群を無かったことにし、指定されたコミットまで戻す
# 作業ファイルはunstageの状態でそのまま
# 用途としては、まだプッシュしていないコミットを一旦取り消して整理したいとき
git reset <commit id>

# 18. 強制リセット（SourceTree：HARDリセット）
# 全ての作業を破棄し、現ブランチの一連のコミット群を無かったことにし、指定されたコミットまで戻す
# 作業がなくなるので、とても危険
git reset --hard <commit id>

# 19. リベース。プッシュ済みのブランチで使用しないでください
# 現コミットまでのマージされていないコミット群を、
# 指定したコミットから修正開始してコミットしたかのように、コミット履歴を改変する
# 最新のコミットはMergeした時と同じ内容になるが、コミット履歴が改変される
```

If you want to create origin git server, read "eco-07. Git Server"(other page).