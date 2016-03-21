<properties
	pageTitle="Application Insights: 言語、プラットフォーム、統合| Microsoft Azure"
	description="Application Insights で利用できる言語、プラットフォーム、統合"
	services="application-insights"
    documentationCenter=""
	authors="OlegAnaniev-MSFT"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/02/2016"
	ms.author="awills"/>

# Application Insights: 言語、プラットフォーム、統合

以下は、これまでに把握している実装です (サード パーティによるものも含む)。

## 言語

+ [C#|VB (.NET)](app-insights-asp-net.md)
+ [C#|VB (Windows ストア)](app-insights-windows-get-started.md)
+ [C++ (Windows ストア)](app-insights-windows-cpp.md)
+ [Java](app-insights-java-get-started.md)
+ [JavaScript Web ページ](app-insights-web-track-usage.md)
+ [Objective-C](https://github.com/Microsoft/ApplicationInsights-iOS)
+ [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
+ [Ruby](https://rubygems.org/gems/application_insights)
+ [その他](#projects)

## プラットフォームとフレームワーク

+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [ASP.NET](app-insights-asp-net.md)
+ [ASP.NET - 既にライブとなっているアプリ用](app-insights-monitor-performance-live-website-now.md)
+ [ASP.NET 5](app-insights-asp-net-five.md)
+ [Android](https://github.com/Microsoft/ApplicationInsights-Android)
+ [Azure Web アプリと VM](../azure-portal/insights-perf-analytics.md)
+ [Azure Cloud Services](app-insights-cloudservices.md) &#151; Web ロールと worker ロールの両方を含む
+ [Microsoft Dynamics からの CRM オンライン](app-insights-sample-mscrm.md)
+ [Docker](app-insights-docker.md)
+ [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
+ [iOS](https://github.com/Microsoft/ApplicationInsights-iOS)
+ [J2EE](app-insights-java-get-started.md)
+ [J2EE - 既にライブとなっているアプリ用](app-insights-java-live.md)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
+ [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
+ [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
+ [Windows ストア C#|VB](app-insights-windows-get-started.md)
+ [Windows ストア C++](app-insights-windows-cpp.md)
+ [Windows デスクトップのアプリケーションとサービス、および worker ロール](app-insights-windows-desktop.md)
+ [Xamarin](https://github.com/Microsoft/ApplicationInsights-Xamarin)
+ [その他](#projects)


## ログ記録フレームワーク

+	[Log4Net、NLog、または System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java、Log4J、または Logback](app-insights-java-trace-logs.md)
+   [セマンティック ログ (SLAB)](https://github.com/fidmor89/SLAB_AppInsights)&#151; [セマンティック ログ アプリケーション ブロック](https://msdn.microsoft.com/library/dn440729.aspx)と連携
+   [クラウド ベースのロード テスト](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)


## コンテンツ管理システム

+ [Concrete](https://github.com/fidmor89/appInsights-Concrete)
+ [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [Orchard](https://orchardazureappinsights.codeplex.com) 
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)

## エクスポートとデータの分析

+ [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
+ [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
+ [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> 独自の SDK をビルドする

使用する言語やプラットフォーム用の SDK がない場合は、自分で作成するという方法があります。 [GitHub の Application Insights SDK プロジェクト](https://github.com/Microsoft/AppInsights-Home)に示されている既存の SDK のコードをご覧ください。

<!---HONumber=AcomDC_0309_2016-->