---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753519"
---
## <a name="open-the-sample-project-in-unity"></a>Unity でサンプル プロジェクトを開く

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Android デバイス向けに設定するには

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>iOS デバイス向けに設定するには

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>アカウント識別子とキーを構成する

**[Project]\(プロジェクト\)** ウィンドウで `Assets/AzureSpatialAnchorsPlugin/Examples` に移動し、シーン ファイル `AzureSpatialAnchorsLocalSharedDemo.unity` を開きます。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

さらに、**[Inspector]\(インスペクター\)** ウィンドウで、`Base Sharing Url` の値として (ASP.NET Web アプリの Azure デプロイにある) `Sharing Anchors Service url` を入力し、`index.html` を `api/anchors` に置き換えます。 その結果、`https://<app_name>.azurewebsites.net/api/anchors` のようになります。

**[File]\(ファイル\)** -> **[Save]\(保存\)** の順に選択してシーンを保存します。

## <a name="to-deploy-to-an-android-device"></a>Android デバイスにデプロイするには

Android デバイスの電源をオンにしてサインインし、USB ケーブルを使用して PC に接続します。

**[File]\(ファイル\)** -> **[Build Settings]\(ビルド設定\)** の順に選択して、**[Build Settings]\(ビルド設定\)** を開きます。

**[Scenes In Build]\(ビルド中のシーン\)** で、`AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` シーンの横にチェック マークを付けて、他のすべてのシーンのチェック マークをクリアします。

**[Export Project]\(プロジェクトのエクスポート\)** チェック ボックスがオフになっていることを確認してください。 **[Build And Run]\(ビルドして実行\)** をクリックします。 `.apk` ファイルを保存するよう求められたら、任意の名前を選択できます。

アプリ内の手順に従います。 **[Create & Share Anchor]\(アンカーの作成と共有\)** または **[Locate Shared Anchor]\(共有されたアンカーの配置\)** を選択できます。 1 つ目のオプションを使用すると、同じまたは別のデバイスに後で配置できるアンカーを作成できます。 2 番目のオプションを使用すると、以前にアプリを実行したことがある場合に、既に共有されているアンカーを同じまたは別のデバイスに配置できます。

## <a name="to-deploy-to-an-ios-device"></a>iOS デバイスにデプロイするには

**[File]\(ファイル\)** -> **[Build Settings]\(ビルド設定\)** の順に選択して、**[Build Settings]\(ビルド設定\)** を開きます。

**[Scenes In Build]\(ビルド中のシーン\)** で、`AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` シーンの横にチェック マークを付けて、他のすべてのシーンのチェック マークをクリアします。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

アプリ内の手順に従います。 **[Create & Share Anchor]\(アンカーの作成と共有\)** または **[Locate Shared Anchor]\(共有されたアンカーの配置\)** を選択できます。 1 つ目のオプションを使用すると、同じまたは別のデバイスに後で配置できるアンカーを作成できます。 2 番目のオプションを使用すると、以前にアプリを実行したことがある場合に、既に共有されているアンカーを同じまたは別のデバイスに配置できます。

Xcode で、**[Stop]\(停止\)** を押してアプリを停止します。
