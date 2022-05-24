# infra-01. Hosting Service

料金計算ツール | Microsoft Azure  
https://azure.microsoft.com/ja-jp/pricing/calculator/

B-series burstable virtual machine sizes  
https://docs.microsoft.com/ja-jp/azure/virtual-machines/sizes-b-series-burstable

Dv4 and Dsv4-series  
https://docs.microsoft.com/ja-jp/azure/virtual-machines/dv4-dsv4-series

________________________________________
## 1. 100 Pallarel Connection Spec
________________________________________
### 1.1. Price, Transfer, Throughputs

Service                                |type           |year with tax|LAN Mbps|1node php7 rps|note
---------------------------------------|---------------|-------------|--------|--------------|------
さくらドメイン                         |domain         |        1,886|       -|             -|-
Value Domain                           |domain         |        1,408|       -|             -|初年度は968
さくらライト + さくらドメイン          |share+domain   |        3,457|       ?|             ?|1,571(server) + 1,886(domain)
Value Domain まるっと                  |share+domain   |        3,058|       ?|             ?|1,650(server) + 1,408(domain)。Opcacheデフォルト有効。
X10(ドメイン永久無料)                  |share+domain   |       11,880|       ?|             ?|2022年からスタンダードに名称変更。さくら/Value Domainのスタンダードより制限が緩いので高め
さくら VPS 石狩 1GB                    |vps            |        9,680|       ?|             ?|年間一括。2019年以降の価格
ConoHa VPS 1GB                         |vps            |       10,137|       ?|             ?|10,137。割引きっぷ(12カ月)+キャンペーン4%≒約12%off
Azure B1ms(!1)                         |vps            |       17,866|     480|         78-98|1年予約。 15,570 + 56 * 12 + 税。送信データ転送料別
Azure B1ms(!1)×3 + Redis C0 bsc       |vps+lb+session |       77,331|     480|         78-98|1年予約。 17,866 * 3 + (1,798 * 12 + 税)。1つはDB用(!4)
Azure D4a(!2)                          |vps            |      158,683|    2000|             ?|1年予約。143,586 + 56 * 12 + 税(!4)
Azure D4a(!2)×2 + db(!3) + RedisC2std |vps+lb+session |      761,360|    2000|             ?|1年予約。158,683 * 2 + 202,250 + (18,314 * 12 + 税)(!4)

-

※1 2021/03現在、税込み10%、12カ月前払いの割引適用を前提。無料SSL設定対応前提

※2 さくらドメイン、Valueドメインのドメインはcomの価格を想定

※3 本番運用に際し、一定水準以上のシステムが必要な場合、別途大容量ストレージなどが必要なことが多い（ログやDBバックアップの退避先）

!1 東日本 B1ms CentOS  + standard ssd e1, 100stユニット（1st=256KiB。1stユニット=10000st≒2.56GB）, 送信データ転送量5GB/月まで無料

!2 東日本 Dav4D4 CentOS + standard ssd e1, 100stユニット（1st=256KiB。1stユニット=10000st≒2.56GB）, 送信データ転送量5GB/月まで無料

!3 東日本 MariaDB Gen5 汎用 2core 100GB + 第2バックアップ 100GB。1年予約月払い（前払いのメリットがない）。15,322 * 12 + 税 = 202,250円

!4 送信データ転送量5GB/月以降は、別途料金がかかる

________________________________________
### 1.2. Azure CPU Score

https://docs.microsoft.com/ja-jp/azure/virtual-machines/linux/compute-benchmark-scores

要約（東日本リージョン）

VM   |    CPU|Core|RAM| 税抜月額|Note
-----|-------|----|---|---------|----------------------------
B1S  | 13,736|   1|  1|      653|1年予約。200％バーストスコア
B1MS | 13,593|   1|  2|    1,297|1年予約。200％バーストスコア
B2S  | 27,488|   2|  4|    2,604|1年予約。200％バーストスコア
A1v2 |  6,483|   1|  2|    4,415|-
D2av4| 30,023|   2|  8|    5,982|1年予約
D4av4| 59,685|   4| 16|   11,965|1年予約

- 