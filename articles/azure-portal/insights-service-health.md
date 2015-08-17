<properties 
	pageTitle="サービス正常性を追跡する" 
	description="Azure でパフォーマンスの低下やサービスの中断がいつ発生したかを検出します。" 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="stepsic"/>

# サービス正常性を追跡する

Azure は、サービスの中断やパフォーマンスの低下があるたびに、公開を行います。Azure ポータルでこれらのイベントを参照できますが、[REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) または [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) を使用してすべてのイベントにプログラムでアクセスすることもできます。

## リージョンごとのサービス正常性を確認する

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

2. **[ホーム]** に **[サービス正常性]** というタイルがあります![ホーム](./media/insights-service-health/Insights_Home.png)

3. このタイルをクリックすると、Azure のすべてのリージョンの一覧が表示されます。任意のリージョンをクリックすると、そのリージョンのサービス正常性の履歴を参照できます。![ホーム](./media/insights-service-health/Insights_Regions.png)

4. テーブルで個々のインシデントをクリックし、その詳細を参照することも可能です。

## サービス正常性の完全なログを参照する

2. **[参照]** クリックして **[監査ログ]** を選択します。![ハブの参照](./media/insights-service-health/Insights_Browse.png)

3. これによってブレードが開き、過去 7 日間にサブスクリプションに影響を与えたイベントがすべて表示されます。一覧にサービス正常性のエントリが表示されますが、大量にあり一覧で見つけにくい場合があります。

4. **[フィルター]** コマンドをクリックします。

5. **[イベント カテゴリ]** を選択し、**[サービス正常性]** を選択します。![All events](./media/insights-service-health/Insights_Filter.png)

6. **[更新済み]** をクリックします。

7. これでサブスクリプションに影響が与えたサービス正常性イベントがすべて表示されます。![リソース グループ](./media/insights-service-health/Insights_HealthEvent.png)

8. そこから詳細ブレードに移動すると、イベントの詳細を確認できます。
   
## 次のステップ

* イベントが発生するたびに[アラート通知を受信](insights-receive-alert-notifications.md)します。
* [サービス メトリックスを監視](insights-how-to-customize-monitoring.md)して、サービスの可用性と応答性を確認します。
* Application Insights で [Web ページの可用性と応答性を監視](../app-insights-monitor-web-app-availability.md)すると、ページがダウンしているかどうかを検出できます。
 

<!---HONumber=August15_HO6-->