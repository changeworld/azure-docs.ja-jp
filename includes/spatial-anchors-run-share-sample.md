---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305158"
---
## <a name="set-up-your-device"></a>デバイスのセットアップ

### <a name="set-up-an-android-device"></a>Android デバイスのセットアップ

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>iOS デバイスのセットアップ

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>アカウント識別子とキーの構成

**[Project]\(プロジェクト\)** ウィンドウで `Assets/AzureSpatialAnchorsPlugin/Examples` に移動し、シーン ファイル `AzureSpatialAnchorsLocalSharedDemo.unity` を開きます。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

**[Inspector]\(インスペクター\)** ウィンドウで、`Base Sharing Url` の値として (ASP.NET Web アプリの Azure デプロイにある) `Sharing Anchors Service url` を入力し、`index.html` を `api/anchors` に置き換えます。 `https://<app_name>.azurewebsites.net/api/anchors` のようになります。

**[File]\(ファイル\)** > **[Save]\(保存\)** の順に選択してシーンを保存します。

## <a name="to-deploy-the-app-to-an-android-device"></a>アプリを Android デバイスに配置するには

Android デバイスにサインインし、USB ケーブルを使用してコンピューターに接続します。

**[ファイル]** > **[ビルド設定]** を選択して、**[ビルド設定]** を開きます。

**[Scenes In Build]\(ビルド中のシーン\)** で、`AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` シーンの横にチェック マークを付けて、他のすべてのシーンのチェック マークをクリアします。

**[Export Project]\(プロジェクトのエクスポート\)** がオフであることを確認します。 **[Build And Run]\(ビルドして実行\)** を選択します。 `.apk` ファイルを保存するように求められます。 任意の名前を選択できます。

アプリ内の手順に従います。 **[Create & Share Anchor]\(アンカーの作成と共有\)** または **[Locate Shared Anchor]\(共有されたアンカーの配置\)** を選択できます。 1 つ目のオプションを使用すると、同じデバイスまたは別のデバイスに後で配置できるアンカーを作成できます。 2 つ目のオプションを使用すると、アプリを既に実行したことがある場合に、以前に共有したアンカーを同じまたは別のデバイスに配置できます。

## <a name="to-deploy-the-app-to-an-ios-device"></a>アプリを iOS デバイスに配置するには

**[ファイル]** > **[ビルド設定]** を選択して、**[ビルド設定]** を開きます。

**[Scenes In Build]\(ビルド中のシーン\)** で、`AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` シーンの横にチェック マークを付けて、他のすべてのシーンのチェック マークをクリアします。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

アプリ内の手順に従います。 **[Create & Share Anchor]\(アンカーの作成と共有\)** または **[Locate Shared Anchor]\(共有されたアンカーの配置\)** を選択できます。 1 つ目のオプションを使用すると、同じデバイスまたは別のデバイスに後で配置できるアンカーを作成できます。 2 つ目のオプションを使用すると、アプリを既に実行したことがある場合に、以前に共有したアンカーを同じまたは別のデバイスに配置できます。

Xcode で、**[Stop]\(停止\)** を選択してアプリを停止します。
