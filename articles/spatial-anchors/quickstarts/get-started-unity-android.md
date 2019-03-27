---
title: クイック スタート - Azure Spatial Anchors を使用する Android Unity アプリを作成する | Microsoft Docs
description: このクイック スタートでは、Spatial Anchors を使用する Android アプリを Unity でビルドする方法について説明します。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e92c812ffc8b72fe79248c602e48ff01ef9fefcb
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961016"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>クイック スタート:Azure Spatial Anchors を使用する Android Unity アプリを作成する

このクリック スタートでは、[Azure Spatial Anchors](../overview.md) を使用する Android Unity アプリを作成する方法について説明します。 Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、Unity でビルドされた、空間アンカーを保存して呼び戻すことができる ARCore Android アプリが作成されます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Spatial Anchors アカウントを作成する
> * Unity のビルド設定を準備する
> * ARCore SDK for Unity をダウンロードしてインポートする
> * Spatial Anchors アカウント識別子とアカウント キーを構成する
> * Android Studio プロジェクトをエクスポートする
> * Android デバイスに配置して実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

- <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 以降</a>および <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 以降</a>がインストールされている Windows または macOS コンピューター。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">開発者向けオプションが有効化</a>されている、<a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 対応</a>の Android デバイス。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Unity でサンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>アカウント識別子とキーを構成する

**[Project]\(プロジェクト\)** ウィンドウで `Assets/AzureSpatialAnchorsPlugin/Examples` に移動し、シーン ファイル `AzureSpatialAnchorsBasicDemo.unity` を開きます。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

**[File]\(ファイル\)** -> **[Save]\(保存\)** の順に選択してシーンを保存します。

## <a name="export-the-android-studio-project"></a>Android Studio プロジェクトをエクスポートする

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

**[Export]\(エクスポート\)** を選択してダイアログを開きます。 次に、Android Studio プロジェクトのエクスポート先となるフォルダーを選択します。

エクスポートが完了すると、エクスポートした Android Studio プロジェクトと、**HelloAR U3D** という名前のサブフォルダーが含まれる、フォルダーが表示されます。

## <a name="deploy-the-android-application"></a>Android アプリケーションを配置する

Android Studio を開き、**[Open an existing Android Studio project]\(既存の Android Studio プロジェクトを開く\)** を選択します。 次に、エクスポートした Android Studio プロジェクトから **HelloAR U3D** サブフォルダーを選択し、**[OK]** をクリックします。

開くとき、Gradle ラッパーを使用するよう求めるメッセージが表示されます。 **[OK]** を選択して Gradle ラッパーを使用し、プロジェクトを開きます。

Android デバイスの電源をオンにしてサインインし、USB ケーブルを使用して PC に接続します。

Android Studio のツール バーから **[Run]\(実行\)** を選択します。

![Android Studio での配置と実行](./media/get-started-unity-android/android-studio-deploy-run.png)

**[Select Deployment Target]\(配置ターゲットの選択\)** ダイアログで Android デバイスを選択し、**[OK]** を選択して Android デバイス上でアプリを実行します。

アプリの指示に従って、アンカーを配置し、呼び戻します。

> [!NOTE]
> アプリを実行したとき、背景としてカメラが表示されない場合は (たとえば、代わりに空白、青、または他のテクスチャが表示される場合)、Unity に資産を再インポートすることが必要な場合があります。 アプリを停止します。 Unity の上部のメニューで、**[Assets]\(資産\) -> [Reimport all]\(すべて再インポート\)** を選択します。 その後、アプリをもう一度実行します。

Android Studio ツール バーの **[Stop]\(停止\)** を選択して、アプリを停止します。

![Android Studio での停止](./media/get-started-unity-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [チュートリアル:デバイス間で Spatial Anchors を共有する](../tutorials/tutorial-share-anchors-across-devices.md)
