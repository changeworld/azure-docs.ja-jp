<properties 
	pageTitle="Microsoft Azure アプリ向けの Application Insights" 
	description="Application Insights を使用して Azure アプリの使用状況とパフォーマンスを分析します。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/13/2015" 
	ms.author="awills"/>

#  Application Insights: コード サンプル

*Application Insights はプレビュー段階です。*

これは、[Visual Studio Application Insights](app-insights-get-started.md) の使い方を示すコード サンプルのコンパイルです。

## Web サービス

* [テレメトリを Azure Web および worker ロールに追加します](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)。

## 連続エクスポート

テレメトリ データを独自のツールで分析するには、ストレージに[そのテレメトリ データをエクスポートします](app-insights-export-telemetry.md)。そこで、解析して処理できます。

* [worker ロールを使用して SQL にエクスポートする](app-insights-code-sample-export-telemetry-sql-database.md)
* [Stream Analytics を使用して SQL にエクスポートする](app-insights-code-sample-export-sql-stream-analytics.md)


## タスクの自動化

* [新しい Application Insights リソースを作成するスクリプト](app-insights-powershell-script-create-resource.md)








 

<!---HONumber=62-->