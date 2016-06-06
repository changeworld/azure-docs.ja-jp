<properties 
	pageTitle="Application Insights に関するトラブルシューティングと Q &amp; A" 
	description="Visual Studio Application Insights について不明な点や問題点はありませんか? ここで解決してください。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/13/2016" 
	ms.author="awills"/>
 
# 質問 - ASP.NET 向けの Application Insights

## 構成の問題

*次のセットアップで問題が発生しています。*

* [.NET アプリ](app-insights-asp-net-troubleshoot-no-data.md)
* [既に実行中のアプリの監視](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Azure 診断](app-insights-azure-diagnostics.md)
* [Java Web アプリ](app-insights-java-troubleshoot.md)
* [その他のプラットフォーム](app-insights-platforms.md)


## Application Insights と共に使用できるもの

[プラットフォームを確認][platforms]


## これは無料ですか。

* はい、Free [価格レベル](app-insights-pricing.md)を選択した場合は、無料です。ほとんどの機能と大量のデータが手に入ります。 
* Microsoft Azure に登録するには、クレジット カード データを登録する必要がありますが、他の有料の Azure のサービスを使用するか、明示的に有料レベルにアップグレードしない限り、料金は発生しません。
* アプリが Free レベルの月間クォータより多くのデータを送信すると、ログの記録を停止します。このような場合は、料金の支払いを開始するか、月末にクォータがリセットされるまでお待ちください。
* 基本的な使用とセッション データは、クォータの対象になりません。
* 30 日間の無料試用版が用意されています。この期間中、有料の機能を無料でご利用いただけます。
* 各アプリケーション リソースには、個別のクォータがあり、それぞれに個別に価格レベルを設定します。

#### 料金を払うと何ができますか。

* より大きな[データの月間クォータ](https://azure.microsoft.com/pricing/details/application-insights/)。
* 月間クォータを超えてデータの収集を続行するための、「超過」支払いオプション。データがクォータを超えた場合は、1 MB ごとに課金されます。
* [連続エクスポート](app-insights-export-telemetry.md)。


## <a name="q14"></a> Application Insights によってどのような変更がプロジェクトに加えられますか?

詳細は、プロジェクトの種類によって異なります。Web アプリケーションの場合:


+ 次のファイルがプロジェクトに追加されます。

 + ApplicationInsights.config
 + ai.js


+ 次の NuGet パッケージがインストールされます。

 -  *Application Insights API* - コア API

 -  *Application Insights API for Web Applications* - サーバーからテレメトリを送信するために使用されます

 -  *Application Insights API for JavaScript Applications* - クライアントからテレメトリを送信するために使用されます

    これらのパッケージには次のアセンブリが含まれています。

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ 次の項目を挿入します。

 - web.config

 - packages.config

+ (新しいプロジェクトのみ。[Application Insights を既存のプロジェクトに追加している場合][start]は、手動でこの操作を行う必要があります)。 これらを Application Insights リソース ID で初期化するためのスニペットを、クライアントとサーバーのコードに挿入します。たとえば、MVC アプリでは、Views/Shared/\_Layout.cshtml マスター ページにコードが挿入されます。


## 以前のバージョンの SDK からアップグレードする方法。

お使いのアプリケーションに適切な SDK については、「[リリース ノート](app-insights-release-notes.md)」をご覧ください。



## <a name="update"></a>自分のプロジェクトがデータを送信する Azure のリソースを変更するにはどうすればいいですか?

ソリューション エクスプローラーで、`ApplicationInsights.config` を右クリックし、[**Application Insights の更新**] を選択します。Azure の既存または新規のリソースにデータを送信できます。更新ウィザードでは、サーバー SDK のデータの送信先を決定する、ApplicationInsights.config のインストルメンテーション キーを変更します。[すべて更新] を選択解除している場合を除き、Web ページ内のキーが表示される場所でもキーが変更されます。


#### <a name="data"></a>ポータルでのデータ保持期間はどのくらいですか? セキュリティで保護されていますか?

[データの保持とプライバシー][data]に関するページをご覧ください。

## ログの記録

#### <a name="post"></a>診断検索で POST データを表示する方法を教えてください。

POST データは自動ではログに記録されませんが、TrackTrace 呼び出しを使用してメッセージ パラメーターにデータを格納できます。文字列プロパティの制限よりもサイズ制限は大きいですが、フィルター処理には使用できません。

## セキュリティ

#### ポータルのデータはセキュリティで保護されていますか? 保持期間はどれくらいですか?

[データの保持とプライバシー][data]に関するページをご覧ください。


## <a name="q17"></a>Application Insights の機能をすべて有効にしているでしょうか?

<table border="1">
<tr><th>表示内容</th><th>表示方法</th><th>用途</th></tr>
<tr><td>可用性グラフ</td><td><a href="../app-insights-monitor-web-app-availability/">Web テスト</a></td><td>Web アプリが稼働しているか確認する</td></tr>
<tr><td>サーバー アプリ パフォーマンス: 応答時間、...
</td><td><a href="../app-insights-asp-net/">Application Insights をプロジェクトに追加する</a><br/>または <br/><a href="../app-insights-monitor-performance-live-website-now/">AI Status Monitor をサーバーにインストールする</a> (または独自のコードを記述して<a href="../app-insights-api-custom-events-metrics/#track-dependency">依存関係を追跡する</a>)</td><td>パフォーマンスの問題を検出する</td></tr>
<tr><td>依存関係テレメトリ</td><td><a href="../app-insights-monitor-performance-live-website-now/">AI Status Monitor をサーバーにインストールする</a></td><td>データベースや、その他の外部コンポーネントの問題を診断する</td></tr>
<tr><td>例外からスタック トレースを取得する</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">コード内に TrackException 呼び出しを挿入する</a> (自動で報告されるものもある)</td><td>例外を検出して診断する</td></tr>
<tr><td>ログ トレースの検索</td><td><a href="../app-insights-search-diagnostic-logs/">ログ アダプターを追加する</a></td><td>例外、パフォーマンスの問題を診断する</td></tr>
<tr><td>クライアントの利用状況の基本情報: ページ ビュー、セッション、...</td><td><a href="../app-insights-javascript/">Web ページの JavaScript の初期化子</a></td><td>利用状況分析</td></tr>
<tr><td>クライアントのカスタム メトリック</td><td><a href="../app-insights-api-custom-events-metrics/">Web ページでの呼び出しの追跡</a></td><td>ユーザー エクスペリエンスを向上させる</td></tr>
<tr><td>サーバーのカスタム メトリック</td><td><a href="../app-insights-api-custom-events-metrics/">サーバー コードでの呼び出しの追跡</a></td><td>ビジネス インテリジェンス</td></tr>
</table>


## Automation

Application Insights リソースを作成および更新するための [PowerShell スクリプトを作成](app-insights-powershell.md)することができます。

## その他の回答

* [Application Insights フォーラム](https://social.msdn.microsoft.com/Forums/vstudio/ja-JP/home?forum=ApplicationInsights)


<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0525_2016-->