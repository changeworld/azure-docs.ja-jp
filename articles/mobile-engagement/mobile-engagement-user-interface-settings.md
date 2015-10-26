<properties 
   pageTitle="Azure モバイル エンゲージメント ユーザー インターフェイス - Settings (設定)" 
   description="Azure モバイル エンゲージメントを使用してアプリケーションのグローバル設定を管理する方法について説明します。" 
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
   ms.date="08/10/2015"
   ms.author="piyushjo"/>

# アプリケーションのグローバル設定を管理する方法
アプリケーションで利用可能な [Settings (設定)] メニューのオプションは、アプリケーションのプラットフォームやアプリケーションで付与されているユーザーの権限によって異なります。設定には、Details (詳細)、Projects (プロジェクト)、Native Push (ネイティブのプッシュ)、Push Speed (プッシュの速度)、SDK、Tracking (追跡)、App Info (アプリの情報)、Commercial Pressure (コマーシャル プレッシャー)、Permissions (権限)、の項目があります。UI の [Settings (設定)] セクションの [App Info (アプリの情報)] メニューのオプションのみ、Device API の "Tag" 機能で管理できる要素を含んでいます。「概念」の「用語集」には、APNS、GCM、IDFA、API、SDK、API キー、SDK キー、アプリケーション ID (App ID)、AppStore ID、タグ プラン、ユーザー ID、デバイス ID、App Delegate、スタック トレース、ディープ リンクなど、用語の定義と略語があります。

### 関連項目
- [API ドキュメント - Device API][Link 4]、[概念 - 用語集][Link 6]、[トラブルシューティング ガイド - サービス][Link 24]

  ![settings1][46]

## 詳細
アプリケーションの名前と説明を変更できます。アプリケーションの所有者とユーザーのロール権限を表示します。分析構成: 週の開始日、保管日数を表示、変更できます。
 
  ![settings2][47]
 
## プロジェクト
アプリケーションを表示するすべてのプロジェクトを選択できます。また、プロジェクトを検索したり、アプリケーションが包括されているプロジェクトの名前、説明、所有者、ユーザーの権限を表示したりすることもできます。

### 関連項目
-    [UI ドキュメント - ホーム][Link 13]
 
  ![settings3][48]

## Native Push (ネイティブのプッシュ)
ネイティブ プッシュで使用する新しい証明書を登録したり、既存の証明書を削除したりできます。ネイティブのプッシュでは、Azure モバイル エンゲージメントが実行していないときでもいつでもアプリケーションをプッシュできるようになります。少なくとも 1 つ以上のネイティブ プッシュ サービスの資格情報や証明書を指定すると、リーチ キャンペーンの作成時に [Any time (いつでも)] を選択できるようになり、また PUSH API で "notifier" パラメーターを使用できるようになります。

### 関連項目
- [API ドキュメント - Reach API][Link 4]、[API ドキュメント - Push API][Link 4]、[UI ドキュメント - Reach - 新しいプッシュ キャンペーン][Link 27]

### Apple プッシュ通知サービス (APNS)
Apple プッシュ通知サービスを使用してネイティブのプッシュを有効にするには、証明書の登録が必要です。証明書の種類を、開発 (DEV) または実稼働 (PROD) のいずれかに指定する必要があります。その後、証明書とパスワードをアップロードします。

### 関連項目
- [SDK ドキュメント - iOS - Apple プッシュ通知用にアプリケーションを準備する][Link 5]
 
![settings4][49]
 
### Windows プッシュ通知サービス (WPNS)
Windows 通知サービスを使用してネイティブのプッシュを有効にするには、アプリケーションの資格情報を指定する必要があります。パッケージ セキュリティ ID (SID) とシークレット キーが必要です。
 
![settings5][50]
 
### Google Cloud Messaging for Android (GCM)
GCM を使用してネイティブのプッシュを有効にするには、Google の指示に従う必要があります。その後、IP の制限なしで構成された、サーバーの SimpleAPI キーを貼り付ける必要があります。Android v1.12.0 以上では、SDK との統合が必要です。

### 関連項目
- [SDK ドキュメント - Android - Integrate GCM の統合方法][Link 5]、[Google 開発者 - GCM ガイド](http://developer.android.com/guide/google/gcm/gs.html)、[Google 開発者 - GCM ドキュメント](http://developer.android.com/google/gcm/index.html)
 
### 「Amazon Device Messaging for Android (ADM)
ADM を使用してネイティブのプッシュを有効にするには、クライアント ID とクライアント シークレットで構成される Amazon <OAuth credentials> を指定する必要があります (Android v2.1.0 以上では SDK との統合が必要)。

### 関連項目
- [SDK ドキュメント - Android - ADM の統合方法][Link 5]、[Amazon 開発者 - ADM ドキュメント](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## Push Speed (プッシュの速度)
アプリケーションの現在のプッシュ速度を表示したり、アプリケーションのプッシュ速度を定義したりできます。プッシュの速度は、Reach モジュールが 1 秒あたりに実行するプッシュの最大回数を定義します。この機能は、キャンペーンの開始後に 1 秒あたりのリクエストが多すぎたためにサーバーがオーバーロードした場合などに役立ちます。
 
  ![settings7][52]

## 追跡
追跡機能では、iOS や Android アプリケーションのインストール元を追跡できます。ユーザーがどこからアプリケーションをダウンロードしたか (どのアプリケーション ストアか)、そこへ誘導したソースはなにか (広告キャンペーン、ブログ、Web サイト、メール、SMS など) を把握ができます。Azure モバイル エンゲージメントの追跡機能は、別途 SDK からアプリケーションに統合する必要があります。

### 関連項目
- [SDK ドキュメント - Android - 統合方法][Link 5]、[SDK ドキュメント - iOS - 統合方法][Link 5]
 
### Stores (ストア)
アプリケーションをダウンロードできるすべてのストアを、名前とそれに関連付けられたダウンロード URL を基に登録できます。これで、追跡 URL をホストする場所を作成ができます。ストアはこのページで作成したり削除したりできます。アイコンを使用して新しい追跡 URL を作成すると、後述の 追跡 URL ページに移動します。ユーザーがダウンロードした場所を追跡するには数通りの方法があります:

-    サードパーティの Ad サーバーを使用する (Azure モバイル エンゲージメントでは現在 [SmartAd](http://smartadserver.fr/) と [Surikate](http://www.surikate.com/) をサポートしています)。
-    サードパーティの属性サービスを使用する (Azure モバイル エンゲージメントでは現在 [Mobile App Tracking](http://www.mobileapptracking.com/) をサポートしており、今後 [Trademob](http://www.trademob.com/) もサポート予定です)。
-    サードパーティの Install Referrer を使用する (Azure モバイル エンゲージメントでは現在 [Google Play Install Referrer](https://developers.google.com/app-conversion-tracking/docs/third-party-trackers/) - (Android のみ) をサポートしています)。
-    手動レポーティングを使用する。
 
  ![settings8][53]
 
### Ad Campaigns (広告キャンペーン)
Ad サーバー名、キャンペーン ID、アプリケーションのダウンロード元であるソースから成る新しい広告キャンペーンを作成できます。
 
  ![settings9][54]
 
### Tracking URLs (追跡 URL)
アプリケーションをダウンロードできるストアにユーザーをリダイレクトするソース (広告キャンペーン、ブログ、Web サイト、メール、SMS など) 内でターゲット URL として使用できる追跡 URL を構築できます。また、作成済みのすべての追跡　URL を表示することもできます。追跡 URL は、広告キャンペーンや、他のアプリからアプリをダウンロードするようユーザーに招待するリーチ アナウンスのアクション URL として使用することもできます。追跡 URLは、選択されたストアに関連付けられたダウンロード URL にリダイレクトし、追跡システムではインストール時にアプリケーションのダウンロード元であるストアを記録します。ソースが指定されている場合は、それも追跡システムに記録されるため、アプリケーション用に作成するさまざまな広告キャンペーン間での区別が容易になります。

新しい追跡 URL を作成するときは、ストアを指定し、ソースに None、Customer、Ad Server のいずれかを指定する必要があります。

-    None のソースでは、既定の追跡 URL を作成します。
-    Customer のソースでは、外部サーバーにアプリケーションをダウンロードできる URL を指定できます。
-    Ad Server のソースでは、既定の Ad Server に既定の追跡 URL を作成します。
 
### 関連項目
- [UI ドキュメント - Reach - 新しいのプッシュ キャンペーン][Link 27] 
 
### 追跡 URL を構築する
さらに、新しいリーチ キャンペーンのコンテンツ セクション内からアプリケーションをダウンロードできる追跡 URL を構築することもできます。

### 関連項目
- [UI ドキュメント - Reach - プッシュのコンテンツ][Link 29]

![settings10][55]

## アプリ情報
アプリケーションのユーザーに関連した追加情報を登録できます。この情報はアプリケーションから、またはバックエンドから (Device API を使用) 挿入できます。

### 関連項目
- [API ドキュメント - Device API][Link 4]

アプリケーション情報のタグを登録すると、リーチ対象ユーザーの特定の条件を作成してリーチ キャンペーンを区分ができます。既存のアプリ情報のタグの名前と種類を表示したり、文字列、日付、整数、ブール値の名前と種類に基づいて新しいアプリ情報を追加したりできます。

### 関連項目
- [UI ドキュメント - Reach - 新しいのプッシュ キャンペーン][Link 27]
 
![settings11][56]
 
## Commercial Pressure (コマーシャル プレッシャー)
プッシュ クォータでは、一定期間にプッシュを受けることのできる最大回数を定義できます。プッシュ クォータは、[Apply push quotas (プッシュ クォータを適用] オプションが有効になっているリーチ キャンペーンでのみ使用できます。プッシュ クォータは、セグメントごとで管理したり、既定の内容で管理したりできます。クォータでは、1 時間、1 日、1 週間、1 か月にプッシュの最大回数のみを送信するよう設定できます。

### 関連項目
- [UI ドキュメント - Reach - 新しいプッシュ キャンペーン][Link 27]、[UI ドキュメント - セグメント][Link 18]

### Quotas (クォータ):
- Quota by default (既定のクォータ): このクォータは、後述の「Quota by segment」セクションのセグメントに一致しないユーザーに適用されます。既定ではクォータは設定されていません。
- Quota by Segment (セグメントのクォータ): クォータはユーザーのグループに適用する必要があるため、クォータの適用先であるユーザーを特定するセグメントを作成する必要があります。たとえば、実行するセッション数で定義した「ヘビー ユーザー」など、ユーザー グループとして定義したいものを自由に設定できます。1 つのデバイスがクォータの定義されている複数のセグメントに一致する場合、1 時間あたりの最大プッシュ回数が最も多いクォータが適用されます。

![settings12][57]
 
## アクセス許可
アプリケーションのユーザーのメール、名前、組織、権限レベルを検索、表示できます。権限の概念は、プロジェクトのロールに追加され、アプリケーションへのアクセス権を持つユーザーに対して 1 つの権限のセットを関連付けることができます。

### 現在利用可能な権限レベル:
-    リーチ キャンペーン作成者 (リーチ キャンペーンを作成できるユーザー) 
-    リーチ キャンペーン管理者 (リーチ キャンペーンを作成、アクティブ化、中断、完了、破棄できるユーザー)

> 注: ユーザーがプロジェクト ロールとアプリケーションの権限セットの両方を保持している場合は、より制限のない概念が使用されます。したがって、権限を使用する際は、ユーザーのプロジェクト ロールを [Viewer (閲覧者)] (最も低い権限) に設定し、アプリケーション レベルでより制限の少ない権限を追加することをお勧めします。

### 関連項目
- [UI ドキュメント - ホーム][Link 13]  
 
![settings13][58]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 

<!---HONumber=Oct15_HO3-->