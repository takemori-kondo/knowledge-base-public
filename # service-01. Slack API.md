# service-01. Slack API
________________________________________
準備

```text
# 1. 以下でApp（アドイン）トークンを用意
# https://api.slack.com/apps
#
# 2. OAuth & PermissionsでScopeを追加
# （Scopeは、iOS/Androidのcapability宣言と考え方は同じ。例えばbot scopeの以下）
# chat:write
# chat:write.customize
# 
# 3. Basic Information > Install your app > Install to Workspace
#
# 3.5. OAuth 許可
#
# 4. Slackを開き、Appを対象のchannelに追加
#
# 5. 準備は整ったのでcurl等で実際にたたいてみる
# OAuth & Permissions のTokenを使用して、例えば以下
curl -XPOST "https://slack.com/api/auth.test?token=<トークン>"

# 6. chatの例
curl -XPOST -d "token=<トークン>" -d "channel=C01FY5PVD55" -d "text=Hello slack" -d "username=from curl" -d "icon_emoji=whale" "https://slack.com/api/chat.postMessage"
```