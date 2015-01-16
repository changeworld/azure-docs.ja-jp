<properties pageTitle="Azure Web ジョブの推奨リソース" metaKeywords="Azure Web ジョブ, Azure Web ジョブ SDK, Azure ストレージ, Azure キュー, Azure テーブル, Azure Service Bus" description="Azure の Web ジョブと Azure Web ジョブ SDK を使用する方法を学習するための推奨リソース。" metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="Azure WebJobs Recommended Resources" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/27/2014" ms.author="tdykstra" />

#Azure Web ジョブの推奨リソース

このトピックでは、Azure Web ジョブと Azure Web ジョブ SDK の使用方法に関するドキュメント リソースへのリンクを掲載しています。Azure Web ジョブは、Azure Websites でバック グラウンド プロセスとしてスクリプトやプログラムを実行する簡単な方法を提供します。cmd、bat、exe (.NET)、ps1、sh、php、py、js、jar などの実行可能ファイルをアップロードして、実行できます。これらのプログラムは、スケジュールに従って (cron)、または継続的に Web ジョブとして実行されます。

Web ジョブ SDK を使用すると、簡単に Azure Storage を使用できます。Web ジョブ SDK には、Microsoft Azure の BLOB ストレージ、キュー、テーブルおよび Service Bus のキューと共に動作するバインドおよびトリガー システムがあります。

Web ジョブの作成、デプロイ、管理は、Visual Studio の統合ツールでシームレスに行うことができます。テンプレートから Web ジョブを作成し、発行、および管理 (実行、停止、監視、デバッグ) できます。 

Azure の管理ポータルの Web ジョブ ダッシュ ボードは、Web ジョブ内の個々の関数を呼び出す機能など、強力な管理機能を提供して Web ジョブの実行をきめ細かく制御できます。また、ダッシュ ボードでは、関数の実行時間とログ出力も表示されます。 

## 目次

* [Web ジョブの概要](#getstarted)
* [Web ジョブのデプロイ](#deploy)
* [Web ジョブのテストとデバッグ](#debug)
* [その他の Web ジョブのリソース](#additional)
* [その他の Web ジョブ SDK のリソース](#additionalsdk)
* [サンプル Web ジョブ アプリケーション](#samples)
* [ブログ](#blogs)
* [Web ジョブに関する支援](#gethelp)

##<a name="getstarted"></a>Web ジョブの概要

* [Azure Web ジョブの紹介](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure Web ジョブの機能](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Azure Web ジョブ SDK とは](../websites-dotnet-webjobs-sdk/)
* [Announcing the 1.0.0 RTM of Microsoft Azure WebJobs SDK (Microsoft Azure Web ジョブ SDK の RTM の発表)](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Azure Web ジョブ SDK の使用](../websites-dotnet-webjobs-sdk-get-started/)
* [Web ジョブ SDK を使用して Azure キュー ストレージを扱う方法](../websites-dotnet-webjobs-sdk-storage-queues-how-to)
* ビデオ
	* [Web ジョブと Web ジョブ SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Visual Studio 向けの Web ジョブ ツールの概要](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster)
	* [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

次の「[Web ジョブのデプロイ](#deploy)」および「[Web ジョブのテストとデバッグ](#debug)」のセクションも参照してください。

##<a name="deploy"></a>Web ジョブのデプロイ

* [Azure Web ジョブの Azure Web サイトへのデプロイ方法](../websites-dotnet-deploy-webjobs/)
* [Azure の管理ポータルを使用して Web ジョブをデプロイする方法](../web-sites-create-web-jobs/)
* [Enabling Command-line or Continuous Delivery of Azure WebJobs (Azure Web ジョブのコマンドライン、または継続的な配信を有効にする)](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Git deploying a .NET console app to Azure using WebJobs (Git で Web ジョブを使用して Azure に .NET コンソール アプリケーションをデプロイ)](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* ビデオ
	* [Visual Studio 向けの Web ジョブ ツールの概要](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster)
	* [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

##<a name="debug"></a>Web ジョブのテストとデバッグ

* [Visual Studio での Azure Web サイトのトラブルシューティング](../web-sites-dotnet-troubleshoot-visual-studio/)
* [Who wrote that blob? (だれがその BLOB を作成したのか)](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Hosting interactive code in the Cloud (クラウドでの対話型のコードのホスティング)](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Getting a dashboard for local development with the WebJobs SDK (Web ジョブ SDK を使用したローカル開発用のダッシュ ボードの取得)](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Adding Trace to Azure Websites and WebJobs (Azure Websites および Web ジョブへのトレースの追加)](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Microsoft Azure ストレージの監視、診断、およびトラブルシューティング](../storage-monitoring-diagnosing-troubleshooting/)
* ビデオ
	* [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

##<a name="additional"></a>その他の Web ジョブのリソース

* [Magnus Mårtensson 氏による Azure WebJobs GA ブログ記事](http://magnusmartensson.com/azure-webjobs-ga)
* [GitHub の Web ジョブ設定に関するドキュメント](https://github.com/projectkudu/kudu/wiki/Web-jobs)
* [Running Powershell Web Jobs on Azure websites (Azure Web サイトでの Powershell Web ジョブの実行)](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Getting notified when your Azure triggered WebJobs completes (Azure がトリガーした Web ジョブ完了時の通知の受信)](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Azure Web Sites:Architecting Massive-Scale Ready-For-Business Web Apps (Azure Web サイト: 大規模なビジネス対応 Web アプリケーションの設計)。](https://channel9.msdn.com/Events/Build/2014/3-626)Web ジョブ SDK など、Web ジョブを使用した Azure Web サイトのスケール方法について説明します。
* [Simple Web Site Backup retention policy with WebJobs (Web ジョブを使用した簡単な Web サイトのバックアップ保持ポリシー)](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Windows Azure WebSites and Cloud Services Slow on First Request (Microsoft Azure WebSites および Cloud Services が、最初の要求時には応答が遅い)。](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/)Web ジョブを使用して、標準レベルの Websites でのみ使用可能な AlwaysOn 機能をシミュレートする方法を示しています。
* [WebJobs Graceful Shutdown (Web ジョブのグレースフル シャットダウン)](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl)Web ジョブ SDK のグレースフル シャットダウンについては、「[グレースフル シャットダウン](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful)」を参照してください。
* [Scaling Your Web Application with Azure Websites (Azure Websites を使用した Web アプリケーションのスケーリング)](http://msdn.microsoft.com/ja-jp/magazine/dn786914.aspx)
* ビデオ
	* [Scaling out Web Jobs (Web ジョブのスケール アウト)](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)
	* [Magnus Mårtensson 氏による Azure Web ジョブ ビデオ](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>その他の Web ジョブ SDK のリソース

* [Triggers, Bindings, and Route parameters in AzureJobs (Azure ジョブのトリガー、バインド、およびルート パラメーター)](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx)
* Azure Storage のバインド
	* [BLOB](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [キュー](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [テーブル](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [[BlobInput] work? ([BlobInput] が動作する方法)](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Advanced bindings with the Azure Web Jobs SDK (Azure Web ジョブ SDK での高度なバインド)](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [WebJob to upload FREB files to Azure Storage using the WebJobs SDK (Web ジョブ SDK を使用した、FREB のファイルの Azure Storage へのアップロード)](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hosting Azure webjobs outside Azure, with the logging benefits from an Azure hosted webjob (Azure がホストする Web ジョブのログに関するメリットが得られる、Azure 外部での Azure Web ジョブのホスティング)](http://bypassion.dk/?p=510)
* [Building a Data Import Tool with Azure WebJobs (Azure Web ジョブを使用したデータ インポート ツールの構築)](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* ビデオ
	* [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>サンプル Web ジョブ アプリケーション

* [GitHub 上で Web ジョブ チームが提供するサンプル アプリケーション](https://github.com/azure/azure-webjobs-sdk-samples)
* [Simple Azure Website with WebJobs Backend using the WebJobs SDK (Web ジョブ SDK を使用した Web ジョブ バックエンドを備えた簡単な Azure Web サイト)](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77)。スケジュールされたイベント ドリブンの Web ジョブの使用方法を示します。ブログ記事「[Rebuilding the SiteMonitR using Azure WebJobs SDK (Azure Web ジョブ SDK を使用した SiteMonitR の再構築)](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs)」を参照してください。

##<a name="blogs"></a>ブログ

* [Azure のブログ: ](/blog)
* [Amit Apple's blog](http://blog.amitapple.com/)Web ジョブ (SDK ではない) に重点を置いています。
* [Magnus Mårtensson 氏のブログ](http://magnusmartensson.com/)

##<a name="gethelp"></a>Web ジョブに関する支援

* [Web ジョブに関する StackOverflow](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [Web ジョブ SDK に関する StackOverflow](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Azure と ASP.NET のフォーラム](http://forums.asp.net/1247.aspx)
* [Azure Websites のフォーラム](http://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazurewebsitespreview)
* [Azure Websites のユーザーからの意見のサイト](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/)。ハッシュタグ #AzureWebJobs を使用してください。
