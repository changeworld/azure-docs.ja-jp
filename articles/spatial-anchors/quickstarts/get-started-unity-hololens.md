---
title: クイック スタート - Azure Spatial Anchors を使用して HoloLens Unity アプリを作成する | Microsoft Docs
description: このクイック スタートでは、Spatial Anchors を使用して HoloLens アプリを Unity でビルドする方法について説明します。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b2bfec47bc92ebf5db1561d8fca33940dc376866
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752845"
---
# <a name="quickstart-create-a-hololens-unity-app-using-azure-spatial-anchors"></a>クイック スタート:Azure Spatial Anchors を使用して HoloLens Unity アプリを作成する

このクイック スタートでは、[Azure Spatial Anchors](../overview.md) を使用して HoloLens Unity アプリを作成する方法について説明します。 Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用する複合現実エクスペリエンスを作成できます。 完了すると、Unity でビルドされた、空間アンカーを保存して再呼び出しできる HoloLens アプリが作成されます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Spatial Anchors アカウントを作成する
> * Unity のビルド設定を準備する
> * Spatial Anchors アカウント識別子とアカウント キーを構成する
> * HoloLens Visual Studio プロジェクトをエクスポートする
> * HoloLens デバイスにデプロイして実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

- <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a>、<a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a>、および**ユニバーサル Windows プラットフォーム開発ワークロード**がインストールされた Windows マシン。
- [開発者モード](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio)が有効になっている HoloLens デバイス。
- **[ビルド設定]**->**[プレーヤーの設定]**->**[発行の設定]**->**[機能]** で、アプリに **SpatialPerception** 機能を設定する必要があります。
- **Windows Mixed Reality SDK** を使用して、**[ビルド設定]**->**[プレーヤーの設定]**->**[XR Settings]\(XR の設定\)** の **[Virtual Reality Supported]\(サポートされている仮想現実\)** をアプリで有効にする必要があります。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Unity でサンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Unity を開き、`Unity` フォルダーのプロジェクトを開きます。

**[ファイル]** -> **[ビルド設定]** を選択して、**[ビルド設定]** を開きます。

**[プラットフォーム]** セクションで、**[ユニバーサル Windows プラットフォーム]** を選択します。 次に、**[ターゲット デバイス]** を **[HoloLens]** に変更します。

**[Switch Platform]\(プラットフォームの切り替え\)** を選択して、プラットフォームを **[ユニバーサル Windows プラットフォーム]** に変更します。

![Unity のビルド設定](./media/get-started-unity-hololens/unity-build-settings.png)

**[ビルド設定]** ウィンドウを閉じます。

## <a name="configure-account-identifier-and-key"></a>アカウント識別子とキーを構成する

**[プロジェクト]** ウィンドウで `Assets/AzureSpatialAnchorsPlugin/Examples` に移動し、`AzureSpatialAnchorsBasicDemo.unity` シーン ファイル を開きます。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

**[ファイル]** -> **[保存]** を選択してシーンを保存します。

## <a name="export-the-hololens-visual-studio-project"></a>HoloLens Visual Studio プロジェクトをエクスポートする

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

**[ビルド]** を選択してダイアログを開きます。 次に、HoloLens Visual Studio プロジェクトのエクスポート先となるフォルダーを選択します。

エクスポートが完了すると、エクスポートされた HoloLens プロジェクトを含むフォルダーが表示されます。

## <a name="deploy-the-hololens-application"></a>HoloLens アプリケーションをデプロイする

フォルダーの `HelloAR U3D.sln` をダブルクリックして、Visual Studio でプロジェクトを開きます。

**[ソリューション構成]** を **[リリース]** に変更し、**[ソリューション プラットフォーム]** を **[x86]** に変更し、デプロイ ターゲット オプションから **[デバイス]** を選択します。

![Visual Studio の構成](./media/get-started-unity-hololens/visual-studio-configuration.png)

HoloLens デバイスの電源をオンにしてサインインし、USB ケーブルを使用して PC に接続します。

**[デバッグ]** > **[デバッグの開始]** を選択して、アプリのデプロイとデバッグを開始します。

アプリの指示に従って、アンカーを配置して再呼び出しします。

Visual Studio で、**[デバッグの停止]** を選択するか、**Shift + F5** キーを押して、アプリを停止します。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [チュートリアル:デバイス間で空間アンカーを共有する](../tutorials/tutorial-share-anchors-across-devices.md)
