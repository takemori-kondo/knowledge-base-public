# vm-04ex. Dockerの概要
________________________________________
## 1. Dockerとは
________________________________________
Dockerはコンテナ基盤

- Docker CLIコマンドの受付 & コンテナ監視デーモン
- Docker CLI
- Dockerfile(コンテナ定義)
- コンテナ
- docker-compose.yml

コンテナの仕組み

- OSユーザ&プロセスをVMに見立てたもの
- Linux Distributionの違いは吸収できる
- DockerホストがUbuntu、コンテナがRedhatも可能

ネットワーク・接続の仕組み

- Docker外部と内部は、NAT/ポートフォワーディング
- マッピングは内外同じポート番号にすればシンプル
- Docker内部コンテナ同士はPrivateIPで通信可能

Dockerfileとキャッシュの仕組み

- sudo docker compose up -dは、ビルド&起動に相当
- ビルド成功後は、Dockerイメージキャッシュを使う
    - Dockerfileを参照しなくなる
    - Dockerfileを編集した場合、明示的な再ビルドが必要

その他

- 事実上Linuxを前提。CRLFで不具合

________________________________________
## 2. 歴史と補足
________________________________________
ネットワーク機能

Release 1.6.0 docker/compose
https://github.com/docker/compose/releases/tag/1.6.0

- 直感的なネットワーク設定が可能な機能
- Compose file version2.0から可能になった