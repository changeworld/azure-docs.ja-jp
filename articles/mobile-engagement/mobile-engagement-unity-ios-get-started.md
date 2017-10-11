---
title: "Unity iOS 展開用 Azure Mobile Engagement の概要します。"
description: "Unity アプリを iOS デバイスに展開するため、分析とプッシュ通知で Azure Mobile Engagement を使用する方法を説明します。"
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c8f50404771965ec636065346ac04e059d264c3d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Unity iOS 展開用 Azure Mobile Engagement の概要します。
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用して、アプリの使用量を把握する方法と、プッシュ通知を送信する方法は、iOS デバイスに展開する場合に Unity アプリケーションのユーザーをセグメント化します。
このチュートリアルではクラシック Unity ロール ボール チュートリアルの開始点として。 この手順を行う必要があります[チュートリアル](mobile-engagement-unity-roll-a-ball.md)おわかり以下のチュートリアルでは、Mobile Engagement 統合に進む前にします。 

このチュートリアルでは、次の項目が必要です。

* [Unity エディター](http://unity3d.com/get-unity)
* [モバイル エンゲージメント Unity SDK](https://aka.ms/azmeunitysdk)
* XCode エディター

> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントを持っていない場合は、ほんの数分で無料の試用アカウントを作成できます。 詳細については、「 [Azure 無料評価版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started)です。
> 
> 

## <a id="setup-azme"></a>IOS アプリ用の Mobile Engagement をセットアップします。
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Mobile Engagement バックエンドにアプリを接続します。
### <a name="import-the-unity-package"></a>Unity パッケージをインポートします。
1. ダウンロード、 [Mobile Engagement Unity パッケージ](https://aka.ms/azmeunitysdk)し、ローカル コンピューターに保存します。 
2. 移動して**アセット パッケージのインポート]-> [カスタム パッケージ]-> [**上記の手順でダウンロードしたパッケージを選択します。 
   
    ![][70] 
3. すべてのファイルが選択されており、クリックを確認してください**インポート**ボタンをクリックします。 
   
    ![][71] 
4. インポートが正常に実行すると、プロジェクトにインポートする SDK ファイルが表示されます。  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>更新プログラム、EngagementConfiguration
1. 開き、 **EngagementConfiguration** SDK のフォルダーと更新プログラムからスクリプト ファイル、 **IOS\_接続\_文字列**Azure ポータルから取得した接続文字列を使用します。  
   
    ![][73]
2. ファイルを保存します。 

### <a name="configure-the-app-for-basic-tracking"></a>基本的な追跡用のアプリを構成します。
1. 開き、 **PlayerController**スクリプト オブジェクトにアタッチ、Player を編集するためです。 
2. 次の using ステートメントを追加します。
   
        using Microsoft.Azure.Engagement.Unity;
3. 次の追加、`Start()`メソッド
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>展開し、アプリを実行します。
1. IOS デバイスをコンピューターに接続します。 
2. 開いている**ファイルにビルドの詳細設定]-> [** 
   
    ![][40]
3. 選択**iOS**し、**スイッチ プラットフォーム**
   
    ![][41]
   
    ![][42]
4. をクリックして**Player の設定**し、有効なバンドル Id を提供します。 
   
    ![][53]
5. 最後にをクリックして**ビルドと実行**
   
    ![][54]
6. IOS パッケージを格納するフォルダーの名前を指定するように求められます。 
   
    ![][43]
7. すべてが正常に出るとする場合は、プロジェクトはコンパイルされ、XCode アプリケーション開く必要があります。 
8. 確認して、**バンドル id**がプロジェクトに正しい。  
   
    ![][75]
9. XCode で、パッケージが接続されているデバイスに展開できるようにアプリを今すぐ実行し、電話で Unity ゲームが表示されます。 

## <a id="monitor"></a>リアルタイム監視をアプリに接続します。
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>プッシュ通知やアプリ内メッセージングが有効にします。
Mobile Engagement では、ユーザーと対話することができ、プッシュ通知とアプリ内メッセージング キャンペーンのコンテキストで使用します。 このモジュールには、Mobile Engagement ポータルで REACH は呼び出されます。
通知を受信するアプリの追加の構成を実行する必要はありませんし、そのセットアップでは既にです。

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
