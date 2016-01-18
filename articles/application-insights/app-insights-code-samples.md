<properties 
	pageTitle="Application Insights: コード サンプルとチュートリアル" 
	description="自分のアプリに合わせて調整できるサンプル。" 
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
	ms.date="01/05/2016" 
	ms.author="awills"/>

#  Application Insights: コード サンプルとチュートリアル

*Application Insights はプレビュー段階です。*

これは、[Visual Studio Application Insights](app-insights-overview.md) の使い方を示すコード サンプルのコンパイルです。

## SDK ラボ

[SDK ラボ](https://www.myget.org/gallery/applicationinsights-sdk-labs)は、Application Insights SDK の追加機能としてインストール、アンインストールできる実験用の NuGet パッケージです。実際にお試しいただき、フィードバックをお寄せください。

## Web サービス

* [テレメトリを Azure Web および worker ロールに追加します](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)。
* [Windows 8.1 ユニバーサル アプリ](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal/)

## 連続エクスポート

テレメトリ データを独自のツールで分析するには、ストレージに[そのテレメトリ データをエクスポートします](app-insights-export-telemetry.md)。そこで、解析して処理できます。

* [Power BI へのエクスポート](app-insights-export-power-bi.md) 
* [worker ロールを使用してエクスポートされたデータを解析する](app-insights-code-sample-export-telemetry-sql-database.md)
* [Stream Analytics を使用して SQL にエクスポートする](app-insights-code-sample-export-sql-stream-analytics.md)
* [Microsoft Dynamics CRM のテレメトリの有効化](app-insights-sample-mscrm.md)


## タスクの自動化

* [PowerShell を使用した Application Insights への Azure 診断の送信](app-insights-powershell.md)
* [新しい Application Insights リソースを作成するスクリプト](app-insights-powershell-script-create-resource.md)
* [PowerShell を使用した Application Insights への Azure 診断の送信](app-insights-powershell-azure-diagnostics.md)








 

<!---HONumber=AcomDC_0107_2016-->