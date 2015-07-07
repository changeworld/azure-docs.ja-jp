<properties 
	pageTitle="Application Insights に関するトラブルシューティングと Q &amp; A" 
	description="Visual Studio Application Insights について不明な点や問題点はありませんか? ここで解決してください。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# トラブルシューティングおよび Q & A - Application Insights for ASP.NET

## Application Insights と共に使用できるもの

[プラットフォームを参照][platforms]

## SDK の追加

#### <a name="q01"></a>Application Insights をプロジェクトに追加するオプションが Visual Studio に表示されません

+ [Visual Studio 2013 Update 3 以降](http://go.microsoft.com/fwlink/?LinkId=397827)を使用しているか確認してください。Application Insights Tools がプレインストールされています。
+ このツールは、あらゆる種類のアプリケーションをサポートしているわけではありませんが、Application Insights SDK をプロジェクトに手動で追加することはできます。[この手順][windows]を使用してください。 


#### <a name="q02"></a>新しい Web プロジェクトが作成されましたが Application Insights が追加されませんでした。

Application Insights ポータルとの通信に失敗した場合やアカウントに問題がある場合にこのような状況になることがあります。

+ 適切な Azure アカウントのログイン資格情報を指定していることを確認してください。[新しいプロジェクト] ダイアログで使用される Microsoft Azure 資格情報は、Visual Studio の右上に表示される Visual Studio Online 資格情報とは異なる場合があります。
+ しばらく待ってから [Application Insights を既存のプロジェクトに追加][start]してください。
+ Microsoft Azure アカウントの設定を表示し、制限の有無を確認してください。Application Insights アプリケーションを手動で追加できるかどうかを確認してください。

#### <a name="emptykey"></a>エラー「インストルメンテーション キーは空にできません」が発生しました

Application Insights をインストールしているとき、またはログ アダプターをインストールしているときに、何かの問題が発生した可能性があります。

ソリューション エクスプローラーで、`ApplicationInsights.config` を右クリックし、[**Application Insights の更新**] を選択します。Azure へのサインインを促すダイアログが表示されます。または、Application Insights のリソースを作成するか、既存のリソースを再利用します。


#### <a name="q14"></a>Application Insights によってどのような変更がプロジェクトに加えられますか?

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

+ (新しいプロジェクトのみ。[Application Insights を既存のプロジェクトに追加している場合][start]は、手動でこの操作を行う必要があります)。 これらを Application Insights リソース ID で初期化するためのスニペットを、クライアントとサーバーのコードに挿入します。たとえば、MVC アプリでは、Views/Shared/_Layout.cshtml マスター ページにコードが挿入されます。

####<a name="NuGetBuild"></a> 「NuGet パッケージがありません」というエラーがビルド サーバーで出ますが、開発用コンピューターではすべて正常にビルドできます

[NuGet パッケージの復元](http://docs.nuget.org/Consume/Package-Restore)に関するページと[自動パッケージの復元](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)に関するページを参照してください。

####<a name="FailUpdate"></a> NuGet パッケージを 0.17 以降に更新した後にビルドしようとしたら、「プロジェクトが、コンピューターにない NuGet パッケージを参照しています」というエラーが出ました。

NuGet パッケージを 0.17 以降に更新した後に上記のエラーが発生した場合は、proj ファイルを編集し、残っている BCL ターゲットを削除する必要があります。

これを行うには、次の手順を実行します。

1. ソリューション エクスプローラーでプロジェクトを右クリックし、[プロジェクトのアンロード] を選択します。
2. もう一度プロジェクトを右クリックし、[編集 *yourProject.csproj*] を選択します。 
3. プロジェクト ファイルの一番下に移動し、次のような BCL ターゲットを削除します: ``` <Import Project="..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets" Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" />
	  
	  <Target Name="EnsureBclBuildImported" BeforeTargets="BeforeBuild" Condition="'$(BclBuildImported)' == ''">
	  
	    <Error Condition="!Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=317567." HelpKeyword="BCLBUILD2001" />
	    
	    <Error Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="The build restored NuGet packages. Build the project again to include these packages in the build. For more information, see http://go.microsoft.com/fwlink/?LinkID=317568." HelpKeyword="BCLBUILD2002" />
	    
	</Target> ```
4. ファイルを保存します。
5. プロジェクトを右クリックし、[再読み込み *yourProject.csproj*] を選択します。

## データが表示されない

#### <a name="q03"></a>Application Insights を正常に追加してアプリを実行したのですが、ポータルにデータが表示されません。

+ 概要ページで、検索タイルをクリックして診断検索を開いてください。データはまずここに表示されます。
+ [更新] ボタンをクリックします。現在のバージョンでは、ブレードの内容は自動的に更新されません。
+ Microsoft Azure のスタート ボードで、サービス状態マップを確認してください。アラート表示がある場合は、"OK" が表示されるまで待ってから、Application Insights アプリケーション ブレードをいったん閉じて開き直します。
+ [状態ブログ](http://blogs.msdn.com/b/applicationinsights-status/archive/2015/04/14/data-latency-and-data-access-issue-with-data-storage-service-4-14-investigating.aspx)も参照してください。
+ ファイアウォールでは、dc.services.visualstudio.com と f5.services.visualstudio.com への発信トラフィック用に TCP ポート 80 と 443 を開く必要がある場合があります。
+ プロキシを使用して社内ネットワークの外に送信しなければならない場合は、Web.config に [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) を設定します。
+ Windows Server 2008: 更新プログラム [KB2468871](https://support.microsoft.com/kb/2468871)、[KB2533523](https://support.microsoft.com/kb/2533523)、[KB2600217](https://support.microsoft.com/kb/2600217) をインストールしていることを確認します。

#### <a name="q04"></a>Web サイトの [利用状況分析] にデータが表示されません

+ データは、Web ページのスクリプトによって取得されます。Application Insights を既存の Web プロジェクトに追加した場合は、[スクリプトを手動で追加する][start]必要があります。
+ Internet Explorer がサイトを互換モードで表示していないか確認してください。
+ ブラウザーのデバッグ機能 (一部のブラウザーでは F12 を押した後に [ネットワーク] を選択) を使用して、dc.services.visualstudio.com にデータが送信されていることを確認してください。

#### <a name="q08"></a>Application Insights を使用してイントラネット Web サーバーを監視できますか?

はい。サーバーがデータをパブリック インターネットに送信できる場合は健全性と利用状況を監視できます。

ただし、サービスに対して Web テストを実行する場合は、パブリック インターネットからポート 80 でサービスにアクセスできる必要があります。

#### パブリック インターネットへのアクセスがないイントラネット Web サーバーを監視できますか?

dc.services.visualstudio.com への https POST 呼び出しを中継できるプロキシを用意する必要があります。

#### データが表示されていたのに停止しました。

* [状態ブログ](http://blogs.msdn.com/b/applicationinsights-status/)を確認してください。
* データ ポイントの月間クォータに達していませんか? [設定]、[クォータと価格] の順に開いて確認してください。上限に達している場合は、プランをアップグレードするか、追加容量分を購入することができます。「[料金プラン](http://azure.microsoft.com/pricing/details/application-insights/)」を参照してください。



## ポータル

#### <a name="q05"></a>Microsoft Azure プレビューのスタート ボードを表示しています。Application Insights でデータを見つけるにはどうすればいいですか?

次のどちらかの操作を行います。

* [参照]、[Application Insights]、目的のプロジェクトの順に選択します。プロジェクトが表示されない場合は、[Visual Studio で Application Insights を Web プロジェクトに追加する][start]必要があります。

* Visual Studio のソリューション エクスプローラーで、Web プロジェクトを右クリックし、[Application Insights ポータルを開く] を選択します。


#### <a name="update"></a>自分のプロジェクトがデータを送信する Azure のリソースを変更するにはどうすればいいですか?

ソリューション エクスプローラーで、`ApplicationInsights.config` を右クリックし、[**Application Insights の更新**] を選択します。Azure の既存または新規のリソースにデータを送信できます。更新ウィザードでは、サーバー SDK のデータの送信先を決定する、ApplicationInsights.config のインストルメンテーション キーを変更します。[すべて更新] を選択解除している場合を除き、Web ページ内のキーが表示される場所でもキーが変更されます。


#### <a name="q06"></a>Microsoft Azure プレビューのホーム画面のマップに表示されるのは、自分のアプリケーションの状態ですか?

いいえ。 このマップには Azure サービスの状態が示されています。Web テストの結果を確認するには、[参照]、[Application Insights]、対象のアプリケーションの順に選択し、Web テストの結果を表示します。


#### <a name="q07"></a>Application Insights をアプリケーションに追加して Application Insights ポータルを開いたのですが、説明のスクリーンショットとはまったく違う画面が表示されます。

Visual Studio Online バージョンに接続される[古いバージョンの Application Insights SDK](http://msdn.microsoft.com/library/dn793604.aspx) をご利用ではないでしょうか。

ご覧のヘルプ ページは、[Microsoft Azure 向けの Application Insights プレビュー][start] (Visual Studio 2013 Update 3 以降に付属) を対象にしています。

#### <a name="data"></a>どのくらいの期間、データはポータルに保持されますか? セキュリティで保護されていますか?

[データの保持とプライバシー][data]に関するページを参照してください。

## ログの記録

#### <a name="post"></a>診断検索で POST データを表示する方法を教えてください。

POST データは自動ではログに記録されませんが、TrackTrace 呼び出しを使用してメッセージ パラメーターにデータを格納することができます。文字列プロパティの制限よりもサイズ制限は大きいですが、フィルター処理には使用できません。

## セキュリティ

#### ポータルのデータはセキュリティで保護されていますか? 保持期間はどれくらいですか?

[データの保持とプライバシー][data]に関するページを参照してください。


## <a name="q17"></a>Application Insights の機能はすべて有効になっているでしょうか?

<table border="1">
<tr><th>表示内容</th><th>表示方法</th><th>用途</th></tr>
<tr><td>可用性グラフ</td><td><a href="../app-insights-monitor-web-app-availability/">Web テスト</a></td><td>Web アプリが稼動しているか確認する</td></tr>
<tr><td>サーバー アプリ パフォーマンス: 応答時間、...
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Application Insights をプロジェクトに追加する</a><br/>または <br/><a href="../app-insights-monitor-performance-live-website-now/">AI Status Monitor をサーバーにインストールする</a></td><td>パフォーマンスの問題を検出する</td></tr>
<tr><td>依存関係テレメトリ</td><td><a href="../app-insights-monitor-performance-live-website-now/">AI Status Monitor をサーバーにインストールする</a></td><td>データベースまたはその他の外部コンポーネントの問題を診断する</td></tr>
<tr><td>例外からスタック トレースを取得する</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">コード内に TrackException 呼び出しを挿入する</a> (自動で報告されるものもある)</td><td>例外を検出して診断する</td></tr>
<tr><td>ログ トレースの検索</td><td><a href="../app-insights-search-diagnostic-logs/">ログ アダプターを追加する</a></td><td>例外、パフォーマンスの問題を診断する</td></tr>
<tr><td>クライアントの利用状況の基本情報: ページ ビュー、セッション、...</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">Web ページの JavaScript の初期化子</a></td><td>利用状況分析</td></tr>
<tr><td>クライアントのカスタム メトリック</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Web ページでの呼び出しの追跡</a></td><td>ユーザー エクスペリエンスを向上させる</td></tr>
<tr><td>サーバーのカスタム メトリック</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">サーバー コードでの呼び出しの追跡</a></td><td>ビジネス インテリジェンス</td></tr>
</table>

Azure VM で Web サービスが実行されている場合は、そこでも[診断を取得][azurediagnostic]できます。



<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->