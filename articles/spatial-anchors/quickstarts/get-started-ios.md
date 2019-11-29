---
title: クイック スタート:iOS アプリの作成
description: このクイック スタートでは、Spatial Anchors を使用する iOS アプリを構築する方法について説明します。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 39732ef785fc610572f917be77dec3f560698fb7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277203"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>クイック スタート:Azure Spatial Anchors を使用する iOS アプリを Swift または Objective-C で作成する

このクイック スタートでは、[Azure Spatial Anchors](../overview.md) を使用する iOS アプリを Swift または Objective-C で作成する方法について説明します。 Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、空間アンカーを保存して再呼び出しできる ARKit iOS アプリが作成されます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Spatial Anchors アカウントを作成する
> * Spatial Anchors アカウント識別子とアカウント キーを構成する
> * iOS デバイスにデプロイして実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

- 最新バージョンの <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> と <a href="https://cocoapods.org" target="_blank">CocoaPods</a> がインストールされた、開発者向けの macOS コンピューター。
- HomeBrew を介してインストールされた Git。 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` コマンドをターミナルに 1 行で入力します。 次に、`brew install git` を実行します。
- 開発者向けの <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit 対応</a> iOS デバイス。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>サンプル プロジェクトを開く

ターミナルを使用して、以下のアクションを実行します。

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

CocoaPods を使用して必要なポッドをインストールします。

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

`iOS/Swift/` に移動します。

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

`iOS/Objective-C/` に移動します。

```bash
cd ./iOS/Objective-C/
```

---

`pod install --repo-update` を実行して、プロジェクトの CocoaPods をインストールします。

Xcode で `.xcworkspace` を開きます。

> [!NOTE]
> macOS Catalina (10.15) にアップグレードした後に CocoaPod に関する問題が発生している場合は、[こちら](#cocoapods-issues-on-macos-catalina-1015)のトラブルシューティングの手順を参照してください。

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>アカウント識別子とキーを構成する

次に、自分のアカウント識別子とアカウント キーを使用するようにアプリを構成します。 これらの情報は、[Spatial Anchors リソースを設定](#create-a-spatial-anchors-resource)するときにテキスト エディターにコピーしました。

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

`iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`を開きます。

`spatialAnchorsAccountKey` フィールドを見つけ、`Set me` をアカウント キーに置き換えます。

`spatialAnchorsAccountId` フィールドを見つけ、`Set me` をアカウント識別子に置き換えます。

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

`iOS/Objective-C/SampleObjC/BaseViewController.m`を開きます。

`SpatialAnchorsAccountKey` フィールドを見つけ、`Set me` をアカウント キーに置き換えます。

`SpatialAnchorsAccountId` フィールドを見つけ、`Set me` をアカウント識別子に置き換えます。

---

## <a name="deploy-the-app-to-your-ios-device"></a>アプリを iOS デバイスにデプロイする

iOS デバイスを Mac に接続し、**アクティブ スキーム**を iOS デバイスに設定します。

![デバイスを選択する](./media/get-started-ios/select-device.png)

**[Build and then run the current scheme]\(ビルドしてから現在のスキームを実行する\)** を選択します。

![デプロイして実行する](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> `library not found for -lPods-SampleObjC` エラーが表示される場合は、`.xcworkspace` ではなく `.xcodeproj` ファイルを開いた可能性があります。 `.xcworkspace` を開き、もう一度試してください。

Xcode で、 **[Stop]\(停止\)** を押してアプリを停止します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>macOS Catalina (10.15) での CocoaPods に関する問題

最近 macOS Catalina (10.15) に更新し、そのとき事前に CocoaPods がインストールされていると、CocoaPods が破損した状態になり、ポッドや `.xcworkspace` プロジェクト ファイルを正しく構成できないことがあります。 この問題を解決するには、次のコマンドを実行して CocoaPods を再インストールする必要があります。

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [チュートリアル:デバイス間で Spatial Anchors を共有する](../tutorials/tutorial-share-anchors-across-devices.md)
