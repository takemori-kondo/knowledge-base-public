# aws-04. AWS S3
________________________________________
## 1. S3
________________________________________
※ 基本的なことは、「# aws-01. AWS お試し」を参照してください

Amazon S3 のよくある質問  
https://aws.amazon.com/jp/s3/faqs/

オブジェクト キー名の作成  
https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-keys.html

Amazon S3 からの HTTP 500 または 503 エラーをトラブルシューティングするにはどうすればよいですか?  
https://repost.aws/ja/knowledge-center/http-5xx-errors-s3

________________________________________
### 1.1. よくあるトラブル

1. 単純な方式では1アップロード5GB制限
    - それ以上の場合はマルチパートオプションが必要
2. フォルダやファイル名で避けたほうが良い文字
    - 後述
3. 高負荷でなくても極まれに5xxを返すことがある
   - 再試行すればよい
   - 経験則では、10万～20万回アップロードに1回程度？

________________________________________
### 1.2. ファイル名で避けたほうが良い文字

```text
\{^}%`]'>[~<#|
&$@=;/:+,?
制御文字
2つ以上連続するスペース
```

________________________________________
### 1.3. よく使うコマンド例

```powershell
# profile名が「PowerUserAccess-330980311219」の例

# AWSアカウントに紐づいたバケットをすべて表示
aws s3 ls --profile PowerUserAccess-330980311219

# 特定のフォルダの中身を表示
aws s3 ls s3://takemori-sample-bucket/sample01/ --profile PowerUserAccess-330980311219

# コピー
aws mv cp sample-file02.txt s3://takemori-sample-bucket/sample02/

# 移動
aws s3 mv sample-file03.txt s3://takemori-sample-bucket/sample02/
```