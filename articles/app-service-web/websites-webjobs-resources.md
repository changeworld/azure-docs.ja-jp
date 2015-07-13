<properties 
	pageTitle="Azure WebJobs のドキュメント リソース" 
	description="Azure の Web ジョブと Azure Web ジョブ SDK を使用する方法を学習するための推奨リソース。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="tdykstra"/>

# Azure WebJobs のドキュメント リソース

## 概要

このトピックでは、Azure Web ジョブと Azure Web ジョブ SDK の使用方法に関するドキュメント リソースへのリンクを掲載しています。Azure Web ジョブは、[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) でバック グラウンド プロセスとしてスクリプトやプログラムを実行する簡単な方法を提供します。cmd、bat、exe (.NET)、ps1、sh、php、py、js、jar などの実行可能ファイルをアップロードして、実行できます。これらのプログラムは、スケジュールに従って (cron)、または継続的に Web ジョブとして実行されます。

Web ジョブ SDK を使用すると、簡単に Azure Storage を使用できます。Web ジョブ SDK には、Microsoft Azure Storage の BLOB、キュー、テーブルおよび Service Bus のキューと共に動作するバインドおよびトリガー システムがあります。

Web ジョブの作成、デプロイ、管理は、Visual Studio の統合ツールでシームレスに行うことができます。テンプレートから Web ジョブを作成し、発行、および管理 (実行、停止、監視、デバッグ) できます。

Azure ポータルの Web ジョブ ダッシュボードは、Web ジョブ内の個々の関数を呼び出す機能など、強力な管理機能を提供して Web ジョブの実行をきめ細かく制御できます。また、ダッシュボードでは、関数の実行時間とログ出力も表示されます。

##<a name="getstarted"></a>Web ジョブと Web ジョブ SDK の概要

* [Azure Web ジョブの紹介](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure の Web ジョブはとても役に立ちます。今すぐすぐ使用を開始してください。](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Troy Hunt によるブログの投稿です。)
* [Azure Web ジョブの機能](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [Azure Web ジョブ SDK とは](websites-dotnet-webjobs-sdk.md)
* [Microsoft Patterns and Practices によるバック グラウンド ジョブのガイダンス](https://github.com/mspnp/azure-guidance/blob/master/Background-Jobs.md)
* [Microsoft Azure Web ジョブ SDK の 1.0.0 RTM の発表](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Azure Web ジョブ SDK の使用](websites-dotnet-webjobs-sdk-get-started.md)
* [Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Web ジョブ SDK で Azure BLOB ストレージを使用する方法](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Web ジョブ SDK を使用して Azure テーブル ストレージを使用する方法](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Web ジョブ SDK で Azure Service Bus を使用する方法](websites-dotnet-webjobs-sdk-service-bus.md)
* [Azure Web ジョブ SDK クイック リファレンス (PDF ダウンロード)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* ビデオ
	* [Web ジョブと Web ジョブ SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Visual Studio 向けの Web ジョブ ツールの概要](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

次の「[Web ジョブのデプロイ](#deploy)」および「[Web ジョブのテストとデバッグ](#debug)」セクションも参照してください。

##<a name="deploy"></a>Web ジョブのデプロイ

* [Visual Studio を使用して Azure WebJobs をデプロイする方法](websites-dotnet-deploy-webjobs.md)
* [Azure 管理ポータルを使用して Web ジョブをデプロイする方法](web-sites-create-web-jobs.md)
* [Azure Web ジョブのコマンドラインまたは継続的な配信の有効化](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Git で Web ジョブを使用して Azure に .NET コンソール アプリケーションをデプロイ](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [F# WebJob を Azure にデプロイする](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* ビデオ
	* [Visual Studio 向けの Web ジョブ ツールの概要](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Web ジョブのスケジュール設定

* [[Azure Web ジョブの追加] ダイアログ ボックス](websites-dotnet-deploy-webjobs.md#configure)
* [Azure 管理ポータルでのスケジュール実行の Web ジョブの作成](web-sites-create-web-jobs.md#CreateScheduled)

##<a name="debug"></a>Web ジョブのテストとデバッグ

* [Azure Web ジョブに使用できる Visual Studio の新しい開発者機能とデバッグ機能](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Web ジョブ ダッシュボードの表示](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Web ジョブ SDK を使用してログを記述し、ダッシュボードに表示する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Web ジョブのリモート デバッグ](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [だれがその BLOB を作成したのか](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [クラウドでの対話型のコードのホスティング](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Web ジョブ SDK を使用したローカル開発用のダッシュボードの取得](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Azure Web ジョブへのトレースの追加](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Microsoft Azure Storage の監視、診断、およびトラブルシューティング](../storage-monitoring-diagnosing-troubleshooting/)
* ビデオ
	* [Web ジョブ ツールとリモート デバッグ](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Web ジョブの拡張

* [Azure Websites を使用した Web アプリケーションのスケーリング](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure App Service: 大規模なビジネス対応 Web Apps の設計](https://channel9.msdn.com/Events/Build/2014/3-626)。Web ジョブ SDK など、Web ジョブを使用した Web アプリのスケール方法について説明します。
* ビデオ
	* [Web ジョブのスケール アウト](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>その他の Web ジョブのリソース

* [Magnus Mårtensson 氏による Azure WebJobs GA ブログ記事](http://magnusmartensson.com/azure-webjobs-ga)
* [GitHub の Web ジョブ設定に関するドキュメント](https://github.com/projectkudu/kudu/wiki/Web-jobs)。
* [Azure App Service での Powershell Web ジョブの実行](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Azure がトリガーした Web ジョブ完了時の通知の受信](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Web ジョブを使用した簡単な Web アプリのバックアップ保持ポリシー](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Azure Web Apps および Cloud Services が、最初の要求時には応答が遅い](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/)。Web ジョブを使用して、Standard の価格レベルでのみ使用可能な AlwaysOn 機能をシミュレートする方法を示しています。
* [Web ジョブのグレースフル シャットダウン](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl)Web ジョブ SDK のグレースフル シャットダウンについては、「[グレースフル シャットダウン](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful)」を参照してください。
* [Azure Web ジョブと AzCopy を使用したバックアップの自動化](http://markjbrown.com/azure-webjobs-azcopy/)
* ビデオ
	* [Magnus Mårtensson 氏による Azure Web ジョブ ビデオ](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>その他の Web ジョブ SDK のリソース

* [Web ジョブ SDK のソース コード](https://github.com/Azure/azure-webjobs-sdk)
* [￼￼[BlobTrigger]￼￼ が動作する方法](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Azure Web ジョブ SDK での高度なバインド](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [Web ジョブ SDK を使用した、FREB のファイルの Azure Storage へのアップロード](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Azure がホストする Web ジョブのログに関するメリットが得られる、Azure 外部での Azure Web ジョブのホスティング](http://bypassion.dk/?p=510)
* [Azure Web ジョブを使用したデータ インポート ツールの構築](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* ビデオ
	* [Channel 9 の Azure Web ジョブ ビデオ シリーズ](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>サンプル Web ジョブ アプリケーション

* [GitHub 上で Web ジョブ チームが提供するサンプル アプリケーション](https://github.com/azure/azure-webjobs-sdk-samples)
* [Web ジョブ SDK を使用した Web ジョブ バックエンドを備えた簡単な Azure Web アプリ](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77)。スケジュールされたイベント ドリブンの Web ジョブの使用方法を示します。ブログ記事「[Azure Web ジョブ SDK を使用した SiteMonitR の再構築](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs)」を参照してください。

##<a name="blogs"></a>ブログ

* [Azure のブログ](/blog)
* [Amit Apple のブログ](http://blog.amitapple.com/)。Web ジョブ (SDK ではない) に重点を置いています。
* [Magnus Mårtensson 氏のブログ](http://magnusmartensson.com/)

##<a name="gethelp"></a>Web ジョブに関する支援

* [Web ジョブに関する StackOverflow](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [Web ジョブ SDK に関する StackOverflow](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Azure と ASP.NET のフォーラム](http://forums.asp.net/1247.aspx)
* [Azure App Service Web Apps フォーラム](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Azure Web Apps のユーザーからの意見のサイト](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/)。ハッシュタグ #AzureWebJobs を使用してください。
* [Web ジョブのバグまたは問題を報告する](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)をご覧ください。
 

<!---HONumber=62-->