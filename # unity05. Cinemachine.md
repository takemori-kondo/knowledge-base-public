# unity05. Cinemachine
________________________________________
## 1. 概要
________________________________________
典型的な例1

1. 実際に動かすカメラ(Main Cameraなど)にBrain
2. 所望のTrack(移動制御などを行うオブジェクト)などを用意
    - Dolly Track with Cart など
3. 仮想カメラを追加・設置

典型的な例2

1. 実際に動かすカメラ(Main Cameraなど)にBrain
2. 仮想カメラを2か所に設置
3. スクリプトから2つの仮想Cameraの位置を切り替えられるようにする

典型的な例3 (cinemachine3 から簡単に実現可能になった)

1. 複数のCameraとBrainを用意し、それぞれChannelマスクが重複しないように設定
2. 仮想カメラ側では、OutputChannelを紐づけたいBrainが管理するChannelにする

パラメータ

プロパティ          |概要
--------------------|-------------------------------------------------------------------------------------
Status: Live        |Debug中の確認用機能。この仮想カメラの今の表示を確認する
Game Window Guides  |ターゲットを追従する際のオプションの設定
Save During Play    |Debug中に設定した内容をDebugが終了しても保持させるか
Priority            |この値が最も高い仮想カメラが実際のカメラになる
Follow              |追跡対象の指定
Look At             |注視対象の指定
Standby Update      |非Liveなカメラ更新頻度の指定
Lens                |カメラのレンズオプション
Transitions         |カメラ間遷移のオプション
    Blend Hint      |移動経路のオプション。直線、円、円柱、Look At角ベースでブレンド
    Inherit Position|仮想カメラがLiveになる際に位置を実カメラの位置からスタートして本来の位置に補間していく
Body                |Followに対するオプション
    Damping         |カメラ追従の際に本来の位置に移動するまでの時間。大きいと鈍くなる
    その他いろいろ  |被写体に人間を映すことを想定したパラメータ群（右肩にずらす量、上にずらす量）

.    