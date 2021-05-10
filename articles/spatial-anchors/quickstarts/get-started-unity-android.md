---
title: クイック スタート:Unity Android アプリの作成
description: このクイック スタートでは、Spatial Anchors を使用する Android アプリを Unity でビルドする方法について説明します。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4f4bfdb5d09da6a9967b53ca56bb2491976592a1
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105886"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>クイック スタート:Azure Spatial Anchors を使用して Unity Android アプリを作成する

このクイック スタートでは、[Azure Spatial Anchors](../overview.md) を使用して Unity Android アプリを作成する方法について説明します。 Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、Unity でビルドされた、空間アンカーを保存して呼び戻すことができる ARCore Android アプリが作成されます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Spatial Anchors アカウントを作成する
> * Unity のビルド設定を準備する
> * Spatial Anchors アカウント識別子とアカウント キーを構成する
> * Android Studio プロジェクトをエクスポートする
> * Android デバイスに配置して実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

- **Android Build Support**、**Android SDK & NDK Tools**、**OpenJDK** モジュールを含む <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a> がインストールされている Windows または macOS マシン。 **Unity 2020 LTS** を ASA SDK バージョン 2.9 以降 ([Unity XR Plug-in Framework](https://docs.unity3d.com/Manual/XRPluginArchitecture.html) が使用される) で、または **Unity 2019 LTS** を ASA SDK バージョン 2.8 以前で使用します。
  - Windows で実行する場合は、<a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> および <a href="https://git-lfs.github.com/">Git LFS</a> も必要になります。
  - MacOS で実行する場合は、HomeBrew から Git をインストールします。 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` コマンドをターミナルに 1 行で入力します。 続けて、`brew install git` および `brew install git-lfs` を実行します。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">開発者向け</a>の <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 対応</a> Android デバイス。
  - コンピューターが Android デバイスと通信するには、追加のデバイス ドライバーが必要になることがあります。 追加情報と手順については、[こちら](https://developer.android.com/studio/run/device.html)を参照してください。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Unity のサンプル プロジェクトをダウンロードして開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[こちら](../how-tos/setup-unity-project.md#download-asa-packages)の手順に従って、Android プラットフォームに必要な ASA SDK パッケージをダウンロードし、インポートします。

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Android Studio プロジェクトをエクスポートする

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

**[Run Device]\(実行デバイス\)** でデバイスを選択し、 **[ビルドして実行]** を選択します。 `.apk` ファイルを保存するよう求められたら、任意の名前を選択できます。

アプリで矢印を使用して **[BasicDemo]** を選択し、 **[実行]**  ボタンを 押してデモを実行します。 指示に従って、アンカーを配置し、呼び戻します。

![スクリーンショット 1](./media/get-started-unity-android/screenshot-1.jpg)
![スクリーンショット 2](./media/get-started-unity-android/screenshot-2.jpg)
![スクリーンショット 3](./media/get-started-unity-android/screenshot-3.jpg)

アプリの指示に従って、アンカーを配置し、呼び戻します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="rendering-issues"></a>レンダリングに関する問題

アプリを実行したとき、背景としてカメラが表示されない場合は (たとえば、代わりに空白、青、または他のテクスチャが表示される場合)、Unity に資産を再インポートすることが必要な場合があります。 アプリを停止します。 Unity の上部のメニューで、 **[Assets]\(資産\) -> [Reimport all]\(すべて再インポート\)** を選択します。 その後、アプリをもう一度実行します。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [チュートリアル:デバイス間で Spatial Anchors を共有する](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [方法: Unity プロジェクトで Azure Spatial Anchors を構成する](../how-tos/setup-unity-project.md)
