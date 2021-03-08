---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 9554e04c82299016076f09f85f604af10a94ab4a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185414"
---
## <a name="android"></a>[Android](#tab/Android)

Java Android サンプルでは、デバイス間での共有がサポートされています。

Android Studio でサンプル フォルダーから *SharedActivity.java* ファイルを開きます。 

*SharedActivity.java* ファイルの `SharingAnchorsServiceUrl` の値として、前の手順で (ASP.NET Web アプリの Azure デプロイから) コピーした URL を入力します。 

URL の `index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Objective-C iOS サンプルでは、デバイス間での共有がサポートされています。

[samples] フォルダーの *SharedDemoViewController.m* ファイルを開きます。 

*SharedDemoViewController.m* ファイルの `SharingAnchorsServiceUrl` の値として、前の手順で (ASP.NET Web アプリの Azure デプロイから) 取得した URL を入力します。 

URL の `index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

アプリをデバイスにデプロイします。 

アプリが起動したら、 **[Tap to start Shared Demo]\(タップして共有されたデモを開始する\)** オプションを選択し、アプリの指示に従います。 **[Tap to locate Anchor by its anchor number]\(タップしてアンカーをその番号で検索する\)** または **[Tap to create Anchor and save it to the service]\(タップしてアンカーを作成し、サービスに保存する\)** を選択できます。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Xamarin Android および iOS の両方のサンプルで、デバイス間での共有がサポートされています。

[samples] フォルダーの *AccountDetails.cs* ファイルを開きます。 

*AccountDetails.cs* ファイルの `AnchorSharingServiceUrl` の値として、前の手順で (ASP.NET Web アプリの Azure デプロイから) 取得した URL を入力します。 

URL の `index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android デバイスのセットアップ

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>iOS デバイスのセットアップ

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

**[プロジェクト]** ウィンドウで `Assets\AzureSpatialAnchors.Examples\Resources` に進みます。 

**[SpatialAnchorSamplesConfig]** を選択します。 次に、 **[インスペクター]** ウィンドウで、`Base Sharing Url` の値として (ASP.NET Web アプリの Azure デプロイにある) `Sharing Anchors Service` URL を入力します。 `index.html` を `api/anchors` で置き換え `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

**[File]\(ファイル\)**  >  **[Save]\(保存\)** の順に選択してシーンを保存します。

## <a name="deploy-to-your-device"></a>デバイスにデプロイする

### <a name="deploy-to-an-android-device"></a>Android デバイスにデプロイする

Android デバイスにサインインし、USB ケーブルを使用してコンピューターに接続します。

**[ファイル]**  >  **[ビルド設定]** を選択して、 **[ビルド設定]** を開きます。

**[Scenes In Build]\(ビルド内のシーン\)** で、すべてのシーンの横にチェック マークが付いていることを確認します。

**[Export Project]\(プロジェクトのエクスポート\)** がオフであることを確認します。 **[Build And Run]\(ビルドして実行\)** を選択します。 *.apk* ファイルを保存するように求められます。 任意の名前を選択できます。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>iOS デバイスにデプロイする

**[ファイル]**  >  **[ビルド設定]** を選択して、 **[ビルド設定]** を開きます。

**[Scenes In Build]\(ビルド内のシーン\)** で、すべてのシーンの横にチェック マークが付いていることを確認します。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Xcode で、**[Stop]\(停止\)** を選択してアプリを停止します。
