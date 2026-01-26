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
### 1.2. 短くないAudioClipの設定補足

- BGMなど10秒以上ある音声ファイルは、原則としてLoad TypeをStreamingにすること
    - デフォルトがDecompress On Loadなのはほぼ罠

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