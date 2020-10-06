---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: efd5ff494620d4fab3fb904d9bcf054b57a3290b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358787"
---
## <a name="android"></a>[Android](#tab/Android)

Java Android サンプルでは、デバイス間での共有がサポートされています。
Android Studio の samples フォルダーから `SharedActivity.java` ファイルを開きます。 `SharedActivity.java` ファイルの `SharingAnchorsServiceUrl` の値として、前の手順で (ASP.NET Web アプリの Azure デプロイから) 取得した URL を入力します。 URL の `index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Objective-C iOS サンプルでは、デバイス間での共有がサポートされています。
samples フォルダー内の `SharedDemoViewController.m` ファイルを開きます。 `SharedDemoViewController.m` ファイルの `SharingAnchorsServiceUrl` の値として、前の手順で (ASP.NET Web アプリの Azure デプロイから) 取得した URL を入力します。 URL の `index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

アプリをデバイスにデプロイします。 アプリが起動したら、 **[Tap to start Shared Demo]\(タップして共有されたデモを開始する\)** オプションを選択します。 アプリ内の手順に従います。 **[Tap to locate Anchor by its anchor number]\(タップしてアンカーをその番号で検索する\)** または **[Tap to create Anchor and save it to the service]\(タップしてアンカーを作成し、サービスに保存する\)** を選択できます。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Xamarin Android および iOS の両方のサンプルで、デバイス間での共有がサポートされています。
samples フォルダー内の `AccountDetails.cs` ファイルを開きます。 `AccountDetails.cs` ファイルの `AnchorSharingServiceUrl` の値として、前の手順で (ASP.NET Web アプリの Azure デプロイから) 取得した URL を入力します。 URL の `index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android デバイスのセットアップ

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>iOS デバイスのセットアップ

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

**[Project]\(プロジェクト\)** ウィンドウで `Assets\AzureSpatialAnchors.Examples\Resources` に移動します。 [`SpatialAnchorSamplesConfig`] を選択します。 さらに、**[Inspector]\(インスペクター\)** ウィンドウで、`Base Sharing Url` の値として (ASP.NET Web アプリの Azure デプロイにある) `Sharing Anchors Service url` を入力し、`index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

**[File]\(ファイル\)**  >  **[Save]\(保存\)** の順に選択してシーンを保存します。

## <a name="deploy-to-your-device"></a>デバイスにデプロイする

### <a name="deploy-to-android-device"></a>Android デバイスにデプロイする

Android デバイスにサインインし、USB ケーブルを使用してコンピューターに接続します。

**[ファイル]**  >  **[ビルド設定]** を選択して、 **[ビルド設定]** を開きます。

**[Scenes In Build]\(ビルド内のシーン\)** で、すべてのシーンの横にチェック マークが付いていることを確認します。

**[Export Project]\(プロジェクトのエクスポート\)** がオフであることを確認します。 **[Build And Run]\(ビルドして実行\)** を選択します。 `.apk` ファイルを保存するように求められます。 任意の名前を選択できます。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>iOS デバイスにデプロイする

**[ファイル]**  >  **[ビルド設定]** を選択して、 **[ビルド設定]** を開きます。

**[Scenes In Build]\(ビルド内のシーン\)** で、すべてのシーンの横にチェック マークが付いていることを確認します。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

Xcode で、**[Stop]\(停止\)** を選択してアプリを停止します。
