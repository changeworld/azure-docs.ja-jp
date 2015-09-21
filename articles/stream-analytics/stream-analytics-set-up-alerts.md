<properties 
	pageTitle="Stream Analytics のアラート | Microsoft Azure" 
	description="Stream Analytics のアラートについて理解する" 
	keywords="big data analytics,cloud service,internet of things,managed service,stream processing,streaming analytics,streaming data"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# アラートの設定

## [監視] ページ

メトリックが指定した条件に達したときにアラートをトリガーするルールを設定できます。

例: "過去 15 分間の出力イベントが 100 未満の場合は電子メール ID: xyz@company.com” に電子メール通知を送信する"。

ルールは、ポータルでメトリックに対して設定することも、操作ログのデータに対して[プログラムによって](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)構成することもできます。

## Azure ポータルを使用したアラートの設定

アラートは、次の 2 つの方法で設定できます。

1.	Stream Analytics ジョブの [監視] タブ  
2.	Management Services の操作ログ  

## ポータルでのジョブの [監視] タブを使用したアラート

1.	[監視] タブでメトリックを選択し、ダッシュボードの下部にある [ルールの追加] をクリックし、ルールを設定します。  

    ![ダッシュボード](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	アラートの名前と説明を定義します。

    ![ルールの作成](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	しきい値、アラート評価ウィンドウ、およびアラートのアクションを入力します。

    ![条件の定義](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## 操作ログを使用したアラートの設定

1.	[Azure ポータル](https://manage.windowsazure.com)で、Management Services の **[アラート]** タブに移動します。  
2.	**[ルールの追加]** をクリックします。  

    ![条件](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	アラートの名前と説明を定義します。[サービスの種類] として [Stream Analytics] を選択し、[サービス名] としてジョブの名前を選択します。

    ![アラートの定義](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->