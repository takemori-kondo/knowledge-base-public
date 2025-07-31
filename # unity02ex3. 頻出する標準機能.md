# unity02ex3. 頻出する標準機能
________________________________________
Unity スクリプトリファレンス  
https://docs.unity3d.com/ja/2022.3/ScriptReference/index.html
________________________________________
## 1. 基盤
________________________________________
### 1.1. 基本

Object

- name
- Destroy(obj)
- Object.Instantiate(original, parent)

GameObject

- activeSelf
- transform
- AddComponent()
- GetComponent()
- GetComponentInChildren()
- GetComponentsInChildren()
- SetActive(value)
- GameObject.Find(name)
- GameObject.FindWithTag(tag)

Component/Behaviour/MonoBehaviour

- gameObject
- transform
- enabled
- StartCoroutine(routine)

Debug

- Debug.Log(message, context)
- Debug.LogWarning(message, context)
- Debug.LogError(message, context)

ScriptableObject

Vector2

- magnitude
- normalized
- Normalize()
- Dot(lhs, rhs)
- Lerp(a, b, t)
- MoveTowards(current, target, maxDistanceDelta)
- SignedAngle(from, to)
- SmoothDamp(current, target, ref currentVelocity, smoothTime)
    - +, -, *, /

________________________________________
### 1.2. Timeと非同期

Time

- Time.timeScale
- Time.time
- Time.deltaTime
- Time.fixedTime
- Time.fixedDeltaTime
- Time.unscaledTime
- Time.unscaledDeltaTime

WaitForEndOfFrame/WaitForFixedUpdate/WaitForSeconds/WaitForSecondsRealtime

- new WaitForEndOfFrame()
- new WaitForFixedUpdate()
- new WaitForSeconds(seconds)
- new WaitForSecondsRealtime(time)

async/await対応操作やクラス (待機可能)

- AsyncOperation
- Awaitable.EndOfFrameAsync(cancellationToken)
- Awaitable.WaitForSecondsAsync(seconds, cancellationToken)

________________________________________
### 1.3. ユーティリティ

Mathf

- Mathf.Deg2Rad
- Mathf.PI
- Mathf.Rad2Deg
- Mathf.Abs(f)
- Mathf.Sign(f)
- Mathf.Floor(f)
- Mathf.Ceil(f)
- Mathf.Round(f)
- Mathf.Min(f)
- Mathf.Max(f)
- Mathf.Clamp(f)
- Mathf.Repeat(f, length)
- Mathf.Sin(f)
- Mathf.Cos(f)
- Mathf.Tan(f)
- Mathf.Atan2(f)
- Mathf.MoveTowards(current, target, maxDelta)
- Mathf.SmoothDamp(current, target, ref currentVelocity, smoothTime)

Random

- Random.Range(float minInclusive, float maxInclusive)
- Random.Range(int minInclusive, int maxInclusive)

（※）Random.Rangeは、floatは最大値を含むが、intは最大値を含まない

________________________________________
### 1.4. リソース

個別リソース

- Resources.LoadAsync(path) (待機可能)
- Unity​Engine.​Addressable​Assets.Addressables.LoadAssetAsync(location)

シーン

UnityEngine.SceneManagement.LoadScene()
UnityEngine.SceneManagement.LoadSceneAsync(sceneName)

________________________________________
## 2. グラフィック
________________________________________
### 2.1. Camera、色、画面、RenderTexture

Camera

- orthographic
- orthographicSize
- fieldOfView
- depth （inspector上はPriority）
- clearFlags （inspector上はBackground Type）
- backgroundColor
- targetTexture （inspector上はOutput Texture）
- ResetAspect()
- ScreenPointToRay(position)
- ScreenToWorldPoint(position)

Color/Color32

- Color.Lerp(a, b, t)
    - +, -, *, /
- Color32.Lerp(a, b, t)
    - (Color)

Screen

- Screen.width
- Screen.height
- Screen.sleepTimeout

RenderTexture

- width
- height
- Release()

その他

- UnityEngine.Experimental.Rendering.GraphicsFormat.R8G8B8A8_UNorm
- UnityEngine.Experimental.Rendering.GraphicsFormat.D32_SFloat_S8_UInt

________________________________________
### 2.2. UIと2D

Unity UI - Unity マニュアル  
https://docs.unity3d.com/ja/2022.3/Manual/com.unity.ugui.html

Namespace UnityEngine.UI | Unity UI | 2.0.0  
https://docs.unity3d.com/Packages/com.unity.ugui@2.0/api/UnityEngine.UI.html

※ Unity UI 2.0.0から、TMPは統合されました

Canvas

- renderMode
- sortingOrder （inspector上はSort Order または Order In Layer）

CanvasGroup

- alpha
- blocksRaycasts

CanvasRenderer

UnityEngine.UI.CanvasScaler

- uiScaleMode
- referenceResolution
- screenMatchMode
- matchWidthOrHeight

GraphicRaycaster

RectTransform（Stretchなし）

- RectTransform.anchoredPosition
- RectTransform.sizeDelta
- RectTransform.anchorMin
- RectTransform.anchorMax

RectTransform（Stretchあり）

- RectTransform.offsetMin
- RectTransform.offsetMax
- RectTransform.anchorMin
- RectTransform.anchorMax

Graphicおよび継承クラス

- color
- raycastTarget
    - Image
        - sprite （inspector上はSource Iamge）
        - type Simple/Sliced/Tiled/Filled
        - fillMethod Horizontal/Vertical/Radial90/180/360
        - fillOrigin
        - fillAmount
    - RawImage
        - texture
    - TMP_Text
        - text

Selectableおよび継承クラス

- animator
- colors
- image
- interactable （※ 表示の変化もあるenabled）
    - Button
        - onClick
    - ScrollBar
        - 略
    Slider
        - 略
    Toggle
        - isOn
        - onValueChanged
    - TMP_Dropdown
        - 略
    - TMP_InputField
        - onDeselect
        - onSelect
        - onValueChanged
        - text

ScrollRect

- verticalNormalizedPosition
- viewport

ScrollView

- 略

Sprite

- texture
- Sprite.Create(texture, rect, pivot)

SpriteRenderer

- 略

Texture2D

- width
- height
- LoadImage(data) （※ pngのbyte[]など）

________________________________________
### 2.2ex. LayoutGroup関連

- ContentSizeFitter.horizontalFit
- LayoutRebuilder.ForceRebuildLayoutImmediate

________________________________________
### 2.3. 3D

Transform

- localScale
- localRotation
- position
- Rotate(pitch, yaw, roll)
- Translate(right, up, forward)
- SetParent(parent, worldPositionStays)

その他

- Material
- Mesh
- MeshFilter.mesh
- MeshRenderer.material

________________________________________
### 2.4. Animator（Mecanim）

※ 単純なUI制御はDOTweenで行うこと。Mecanimは重い

AnimationClip

- 略

Animator

- GetCurrentAnimatorStateInfo(layerIndex)
- SetTrigger(name)

AnimatorStateInfo

- IsName(name)

________________________________________
## 3. 入力
________________________________________
軸（Vertical, Horizontal, Mouse X, Mouse Y, Mouse ScrollWheelなどが定義済）

- GetAxis(axisName)

マウス

- Input.mousePosition
- Input.mouseScrollDelta
- Input.GetMouseButton(button)
- Input.GetMouseButtonDown(button)
- Input.GetMouseButtonUp(button)

キーボード

- Input.GetKey(key)
- Input.GetKeyDown(key)
- Input.GetKeyUp(key)

スマホ加速度、向き、タッチ数

- Input.acceleration
- Input.deviceOrientation
- Input.touchCount
- Input.touchs

________________________________________
## 4. Audio
________________________________________
音声再生

AudioClip （※ .mp3, .ogg, .wavなどが対応。取り込むと自動で認識）

AudioSource

- clip
- Play()
- PlayOneShot(clip, volumeScale)

________________________________________
## 5. 物理演算
________________________________________
2D版

- Collider2D.略
- Rigidbody2D.略

















