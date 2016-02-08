<properties 
	pageTitle="Application Insights のログ記録アダプターのリリース ノート" 
	description="最新の更新プログラム。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/21/2015" 
	ms.author="abaranch"/>
 
# .NET のログ記録アダプターに関するリリース ノート

log4net、NLog、System.Diagnostics.Trace などのログ記録フレームワークを既に使用している場合、これらのログをキャプチャして [Application Insights](https://azure.microsoft.com/services/application-insights/) に送信し、ユーザーの操作や例外などのイベントとログ トレースを関連付けることができます。


#### 作業を開始するには:

「[Application Insights での ASP.NET のログ、例外、カスタム診断](app-insights-search-diagnostic-logs.md)」を参照してください。

## バージョン 1.2.6

- バグの修正
- log4Net: log4net のプロパティをカスタム プロパティとして収集します。UserName はカスタム プロパティではなくなりました (telemetry.Context.User.Id として収集されます)。Timestamp はカスタム プロパティではなくなりました。
- NLog: NLog のプロパティをカスタム プロパティとして収集します。SequenceID はカスタム プロパティではなくなりました (telemetry.Sequence として収集されます)。Timestamp はカスタム プロパティではなくなりました。 

## バージョン 1.2.5
- 最初のオープン ソース バージョン: バージョン 1.2.3 以降の Application Insights API を参照してください。

<!---HONumber=AcomDC_0128_2016-->