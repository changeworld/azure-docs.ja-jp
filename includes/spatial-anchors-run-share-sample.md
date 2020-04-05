---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "72882301"
---
## <a name="android"></a>[Android](#tab/Android)

Java Android サンプルでは、デバイス間での共有がサポートされています。
Android Studio の samples フォルダーから `SharedActivity.java` ファイルを開きます。 `SharedActivity.java` ファイルの `SharingAnchorsServiceUrl` の値として、前の手順で (ASP.NET Web アプリの Azure デプロイから) 取得した URL を入力します。 URL の `index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Objective-C iOS サンプルでは、デバイス間での共有がサポートされています。
samples フォルダー内の `SharedDemoViewController.m` ファイルを開きます。 `SharedActivity.java` ファイルの `SharingAnchorsServiceUrl` の値として、前の手順で (ASP.NET Web アプリの Azure デプロイから) 取得した URL を入力します。 URL の `index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Xamarin Android および iOS の両方のサンプルで、デバイス間での共有がサポートされています。
samples フォルダー内の `AccountDetails.cs` ファイルを開きます。 `SharedActivity.java` ファイルの `AnchorSharingServiceUrl` の値として、前の手順で (ASP.NET Web アプリの Azure デプロイから) 取得した URL を入力します。 URL の `index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Android デバイスのセットアップ

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>iOS デバイスのセットアップ

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>アカウント識別子とキーの構成

**[Project]\(プロジェクト\)** ウィンドウで `Assets/AzureSpatialAnchorsPlugin/Examples` に移動し、シーン ファイル `AzureSpatialAnchorsLocalSharedDemo.unity` を開きます。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

**[Project]\(プロジェクト\)** ウィンドウで `Assets\AzureSpatialAnchors.Examples\Resources` に移動します。 [`SpatialAnchorSamplesConfig`] を選択します。 さらに、 **[Inspector]\(インスペクター\)** ウィンドウで、`Base Sharing Url` の値として (ASP.NET Web アプリの Azure デプロイにある) `Sharing Anchors Service url` を入力し、`index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

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

Xcode で、 **[Stop]\(停止\)** を選択してアプリを停止します。
