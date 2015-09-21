<properties 
	pageTitle="操作ログ |Microsoft Azure" 
	description="Stream Analytics の操作ログ" 
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

# 操作ログ

すべての Azure サービスでは、管理操作に関連する詳細を記録するために操作ログ メッセージがユーザーに提供されます。Azure Stream Analytics では、ジョブの状態、ジョブの進行状況、エラー メッセージを表示して入力から、処理、出力までのジョブの進行を追跡するなど、この情報をデバッグ目的に使用できます。

## 操作ログの検索

操作ログには 2 つの方法でアクセスできます。

- Stream Analytics ジョブのダッシュボード  
- Microsoft Azure ポータルの管理サービス  

## Stream Analytics ジョブのダッシュボード

Stream Analytics ジョブの対応するログへのリンクが、ジョブの [ダッシュボード] タブに表示されます。そのリンクをクリックすると、その特定のジョブの最新のログが表示されるようにフィルターが設定されます。

  ![管理サービスの選択](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)

## Microsoft Azure ポータルの管理サービス

Azure ポータルで Stream Analytics および他のサービスの操作ログに手動で移動するには:

1.	[Azure ポータル](https://manage.windowsazure.com)で **[管理サービス]** をクリックします。
2.	**[種類]** で **[Stream Analytics]** を選択し、**[サービス名]** でジョブの名前を選択します。  

    ![Stream Analytics の選択](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)

## 操作ログの使用

時間範囲と状態でフィルター処理して、ジョブのログを表示できます。

選択したイベントについての詳細を表示するには、ウィンドウの下部にある **[詳細]** ボタンをクリックします。

  ![詳細の選択](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)

## 失敗したジョブのデバッグ

[検索] アイコンをクリックし、「failed」と入力します。エラーが発生したすべてのログの結果が表示されます。

  ![失敗したジョブのデバッグ](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)

障害のいずれかを選択して **[詳細]** をクリックし、エラーの詳細を確認できます。一部のエラー メッセージでは、問題を軽減する方法についての情報も提供されます。

  ![操作の詳細](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)

[MSDN フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=AzureStreamAnalytics)で[サポート](http://azure.microsoft.com/support/options/)に問い合わせたり、チームに情報を提供したりする必要がある場合は、操作の詳細、特に **相関 ID** を記録してください。

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=AzureStreamAnalytics)を参照してください。

## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure Stream Analytics の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->