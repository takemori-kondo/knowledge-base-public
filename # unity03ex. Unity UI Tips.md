# unity03ex. Unity UI Tips
________________________________________
## 1. TMP用の日本語フォント用意
________________________________________
Noto Sans Japanese - Google Fonts  
https://fonts.google.com/noto/specimen/Noto+Sans+JP

TextMeshProのフォントアセットの作成 | Unity用ビジュアルノベルツール「宴」  
https://madnesslabo.net/utage/?page_id=12915

コラム：文字セット | Unity用ビジュアルノベルツール「宴」  
https://madnesslabo.net/utage/?page_id=12925

The Adobe-Japan1-7 Character Collection  
https://github.com/adobe-type-tools/Adobe-Japan1

1. DLしてプロジェクトに追加
2. Window > TextMeshPro > Font Asset Creator
    - Font Face : Noto Sans JP - Regular
    - Sampling Point Size : 40
    - Padding : 10%
    - Packing Method : Fast
    - Atlas Resolution : 4096 x 4096
    - Character Set : Characters from File
    - Character File : Adobe-Japan1-0.txt
    - Render Mode : SDFAA

※ 7813/7850, Missing 37と出るが、Missingしたのは基本的に発音記号や通常入力しない記号であるため無問題

________________________________________
## 2. DOTweenを使う
________________________________________
メリット

- Animatorは重いため、単純なアニメ(Tween操作)ならDOTweenの方が軽い
- UniTaskと統合可能で、統合すればUniTaskとしてasync/awaitが可能

頻出ショートカット

1. AudioSource (DOFade, DOPitch)
2. Camera (DOShakeRotation)
3. SpriteRenderer (DOColor, DOFade)
4. Transform (DOMove, DOJump, DOLocalRotateQuaternion, DOLookAt, DOScale, DOPunchXXX, DOShakeXXX)

頻出ショートカット（Unity UI）

1. CanvasGroup (DOFade)
2. Image (DOFade, DOFillAmount)
3. RectTransform (DOAnchorPos, DOJumpAnchorPos, DOPunchAnchorPos, DOShakeAnchorPos)
4. TMP_Text (Pro用。色々。DOTweenTMPAnimatorでラップして1文字ごとにアニメーション可能)

頻出メソッドチェーン

メソッド          |内容
------------------|---------------------------------------------------
From()            |指定した内容から開始し、現在の状態になるTweenにする ※1 ※2 ※3
SetDelay(sec)     |開始をディレイする
SetLoops()        |-1で無限ループ。LoopTypeもRestart、Yoyo、Incrementalから選べる
SetEase(Ease)     |イージングを指定する。デフォルト値はEase.OutQuad
SetRelative()     |相対値として解釈する。Fromはこちらからの指定は不可能
SetAutoKill(false)|完了してもTweenを紐づいたままにする。完了後に逆再生やRestartが可能になる

※1 Fromは他のチェーンより先に記述すること  
※2 DOPath、DOPunch、DOShake、SetRelativeなどは非対応  
※3 Relativeについては代わりに引数で指定する

自動破棄をオフにして追従させる場合

- tween = transform.DOMove(target.position, 2).SetAutoKill(false);
- tween.ChangeEndValue(target.position, true).Restart();

強制制御

staticメソッド       |内容
---------------------|------------------
DOTween.Complete(obj)|完了時の状態にして完了する
DOTween.Flip(obj)    |再生を反転する
DOTween.Goto(obj, to)|再生位置を指定した位置にする
DOTween.Pause(obj)   |再生を停止する
DOTween.Play(obj)    |再生を開始/再開する

.

async/await

- AsyncWaitForCompletion()
    - UniTaskと統合している場合はこれは使わない

________________________________________
## 3. タップエフェクトを描画
________________________________________
典型的な処理

```cs
                var screenPos = Input.mousePosition;
                var worldPos = _camera.ScreenToWorldPoint(new Vector3(screenPos.x, screenPos.y, _distance));
                UniTask.Void(async () =>
                {
                    // Instantiate ～ Destroy まで面倒を見る
                    await PlayTapEffectAsync(worldPos);
                });
```

※ Camera Stackが使えないバージョンの場合は、次の「レンダーテクスチャの例」を参照の事

________________________________________
## 4. レンダーテクスチャの例
________________________________________
タップエフェクトを描画（Camera Stackがない場合）

1. RenderTexture：R8G8B8A8_UNorm, D32_SFloat_S8_UInt
2. 撮影カメラ：Clear Flag = Depth Only
3. 描画先Canvas、Raw Image
4. 画面サイズ変更時、RenderTextureも追従しCameraをリセットする
    - RenderTexture.Release()
    - （上手く設定すれば不要？）CanvasScalerのheight基準で追従
    - Camera.ResetAspect()
    - （オプション）縦長な場合はFOV角を横を基準角として縦角を逆算
5. タップ位置から3D空間に配置
    - （上手く設定すれば不要？）CanvasScalerとScreenの比率でタッチ位置の補正
    - Camera.ScreenToWorldPoint()

※ Camera Stackが使える場合は、そちら。そもそもURPはDepth Onlyクリアがなくなっているはず

________________________________________
## 5. UI向けのShader
________________________________________
Unityで手軽に2D擦りガラスシェーダー  
https://qiita.com/ruccho_vector/items/651f760b3240a253bd1d

- glassShader & Blurあり

________________________________________
## 6. 画像アセットぼやけ・1ドット筋問題
________________________________________
画像ぼやけの原因

- デフォルトがCompression : Normal Quality
- 画像を少し圧縮してしまい、元の画像と少し異なる状態になる
    - Compression : None に変えればOK

1ドット筋の原因

- デフォルトがWrapMode : Repeat
- 計算誤差発生時に画像の反対側の端の色で補完してしまう
    - WrapMode : Clampに変えればOK