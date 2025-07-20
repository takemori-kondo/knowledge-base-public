# unity01ex3. URPとEditor
________________________________________
## Universal Render PipelineのEditor統合の変遷

1. 2018.1 で SRP(LWRP)が実験的に公開
2. 2019.3 で URPに改称し、再設計された
3. 2021.2 で 現在の形に近いSettingsフォルダが用意済のテンプレートが用意された
    - UniversalRenderPipelineGlobalSettings.assets を明示的にProject Settingsに指定する必要があった
    - 2022 LTS で正式リリース
4. 2023.3? Unity6? で Project Settings側でUniversalRenderPipelineGlobalSettings.assetsが意識しなくても良いようになった