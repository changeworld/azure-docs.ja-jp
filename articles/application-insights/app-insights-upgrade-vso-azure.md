<properties 
	pageTitle="以前の Visual Studio Online バージョンの Application Insights からのアップグレード" 
	description="既存のプロジェクトのアップグレード"
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
	ms.date="06/19/2015" 
	ms.author="awills"/>
 
# 以前の Visual Studio Online バージョンの Application Insights からのアップグレード

このドキュメントは、Visual Studio Online に含まれていた以前のバージョンの Application Insights を今でも使用しているプロジェクトがある場合にのみ役立ちます。このバージョンはいずれ使用できなくなるため、Microsoft Azure 内のサービスである新しいバージョンにアップグレードすることをお勧めします。

## 自分が所有しているバージョン

Visual Studio 2013 Update 3 以降を使用してプロジェクトに Application Insights を追加していれば、ほとんどの場合は新しい Azure バージョンを使用しています。

ApplicationInsights.config を開きます。`ActiveProfile` および `Profiles` ノードがある場合は以前のバージョンであり、アップグレードをする必要があります。

または、Visual Studio ソリューション エクスプ ローラーでプロジェクトを確認し、[参照設定] で Microsoft.ApplicationInsights を選択します。[プロパティ] ウィンドウで [バージョン] を探します。これが 0.12 未満の場合、アップグレードが必要です。

## Visual Studio プロジェクトがある場合

1. Visual Studio 2013 Update 3 以降でプロジェクトを開きます。
2. ApplicationInsights.config を削除します。 
3. プロジェクトから Application Insights NuGet パッケージを削除します。削除するには、ソリューション エクスプローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。

    ![](./media/app-insights-upgrade-vso-azure/nuget.png)
4. AppInsightsAgent フォルダーと格納されているファイルを削除します。

    ![](./media/app-insights-upgrade-vso-azure/folder.png)

5. ServiceDefinition.csdef と ServiceConfiguration.csfg からすべての Microsoft.AppInsights の設定名と値を削除します。

    ![](./media/app-insights-upgrade-vso-azure/csdef.png)
4. SDK: プロジェクトを右クリックし、[[Application Insights の追加] を選択][greenbrown]します。これによって SDK がプロジェクトに追加され、さらに Azure で新しい Application Insights リソースが作成されます。
5. ログ: コードに LogEvent() など、以前の API の呼び出しが含まれている場合、それらはソリューションをビルドしようとしたときに見つかります。こういった呼び出しを、[新しい API を使用する][track]ように更新します。
6. Web ページ: プロジェクトに Web ページが含まれている場合、<head> セクション内のスクリプトを置き換えます。通常、Views\\Shared_Layout.cshtml などのマスター ページにはコピーが 1 つだけあります。[Azure の Application Insights リソースの [クイック スタート] ブレードから新しいスクリプトを取得][usage]します。Web ページの本文に logEvent や logPage などのテレメトリ呼び出しが含まれている場合、[新しい API を使用するようにそれらを更新][api]します。
7. サーバー モニター: アプリが IIS 上で実行中のサービスである場合、サーバーから Microsoft Monitoring Agent をアンインストールし、[Application Insights Status Monitor をインストール][redfield]します。
8. Web テスト: Web 可用性テストを使用していた場合、アラートを含む Web 可用性テストを[新しいポータルで再作成][availability]します。
9. アラート: Azure ポータルで、[メトリックに対するアラート][alerts]を設定します。


## Java Web サービスがある場合

1. サーバー コンピューターで、Web サービスのスタートアップ ファイルから APM エージェントへの参照を削除して以前のエージェントを無効にします。TomCat サーバーでは Catalina.bat を編集します。JBoss サーバーでは Run.bat を編集します。 
2. Web サービスを再起動します。
3. Microsoft Azure ポータルで、[新しい Application Insights リソースを追加][java]します。開発用コンピューターで、Web プロジェクトに [Java SDK][java] を追加します。
4. Web ページの <head> セクションのスクリプトを置き換えます (サーバー側には 1 つのコピーだけが含まれている可能性があります)。 [Azure の新しい Application Insights リソースの [クイック スタート] ブレードから新しいスクリプトを取得][usage]します。Web ページの本文に logEvent や logPage などのテレメトリ呼び出しが含まれている場合、[新しい API を使用するようにそれらを更新][track]します。
8. Web テスト: Web 可用性テストを使用していた場合、アラートを含む Web 可用性テストを[新しいポータルで再作成][availability]します。
9. アラート: Azure ポータルで、[メトリックに対するアラート][alerts]を設定します。



<!--Link references-->

[alerts]: app-insights-alerts.md
[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO3-->