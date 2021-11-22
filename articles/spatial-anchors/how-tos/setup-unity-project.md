---
title: Unity 向けの Azure Spatial Anchors をインストールする
description: Azure Spatial Anchors を使用するように Unity プロジェクトを構成する
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/12/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 945a95054f281e0a5920232a729de4087d1deab6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490518"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Unity プロジェクトでの Azure Spatial Anchors の構成

このガイドでは、Unity プロジェクトで Azure Spatial Anchors SDK の使用を開始する方法について説明します。

## <a name="project-requirements"></a>プロジェクトの要件

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>プロジェクトの構成

Unity プロジェクトに Azure Spatial Anchors SDK を含める前に、必ず Unity パッケージ マネージャーを使用して[必要な](#project-requirements)パッケージをインストールしてください。

### <a name="download-asa-packages"></a>ASA パッケージのダウンロード
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>ASA パッケージのインポート
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="hololens-only"></a>HoloLens のみ

#### <a name="configure-your-unity-project-xr-settings"></a>Unity プロジェクトの XR 設定を構成する
HoloLens で複合現実アプリを開発する場合、Unity で XR 構成を設定する必要があります。 詳細については、「[XR 構成の設定 - 混合現実 | Microsoft Docs](/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr)」と「[Unity バージョンと XR プラグインの選択 - 混合現実 | Microsoft Docs](/windows/mixed-reality/develop/unity/choosing-unity-version)」を参照してください。

Azure Spatial Anchors SDK バージョン 2.9.0 以前では、Windows XR プラグイン (com.unity.xr.windowsmr) のみが提供されます。そのため、Azure Spatial Anchors ウィンドウ パッケージは Windows XR プラグインに明示的に依存します。

Azure Spatial Anchors SDK バージョン 2.10.0 以降では、Mixed Reality OpenXR プラグイン ([com.microsoft.mixedreality.openxr](https://dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging?_a=package&feed=Unity-packages&view=overview&package=com.microsoft.mixedreality.openxr&protocolType=Npm)) と Windows XR プラグイン ([com.unity.xr.windowsmr](https://docs.unity3d.com/Manual/com.unity.xr.windowsmr.html)) の両方がサポートされます。 選択に基づき、プロジェクトに com.microsoft.mixedreality.openxr パッケージまたは com.unity.xr.windowsmr パッケージを含める必要があります。

#### <a name="configure-your-unity-project-capabilities"></a>Unity プロジェクトの機能を構成する

Unity プロジェクトでは、次の機能を必ず有効にしてください。
- SpatialPerception
- InternetClient
- PrivateNetworkClientServer

> [!WARNING]
> PrivateNetworkClientServer 機能を有効にしないと、プライベートに構成されているネットワークがデバイスで使用されている場合に、アンカーのクエリが失敗する可能性があります。
### <a name="android-only-configure-the-maintemplategradle-file"></a>Android のみ:mainTemplate.gradle ファイルを構成する

1. **[Edit]\(編集\)**  >  **[Project Settings]\(プロジェクト設定\)**  >  **[Player]\(プレーヤー\)** の順に移動します。
2. **[プレーヤー設定]** の **[インスペクター] パネル** で、**Android** アイコンを選択します。
3. **[ビルド]** セクションで、 **[カスタム メイン Gradle テンプレート]** チェックボックスをオンにして、`Assets\Plugins\Android\mainTemplate.gradle` でカスタム Gradle テンプレートを生成します。
4. `mainTemplate.gradle` ファイルをテキスト エディターで開きます。
5. `dependencies` セクションに、次の依存関係を貼り付けます。

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

すべて完了すると、`dependencies` セクションは次のようになります。

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: Unity でアンカーを作成して配置する](./create-locate-anchors-unity.md)