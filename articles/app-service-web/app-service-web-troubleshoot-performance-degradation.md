<properties
	pageTitle="トラブルシューティング: Web アプリのパフォーマンス低下"
	description="この記事は、Azure App Service でホストされている Web アプリで発生したパフォーマンスに関する問題のトラブルシューティングを行うために役立ちます。"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="cephalin"/>

# トラブルシューティング: Web アプリのパフォーマンス低下

この記事は、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) でホストされている Web アプリで発生したパフォーマンスに関する問題のトラブルシューティングを行うために役立ちます。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。

## 症状

ブラウザーで Web アプリにアクセスしたときのページの読み込みが遅く、タイムアウトすることもある

## 原因

この症状は多くの場合、アプリケーション レベルの問題が原因で発生します。その例を次に示します。

-	要求に時間がかかっている
-	アプリケーションのメモリ/CPU 使用率が高い
-	例外が発生してアプリケーションがクラッシュする

## トラブルシューティングの手順

トラブルシューティングは、大きく次の 3 つのタスクに分けられます。この 3 つのタスクを上から順に行います。

1.	[アプリケーションの動作を観察、監視する](#observe)
2.	[データを収集する](#collect)
3.	[問題を緩和する](#mitigate)

これらの手順には、[App Service Web Apps](/services/app-service/web/) を活用できます。

<a name="observe" />
### 1\.アプリケーションの動作を観察、監視する

#### サービス正常性を追跡する

Microsoft Azure は、サービスの中断やパフォーマンスの低下があるたびに、毎回公表します。サービスの正常性は、[Azure ポータル](https://portal.azure.com/)で追跡できます。詳細については、[サービスの正常性の追跡](insights-service-health.md)に関するページを参照してください。

#### Web アプリを監視する

Web アプリに問題が発生しているかどうかは、アプリを監視することによって確認することができます。Web アプリのブレードで **[要求とエラー]** タイルをクリックします。**[メトリック]** ブレードには、追加できるすべてのメトリックが表示されます。

Web アプリに関しては、次のメトリックを監視するようお勧めします。

-	平均メモリ ワーキング セット
-	平均応答時間
-	CPU 時間
-	メモリ ワーキング セット
-	要求数

![](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

詳細については、次を参照してください。

-	[Azure App Service の Web Apps の監視](web-sites-monitor.md)
-	[アラート通知の受信](insights-receive-alert-notifications.md)

#### Web エンドポイントの状態を監視する

Web アプリを **Standard** 価格レベルで実行している場合、Web Apps で 3 つの地域から 2 つのエンドポイントを監視することができます。

エンドポイント監視では、地理的に分散した場所から Web URL の応答時間とアップタイムをテストする Web テストを構成します。このテストでは、Web URL に対して HTTP get 操作を実行し、各場所から応答時間とアップタイムを測定します。構成された各場所では、テストを 5 分ごとに実行します。

アップタイムは HTTP 応答コードを使用して監視され、応答時間はミリ秒単位で測定されます。HTTP 応答コードが 400 以上である場合、または、応答に 30 秒以上かかる場合、監視テストは失敗します。すべての指定した場所から監視テストが成功した場合、エンドポイントは利用可能と見なされます。

セットアップ方法については、「[方法: Web エンドポイントの状態の監視](web-sites-monitor.md#webendpointstatus)」を参照してください。

また、「[Keeping Azure Web Sites up plus Endpoint Monitoring with Stefan Schackow (Azure の Web サイトの保持とエンドポイントの監視 - Stefan Schackow 共演)](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)」で、エンドポイント監視に関するビデオをご覧いただけます。

#### 拡張機能を使用したアプリケーション パフォーマンスの監視

アプリケーションのパフォーマンス監視には、_サイト拡張機能_を利用することもできます。

App Service Web アプリにはそれぞれ拡張可能な管理エンド ポイントが用意されており、サイト拡張機能としてデプロイされている強力なツール一式を利用することができます。たとえば、[Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx) などのソース コード エディターや、接続されたリソース (Web アプリに接続されている MySQL データベースなど) 用の管理ツールなど、その種類は多岐にわたります。

パフォーマンス監視を目的としたサイト拡張機能としては、[Azure Application Insights](/services/application-insights/) と [New Relic](/marketplace/partners/newrelic/newrelic/) の 2 つがあります。New Relic を使用するには、実行時にエージェントをインストールします。Azure Application Insights を使用するには、SDK でコードを再ビルドします。また、追加データにアクセスできる拡張機能もインストールできます。SDK では、アプリの使用状況とパフォーマンスをさらに詳細に監視するコードを記述できます。

Application Insights の使用については、「[Web アプリケーションのパフォーマンスを監視する](app-insights-web-monitor-performance.md)」を参照してください。

New Relic の使用については、「[Azure の New Relic によるアプリケーション パフォーマンス管理](store-new-relic-cloud-services-dotnet-application-performance-management.md)」を参照してください。

<a name="collect" />
### 2\.データを収集する

####	Web アプリに対して診断ログを有効にする

Web Apps 環境は、Web サーバーと Web アプリケーションの両方のログ情報を診断する機能を備えています。これらは論理的に Web サーバー診断とアプリケーション診断に分けられます。

##### Web サーバー診断

次の種類のログを有効または無効にできます。

-	**詳細なエラー ログ** - 障害 (状態コード 400 以上) を示す HTTP 状態コードの詳細なエラー情報。このログには、サーバーがエラー コードを返した理由を特定するために役立つ情報が記録されている場合があります。
-	**失敗した要求トレース** - 要求の処理に使用された IIS コンポーネントのトレースや各コンポーネントにかかった時間など、失敗した要求の詳細情報。これは、Web アプリのパフォーマンスを向上させたり、特定の HTTP エラーの原因を分離しようとする場合に役立ちます。
-	**Web サーバーのログ記録** - W3C 拡張ログ ファイル形式を使用した、HTTP トランザクションに関する情報。これは、サイトで処理された要求の数や特定の IP アドレスからの要求の数などの Web アプリの全体的なメトリックを特定するときに役立ちます。

##### アプリケーション診断

アプリケーション診断では、Web アプリケーションによって生成された情報を取り込むことができます。ASP.NET アプリケーションは、`System.Diagnostics.Trace` クラスを使用してアプリケーション診断ログに情報を記録できます。

アプリケーションのログを構成する詳細な手順については、「[Azure App Service の Web アプリの診断ログの有効化](web-sites-enable-diagnostic-log.md)」を参照してください。

#### リモート プロファイリングの使用

Azure App Service では、Web Apps、API Apps、WebJobs のプロファイリングをリモートから実行できます。プロセスの実行に予想以上に時間がかかる場合や、HTTP 要求の待機時間が通常よりも長く、かつプロセスの CPU 使用率も高い場合、対象プロセスをリモートからプロファイリングし、CPU サンプリング呼び出し履歴を取得して、プロセスのアクティビティとコードのホット パスを分析することができます。

詳細については、[Azure App Service におけるリモート プロファイリングのサポート](/blog/remote-profiling-support-in-azure-app-service)に関するページを参照してください。


#### Azure App Service サポート ポータルを使用する

Web Apps には、HTTP ログ、イベント ログ、処理ダンプなどを参照することによって、Web アプリに関連した問題をトラブルシューティングする機能があります。その情報はすべて、**http://&lt;your app name>.scm.azurewebsites.net/Support** のサポート ポータルで提供されます。

一般的なトラブルシューティングの状況に合わせて、Azure App Service サポート ポータルには、3 つのタブが用意されています。次の 3 つの手順が想定されています。

1.	現在の動作を観察する
2.	診断情報を集め、組み込みのアナライザーを実行して分析する
3.	緩和する

現時点で問題が発生している場合は、**[分析]**、**[診断]**、**[今すぐ診断]** の順にクリックして診断セッションを作成します。これで、HTTP ログ、イベント ビューアー ログ、メモリ ダンプ、PHP エラー ログ、および PHP プロセス レポートが収集されます。

データの収集後にも、データに対する分析が実行され、HTML レポートが出力されます。

そのデータをダウンロードすることもできます。D:\\home\\data\\DaaS フォルダーが既定の保存先となります。

Azure App Service サポート ポータルの詳細については、[Azure Websites のサポート サイト拡張機能で新たに行われた更新](/blog/new-updates-to-support-site-extension-for-azure-websites)に関するページを参照してください。

#### Kudu デバッグ コンソールを使用する

Web Apps には、ファイルのデバッグ、調査、アップロード用のデバッグ コンソールのほか、ご利用の環境についての情報を入手するための JSON エンドポイントが用意されています。このコンソールは、Web アプリの _Kudu コンソール_または _SCM ダッシュボード_と呼ばれます。

ダッシュボードには、**https://&lt;Your app name>.scm.azurewebsites.net/** リンクからアクセスできます。

Kudu には次のような機能があります。

-	アプリケーションの環境設定
-	ログ ストリーム
-	診断ダンプ
-	デバッグ コンソール (Powershell のコマンドレットや基本的な DOS コマンドを実行可能)


Kudu にはもう 1 つ便利な機能があり、アプリケーションからファーストチャンス例外がスローされた場合に、Kudu と SysInternals ツール Procdump を使用してメモリ ダンプを作成することができます。このメモリ ダンプはプロセスのスナップショットです。Web アプリに関して、通常より複雑な問題をトラブルシューティングできる場合も少なくありません。

Kudu で利用できる機能の詳細については、[知っておくべき Azure Websites Team Services ツール](/blog/windows-azure-websites-online-tools-you-should-know-about/)に関するページを参照してください。

<a name="mitigate" />
### 3\.問題を緩和する

####	Web アプリをスケーリングする

Azure App Service では、アプリケーションが実行されるスケールを調整することによって、パフォーマンスとスループットを高めることができます。Web アプリのスケール アップには、2 つの関連する措置が伴います。1 つは、App Service プランの価格レベルを引き上げること、もう 1 つは、価格レベルを引き上げた後に特定の設定を構成することです。

スケーリングの詳細については、「[Azure App Service の Web アプリをスケーリングする](web-sites-scale.md)」を参照してください。

加えて、アプリケーションを複数のインスタンスで実行することもできます。処理能力がアップするだけでなく、ある程度の耐障害性を確保することができます。この場合、1 つのインスタンスでプロセスがダウンしても、他のインスタンスが要求の処理を続行します。

スケーリングは、[手動] または [自動] に設定することができます。

####	AutoHeal を使用する

AutoHeal は、選択された設定 (構成の変更、要求、メモリに基づく制限、要求の実行に必要な時間など) に従って、アプリのワーカー プロセスをリサイクルします。ほとんどの場合、問題を回復するための一番の近道は、プロセスをリサイクルすることです。Web アプリはいつでも、Azure ポータル内から直接、再起動できますが、AutoHeal はユーザーの介入なしでそれを自動的に実行します。必要な作業は、Web アプリのルート web.config にいくつかのトリガーを追加することだけです。.Net アプリケーション以外でも、これらの設定は同じように作用します。

詳細については、[Azure Web Sites の自動復旧](/blog/auto-healing-windows-azure-web-sites/)に関するページを参照してください。

####	Web アプリを再起動する

1 回限りの問題であれば、通常これが最も簡単な復旧方法です。アプリを停止または再起動するためのオプションは、[Azure ポータル](https://portal.azure.com/)の Web アプリ ブレードにあります。

 ![](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Web アプリの管理には、Azure PowerShell を使用することもできます。詳細については、[リソース マネージャーでの Azure PowerShell の使用](powershell-azure-resource-manager.md)をご覧ください。

<!---HONumber=AcomDC_0128_2016-->