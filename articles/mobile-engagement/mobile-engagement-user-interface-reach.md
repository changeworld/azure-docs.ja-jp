<properties 
   pageTitle="Azure モバイル エンゲージメント ユーザー インターフェイス - Reach (リーチ)" 
   description="Azure モバイル エンゲージメントでプッシュ通知を利用してアプリケーションのユーザーに通知する方法について説明します。" 
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


# プッシュ通知を利用してアプリケーションのユーザーに通知する方法
UI の Reach (リーチ) セクションは、プッシュ キャンペーン管理ツールで、プッシュ通知キャンペーンや機能を作成、編集、アクティブ化、完了、モニターしたり、統計を取得したりできます。また、Reach API (と低レベルのプッシュ API の一部の要素) 経由でアクセスすることも可能です。API と UI のいずれを使用する場合でも、リーチ キャンペーンを使用する前に、Azure モバイル エンゲージメントと Reach の両方を、SDK を使用する各プラットフォーム用のアプリケーションに統合する必要があります。

### 関連項目
-  [API ドキュメント - Reach API][Link 4], [API ドキュメント - Push API][Link 4], [トラブルシューティングガイド - Push/Reach][Link 23]
-  [リーチ - キャンペーン][Link 27]、[リーチ - 条件][Link 28]、[リーチ - コンテンツ][Link 29]、[リーチ - 方法][Link 3]
 
## 4 種類のプッシュ通知
1.    アナウンス - ユーザーに広告メッセージを送信してアプリ内の他の場所へリダイレクトしたり、アプリ外の Web ページやストアに誘導したりできます。 
2.    アンケート - ユーザーに質問を投げかけることでエンド ユーザーからの情報を収集できます。
3.    データ プッシュ- バイナリのデータ ファイルや base 64 のデータ ファイルを送信できます。データ プッシュに含まれる情報はアプリケーションに送信され、ユーザーの現在のアプリの使用状況を修正できます。アプリケーション側でデータ プッシュのデータを処理する機能を備えている必要があります。
4.    タイル (Windows Phone のみ) - Microsoft Push Notification Service (MPNS) を使用して、XML データを含むネイティブの Windows Push を送信できます(SDK バージョン 0.9.0 以降でサポート。タイルの最終的なペイロードは 32 キロバイト以下である必要があります)。

### 関連項目
-  [概念 - 用語集][Link 6]

## 各キャンペーンで表示されるリアルタイム統計の 3 つのカテゴリ
1.    Pushed (プッシュ済み) - キャンペーンで指定された条件に基づいて送信されたプッシュ数。 
2.    Replied (反応) - 通知をアプリ外で開いたり、アプリ内で閉じるなどの通知に対して行われたユーザーの反応の数。 
3.    Actioned (アクション) - ユーザーが通知のリンクをクリックしてアプリの新しい場所、ストア、Web ブラウザーにリダイレクトされた回数。 

> 注: キャンペーンの詳細な統計情報は、Reach API Stats 経由で確認できます。

### 関連項目
-  [概念 - 用語集][Link 6]、[API ドキュメント - Reach API - Stats][Link 4]


## キャンペーンの詳細
キャンペーンの名前の上にカーソルを合わせるか、クリックして開くとキャンペーンを編集、複製、削除したり、まだアクティブ化されていないキャンペーンをアクティブ化したりできます。すでにアクティブなキャンペーンの名前の上にカーソルを合わせるか、クリックして開くとそのキャンペーンの複製を作成できますが、一度アクティブ化したキャンペーンを変更することはできません。
 
![Reach1][18]

## Reach のフィードバック
すでにアクティブなキャンペーンの詳細ビューから統計ビューに切り替えて、統計の簡易ビューから詳細ビューに切り替えると、詳細情報を確認できます (保有している権限に基づきます)。過去のキャンペーンからのリーチのフィードバック情報を新しいキャンペーンの対象条件として使用もできます。リーチのフィードバックの統計は、Reach API の "Stats" を使用して収集できます。また、過去のキャンペーンに基づいてプッシュ キャンペーンの対象をカスタマイズすることも可能です。


### 関連項目 
-  [UI ドキュメント - Reach - 新しいプッシュ キャンペーン][27]、[API ドキュメント - Reach API - Stats][Link 4]

![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 

<!---HONumber=August15_HO7-->