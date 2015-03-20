<properties 
   pageTitle="Azure モバイル エンゲージメント ユーザー インターフェイス - 監視" 
   description="Azure モバイル エンゲージメントの監視セクションのユーザー インターフェイスの概要" 
   services="mobile-engagement" 
   documentationCenter="mobile" 
   authors="v-micada" 
   manager="mattgre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="mobile"
   ms.workload="required" 
   ms.date="02/17/2015"
   ms.author="v-micada"/>

# Azure モバイル エンゲージメント - ユーザー インターフェイス

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">はじめに</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">ナビゲーション</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">ホーム</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">マイ アカウント</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">分析</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">監視</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">リーチ</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">セグメント</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">ダッシュボード</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">設定</a>
</div>

# 監視
 
UI の [監視］ セクションは、リアルタイムの分析情報を提供します。ここでは、しきい値がこれまで UI の [分析] セクションで取得したのとほぼ同じ値に達したときに通知するように設定できます。「概念」の「用語集」では、次のような、分析と監視で使用される用語と省略形を定義しています。アクティブ ユーザー、新しいユーザー、保留ユーザー、セッション、ユーザー パス グラフ、ユーザー マップ、URL の追跡、傾向、アクティビティ、イベント、ジョブ、エラー、追加情報、クラッシュ、アプリ情報。

**関連項目:** 

-  [概念 - 用語集][Link 6]、[トラブルシューティング ガイド - 分析][Link 2]

## 監視 - セッション、ジョブ、イベント、エラー、クラッシュ:
セッション中のユーザー数、特定の画面上のユーザー数、特定の操作を行っているユーザー数を確認できます。ユーザー アクティビティを、セッション、ジョブ、イベント、エラー、クラッシュに分割して表示できます。現在の情報を確認し、過去の時間、日付、週の情報を表示できます。各カテゴリの情報をすべて表示し、特定のセッション、ジョブ、イベント、エラー、クラッシュごとに並べ替えることができます。ライブ監視は、プッシュ通知などのイベント中に使用すると、プッシュ通知の送信直後にアクションの増加があるかどうかを確認できるので便利です。 
 
![Monitor1][14]  

## 監視 - イベント - 詳細のトラブルシューティング:
テスト デバイスからアプリケーションのイベントを生成し、これを [監視] - [イベント] - [詳細] で検出することは、テスト デバイスのデバイス ID を検索し、分析、監視、セグメント化の Azure モバイル エンゲージメント統合が機能していることをアプリケーションから確認する最も簡単な方法の 1 つです。テスト デバイスのデバイス ID を検索したら、[マイ アカウント] - [デバイス] でこれをテスト デバイスに追加できます。イベントを生成できない場合は、Azure モバイル エンゲージメントが Android/iOS/Web/Windows/Windows Phone の各アプリで、SDK と正しく統合されているかご確認ください。

**関連項目:** 

-  [SDK ドキュメント][Link 5]

![Monitor2][15]  

## 監視 - クラッシュ - 詳細のトラブルシューティング:
[監視] - [クラッシュ] - [詳細] からアプリに関するクラッシュ情報を確認すると、アプリがクラッシュした理由を特定するのに役立ちます。Android/iOS/Web/Windows/Windows Phone 用の SDK の各バージョンのリリース ノートで、それぞれの既知の問題を確認する必要もあります。 

**関連項目:** 

-  [SDK ドキュメント - リリース ノート][Link 5]

![Monitor3][16] 

## 監視 - アラート:
メールやインスタント メッセージから自動的に送信されるようにアラートの条件を指定することもできます。(Google の GTalk や Apple の iChat などの XMPP 対応サービスがサポートされています)。アラートは、秒、分、時間あたりのセッション、ジョブ、イベント、エラー、クラッシュの特定の数値が事前に定義した検出しきい値より大きい (>) か、小さい (<) かに基づいています。アラートは、指定された種類のアクティビティをすべて監視することも、ジョブ、イベント、エラーの特定のアクティビティのみを監視することもできます。[Minimum Detection Rate (最小検出率)] を指定することもできます。これは、同じアラートを 2 つの通知に分割する最小時間で、アラートがトリガーされたときに、この間隔 (分) ごとに 2 つ以上の通知を受信しないようにします。
 
![Monitor4][17]

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
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
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
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
