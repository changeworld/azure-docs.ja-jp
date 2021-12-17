---
title: 'クイック スタート: クイック スタート アプリを Unity 2020 にアップグレードする'
description: このクイックスタートでは、Object Anchors を使用するクイックスタート アプリ (HoloLens Unity アプリ) を Unity 2020 にアップグレードする方法について説明します。
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 06/23/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: b628aab20e08460ea6d023651098227aa3390d5e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791994"
---
# <a name="quickstart-upgrade-quickstart-app-to-unity-2020"></a>クイック スタート: クイック スタート アプリを Unity 2020 にアップグレードする

このクイックスタートでは、[Azure Object Anchors](../overview.md) を使用する Unity HoloLens アプリを Unity 2019 から Unity 2020 にアップグレードします。 Azure Object Anchors は、3D アセットを HoloLens の物体認識 Mixed Reality エクスペリエンスを実現する AI モデルに変換するマネージド クラウド サービスです。 作業を終えると、Unity で作成された HoloLens アプリで、物理世界のオブジェクトを検出できるようになります。

学習内容は次のとおりです。

> [!div class="checklist"]
> * アプリを Unity 2019 から Unity 2020 にアップグレードする。
> * パッケージの依存関係をアップグレードする。
> * Unity のビルド設定をアップグレードする。

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

* [Unity HoloLens](get-started-unity-hololens.md) または [MRTK を使用した Unity HoloLens](get-started-unity-hololens-mrtk.md) のクイックスタートでの、すべての前提条件。
* <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2020.3.8f1 以降を使用している Unity Hub</a>

## <a name="open-and-upgrade-the-sample-project"></a>サンプル プロジェクトを開いてアップグレードする

[Unity HoloLens](get-started-unity-hololens.md) または [MRTK を使用した Unity HoloLens](get-started-unity-hololens-mrtk.md) のクイックスタートの手順に従って、[サンプル リポジトリ](https://github.com/Azure/azure-object-anchors)を複製し、Unity 用 Azure Object Anchors パッケージをダウンロードします。

Unity Hub を開きます。 **[追加]** ボタンを選択し、`quickstarts/apps/unity/basic` プロジェクトまたは `quickstarts/apps/unity/mrtk` プロジェクトを選択します。 次に、 **[Unity Version]\(Unity バージョン\)** 列で、マシンにインストールした Unity 2020 のバージョンをドロップダウンから選択します。 **[ターゲット プラットフォーム]** 列で、 **[ユニバーサル Windows プラットフォーム]** を選択します。 最後に、 **[プロジェクト名]** 列を選択し、Unity でサンプルを開きます。

:::image type="content" source="./media/upgrade-unity-2020.png" alt-text="Unity 2020 をアップグレードする":::

プロジェクトをアップグレードするかどうかを確認するダイアログが表示されます。 **[確認]** ボタンを選択します。

:::image type="content" source="./media/confirm-unity-upgrade.png" alt-text="Unity のアップグレードを確認する":::

## <a name="upgrade-package-dependencies"></a>パッケージの依存関係をアップグレードする

アップグレード プロセスが完了すると、**Unity Editor** が開きます。

<a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Mixed Reality Feature Tool</a> のドキュメントに従ってツールを設定し、使用方法を確認してください。

**[Platform Support]\(プラットフォーム サポート\)** セクションで、**Mixed Reality OpenXR Plugin** 機能パッケージのバージョン 1.0.0 以降を Unity プロジェクト フォルダーにインストールします。 `quickstarts/apps/unity/mrtk` プロジェクトを操作している場合は、 **[Mixed Reality Toolkit]** セクションも開いて **Mixed Reality Toolkit Foundation** 機能パッケージと **Mixed Reality Toolkit Tools** 機能パッケージを見つけ、これらをバージョン 2.7.0 以降にアップグレードします。

**Unity Editor** に戻ります。 **Mixed Reality Feature Tool** の機能パッケージがインストールされる間、数分かかることがあります。

新しい入力システムを有効にするかどうかを確認するダイアログが表示されます。 **[はい]** ボタンを選択します。

:::image type="content" source="./media/new-input-system.png" alt-text="新しい入力システム":::

 MRTK シェーダーの上書きを求めるダイアログが表示された場合は、 **[はい]** を選択します。

:::image type="content" source="./media/mrtk-shaders.png" alt-text="MRTK シェーダー":::

インストール プロセスが完了すると、Unity が自動的に再起動します。

## <a name="update-configuration-settings"></a>構成設定の更新

**Unity Editor** に戻り、「<a href="/windows/mixed-reality/develop/unity/xr-project-setup#configuring-xr-plugin-management-for-openxr" target="_blank">OpenXR 用の XR プラグイン管理の構成</a>」のドキュメントに従って、 **[Project Settings]\(プロジェクト設定\)** で **[XR Plugin Management]\(XR プラグイン管理\)** を設定します。 次に、<a href="/windows/mixed-reality/develop/unity/xr-project-setup#optimization" target="_blank">最適化</a>に関するドキュメントに従って、HoloLens 2 に推奨されるプロジェクト設定を適用します。

## <a name="update-mrtk-settings"></a>MRTK 設定を更新する

`quickstarts/apps/unity/mrtk` プロジェクトを操作している場合は、MRTK でもいくつかの調整が必要です。 その場合は、次の手順に従ってください。 それ以外の場合は、「**アプリをビルド、デプロイ、および実行する**」セクションにスキップしてください。

**Unity Editor** で `Assets/MixedReality.AzureObjectAnchors/Scenes` に移動し、 **[AOASampleScene]** を開きます。 **[Hierarchy]\(階層\)** ウィンドウで、 **[MixedRealityToolkit]** オブジェクトを選択します。

:::image type="content" source="./media/open-sample-scene.png" alt-text="サンプル シーンを開く":::

**[インスペクター]** ウィンドウで **[カメラ]** ボタンを選択し、プロファイルを **ObsoleteXRSDKCameraProfile** から **DefaultMixedRealityCameraProfile** に変更します。

:::image type="content" source="./media/update-camera-profile.png" alt-text="カメラ プロファイルを更新する":::

引き続き **[インスペクター]** ウィンドウで **[入力]** ボタンを選択し、 **[Input Data Providers]\(入力データ プロバイダー\)** ドロップダウンを展開します。 次に、「<a href="/windows/mixed-reality/mrtk-unity/configuration/getting-started-with-mrtk-and-xrsdk#configuring-mrtk-for-the-xr-sdk-pipeline" target="_blank">XR SDK パイプライン用の MRTK の構成</a>」のドキュメントに従って、適切な入力データ プロバイダー (**OpenXRDeviceManager** と **WindowsMixedRealityDeviceManager**) を設定します。

:::image type="content" source="./media/update-input-profile.png" alt-text="入力プロファイルを更新する":::

## <a name="build-deploy-and-run-the-app"></a>アプリをビルド、デプロイ、および実行する

これで、プロジェクトが Unity 2020 に完全にアップグレードされました。 [Unity HoloLens](get-started-unity-hololens.md) または [MRTK を使用した Unity HoloLens](get-started-unity-hololens-mrtk.md) のクイックスタートの手順に従って、アプリをビルド、デプロイ、実行します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [概念: SDK の概要](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [FAQ](../faq.md)

> [!div class="nextstepaction"]
> [変換 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
