<properties 
   pageTitle="Azure モバイル エンゲージメント関連のトラブルシューティング ガイド - サービス" 
   description="Azure モバイル エンゲージメント関連のトラブルシューティング ガイド" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>


# サービスの問題に関するトラブルシューティング ガイド

次に説明するのは、Azure モバイル エンゲージメントの実行方法に関して発生する可能性のある問題です。

## サービスの停止

### 問題
- Azure モバイル エンゲージメント サービスの停止に起因すると思われる問題です。

### 原因
- Azure モバイル エンゲージメント サービスの停止に起因すると思われる問題は、いくつかの異なる問題が原因で発生します。
    - すべての Azure モバイル エンゲージメントに元からシステム的に表れる単独の問題
    - サーバーの停止に起因する既知の問題 \(サーバー ステータスに表示されないことがあります\)。
	- スケジュールの遅延、ターゲティングのエラー、バッジ更新に関する問題、統計情報収集の停止、プッシュ機能の停止、API 機能の停止、新しいアプリやユーザーを作成できない、DNS エラー、デバイス上の UI、API、アプリのタイムアウト エラー。
    - クラウドの依存関係の停止 [Azure サービスの状態](http://status.azure.com/)、[Amazon Web Services \(AWS\) の状態](http://status.aws.amazon.com/) 
    - プッシュ通知サービス \(PNS\) の依存関係の停止 [Google - サービス](http://www.google.com/appsstatus#hl=en&v=status)、[Apple - サービス](http://www.apple.com/support/systemstatus/)、[Android - Google GCM](http://developer.android.com/google/gcm/index.html)、[Android - Amazon ADM](https://developer.amazon.com/appsandservices/apis/engage/device-messaging)、[Apple - APNS](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)、[Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx)、[Windows Phone - MPNS][LinkMPNS]、[Windows - WNS](https://developer.windows.com/)
    - アプリ ストアの停止[GooglePlay](https://play.google.com/)、[iTunes](http://www.apple.com/itunes/charts/)、[Windows Phone ストア](http://www.windowsphone.com/)、[Windows ストア](http://windows.microsoft.com/)

*問題がシステム的かどうかをテストするには、次のような異なる方法で同じ機能をテストできます。*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*問題が UI や API にのみ影響するかどうかをテストします。*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[API ドキュメント][Link 4]、[UI ドキュメント][Link 1]

*携帯電話ネットワークに問題があるかどうかをテストします。*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*デバイスに問題があるかどうかをテストします。*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[UI ドキュメント - 設定][Link 1]

*アプリに問題があるかどうかをテストします。*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

エンド ユーザー デバイスへの OS のアップグレードに問題があるかどうかをテストします。解決するには、SDK のアップグレードが必要です。

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[トラブルシューティング ガイド - SDK][Link 2]
 
## 接続と誤った情報に関する問題

### 問題
- Azure モバイル エンゲージメント UI にログインする際の問題。
- Azure モバイル エンゲージメント API との接続エラー。
- デバイス API を使用してアプリ情報タグをアップロードする際の問題。
- Azure モバイル エンゲージメントからログをダウンロードまたはデータをエクスポートする際の問題。
- 正しくない情報が Azure モバイル エンゲージメント UI に表示される。
- 正しくない情報が Azure モバイル エンゲージメント ログに表示される。

### 原因
- Azure モバイル エンゲージメントとの接続の問題:
    - ユーザー アカウントがタスクを実行するのに十分な権限があることを確認します。
    - 問題が 1 台のコンピューターまたはローカル ネットワークに分離されてないことを確認します。
    - Azure モバイル エンゲージメント サービスで障害が報告されていないことを確認します。
    - アプリ情報タグのファイルがすべてのルールに従っていることを確認します。
        - UTF8 文字セットのみを使用します \(ANSI 文字セットはサポートされていません\)。
        - 区切り文字としてコンマ「,」を使用します \(サービス要求を開いて、.csv の区切り文字をコンマ「,」からセミコロン「;」などの別の文字に変更するように要求できます\)。
        - ブール値の「TRUE」と「FALSE」には、すべて小文字を使用します。
        - 最大ファイル サイズの 35 MB よりも小さいファイルを使用します。

### 関連項目

[API ドキュメント][Link 4]、[UI ドキュメント - ホーム][Link 1]
 
## 機能に関する要求

### 問題
- 使用したい機能が、まだ Azure モバイル エンゲージメントに存在しません。

### 原因

まだ存在しない Azure モバイル エンゲージメントの新機能を提案するには、サービス要求を開き、Azure モバイル エンゲージメントで実現したい新機能について、できるだけ詳しく記入します。

### 関連項目

[モバイル エンゲージメント関連のフィードバックと機能に関する要望](http://feedback.azure.com/forums/285737-mobile-engagement)

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
[LinkMPNS]: http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx

<!--HONumber=54-->