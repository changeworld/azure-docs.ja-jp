<properties 
	pageTitle="Application Insights: プラットフォーム" 
	description="Application Insights と共に使用できるもの" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# Application Insights: プラットフォーム

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

#### Application Insights と共に使用できるもの


## 言語

+ [C#、VB](app-insights-start-monitoring-app-health-usage.md)
+ [JavaScript Web ページ](app-insights-web-track-usage.md)
+ [Windows ストアの JavaScript アプリ](#cordova)
+ [Java](app-insights-java.md)
+ [Ruby](https://rubygems.org/gems/application_insights) 
+ [PHP](https://github.com/Microsoft/AppInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)

## プラットフォーム

+ [ASP.NET](app-insights-start-monitoring-app-health-usage.md)
+ [Azure Web アプリと VM](insights-perf-analytics.md)
+ [Android](https://github.com/Microsoft/AppInsights-Android)
+ [iOS](https://github.com/Microsoft/AppInsights-iOS)
+ [Cordova](#cordova)
+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)
+ [Windows デスクトップ](app-insights-windows-desktop.md)


## ログ記録フレームワーク

+	[Log4Net、NLog、または System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java、Log4J、または Logback](app-insights-java-trace-logs.md)


## プロジェクト

GitHub の [Application Insights SDK プロジェクト](https://github.com/Microsoft/AppInsights-Home)に関するページも参照してください。


### <a name="cordova"></a>Cordova および Windows ストアの JavaScript アプリ

Visual Studio でプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。

**[オンライン]** を選択し、Application Insights を検索します。

**Application Insights API for JavaScript Applications** をインストールします。

標準のクライアント側 [Web アプリ スクリプト](app-insights-web-track-usage.md)を使用しますが、変更点が 1 つあります。

Application Insights ポータルからスクリプトを取得したら、インストルメンテーション キーの後に、次の行を挿入します。

    ...{
        instrumentationKey:"00000000-662d-4479-0000-40c89770e67c",
        endpointUrl:"https://dc.services.visualstudio.com/v2/track"
    } ...

[Cordova](http://cordova.apache.org/)

[JavaScript を使用した Windows ストア アプリ](https://msdn.microsoft.com/library/windows/apps/br211385.aspx)

<!--Link references-->



<!--HONumber=54-->