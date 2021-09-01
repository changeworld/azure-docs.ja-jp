---
title: Unity 向けの Azure Spatial Anchors をインストールする
description: Azure Spatial Anchors を使用するように Unity プロジェクトを構成する
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/30/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: d87c789b4c5f1decc468838ccabd136cec32cabc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121721805"
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

### <a name="hololens-only-configure-your-unity-project-xr-settings"></a>HoloLens のみ: Unity プロジェクトの XR 設定を構成する
HoloLens で複合現実アプリを開発する場合、Unity で XR 構成を設定する必要があります。 詳細については、「[XR 構成の設定 - 混合現実 | Microsoft Docs](https://docs.microsoft.com/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr)」と「[Unity バージョンと XR プラグインの選択 - 混合現実 | Microsoft Docs](https://docs.microsoft.com/windows/mixed-reality/develop/unity/choosing-unity-version)」を参照してください。

Azure Spatial Anchors SDK 2.9 以前では、Windows XR プラグイン (com.unity.xr.windowsmr) のみがサポートされています。そのため Azure Spatial Anchor の HoloLens Unity パッケージには、com.unity.xr.windowsmr パッケージに対する明示的な依存関係があります。

Azure Spatial Anchors SDK のバージョン 2.10.0 以降では、Mixed Reality OpenXR プラグイン ([com.microsoft.mixedreality.openxr](https://dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging?_a=package&feed=Unity-packages&view=overview&package=com.microsoft.mixedreality.openxr&protocolType=Npm)) と Windows XR プラグイン ([com.unity.xr.windowsmr](https://docs.unity3d.com/Manual/com.unity.xr.windowsmr.html)) の両方がサポートされています。 プロジェクトには、ユーザーの選択に従って、com.microsoft.mixedreality.openxr または com.unity.xr.windowsmr のいずれかを含める必要があります。

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
