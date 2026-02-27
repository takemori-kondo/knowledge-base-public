# unity10. Audio
________________________________________
## 1. 基本
________________________________________
### 1.1. 典型的な再生

1. AudioSourceコンポーネントを用意しておく
2. AudioSourceにclipを設定
3. Play()またはPlayOneShot()
    - Playはループ等可能だが同一内容の多重再生はできず、今の再生を止める
    - PlayOneShot()は単一のAudioSourceで多重再生できる

________________________________________
### 1.2. ファイル形式と取り込みの要約

Unity側の挙動

- 取り込み時にCompression Formatで再変換したものを使用する
    - 元ファイルをそのまま使うわけではない
    - 例：取り込み時にvorbis(Oggで使われることが多いコーデック)に変換されてLibraryにキャッシュ

参考: 元ファイルの典型的な形式

ext |典型的な形式
----|--------------------------
wav |48kHz, 24bit, stereo, PCM
mp3 |48kHz, stereo, 192kbps

.

Unity側の推奨設定

- Load Typeは、BGMなど10秒以上ある音声ファイルはをStreaming
    - Editorコードで取り込み拡張を用意して、自動判定・適用するのが定番
- Vorbis, Quality=100, Preserve Sample Rate

________________________________________
### 1.3. BGM、SE、Voiceなどのカテゴリでグループ管理

【Unity】AudioMixerでマスター・BGM・SEの音量を管理する方法  
https://wamutai-tech.com/unity-audiomixer/

AudioMixer

1. AudioMixerアセット作成
2. Master - BGM, SE, Voiceとなるように構成追加
3. ExposeでVolumeなど、別スクリプトから操作可能にしたい項目を操作可能にする
4. AudioSource側のOutputと紐づける
5. Volume操作用スクリプトを用意して、Volume操作する
    - `dB = Mathf.Log10(Mathf.Clamp(v, 0.00000000f, 1.0f)) * 20f` など