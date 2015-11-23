<properties
	pageTitle="Azure App Service のデプロイに関するドキュメント"
	description="Azure App Service へのアプリのデプロイについて説明しているドキュメントを紹介します。"
	services="app-service"
	documentationCenter=""
	authors="tdykstra"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/06/2015"
	ms.author="tdykstra"/>

# Azure App Service のデプロイに関するドキュメント

## 概要

この記事では、独自のコンテンツを [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイするために使用可能なメソッドを一覧表示します。操作方法に関する情報が含まれている記事やブログへのリンクも含まれます。追加の記事が発行されたら、このリストに加えられます。

Web アプリをデプロイする最善の方法は、[継続的な配置ワークフロー](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)を設定し、[ソース管理システム](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)に統合することです。自動化により、開発プロセスの効率が向上することに加え、バックアップと復元のプロセスの管理性と信頼性も向上します。

##### クラウドでホストされているソース管理システムからのデプロイ

* [Visual Studio Online を使用した継続的な配信](#vso)
* [Git を使用したリポジトリ Web サイト](#git)
* [Mercurial を使用したリポジトリ Web サイト](#mercurial)
* [Dropbox からのデプロイの自動化](#dropbox)

##### オンプレミスのソース管理システムからのデプロイ

* [Team Foundation Server (TFS) による継続的な配信](#tfs)
* [内部設置型の Git または Mercurial リポジトリ](#onpremises)

##### コマンド ライン ツールを使用したデプロイの自動化

* [MSBuild を使用したデプロイの自動化](#msbuild)
* [FTP ツールとスクリプトを使用してファイルをコピー](#ftp)
* Windows PowerShell(#powershell) を使用したデプロイの自動化
* [.NET 管理 API を使用したデプロイの自動化](#api)
* [Azure コマンド ライン インターフェイス (Azure CLI) からデプロイ](#cli)
* [Web デプロイ コマンド ラインからデプロイ](#webdeploy)
 
##### 統合開発環境 (IDE) からデプロイ

* [Visual Studio から直接デプロイ](#vs)
* [WebMatrix から直接デプロイ](#webmatrix)

もう 1 つのデプロイ オプションとして、[Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy) などのクラウド ベースのサービスを使用することもできます。詳細については、「[Deploy ASP.NET applications to Azure Web Sites (ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする)](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)」をご覧ください。

##<a name="vso"></a>Visual Studio Online を使用した継続的な配信

[Visual Studio Online](http://www.visualstudio.com/) (旧 Team Foundation Service) は、ソース管理とチーム コラボレーションを対象にする、マイクロソフトのクラウドベースのソリューションです。開発者が 5 人以下のチームは、このサービスを無料で使用できます。App Services で Web アプリへの継続的な配信を実行することができ、リポジトリでは [Git または TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) を使用できます。

詳細については、次のリソースを参照してください。

* [Visual Studio Online と TFVC を使用した Azure への継続的な配信](../cloud-services-continuous-delivery-use-vso.md)。TFVC を使用して Visual Studio Online から Web アプリへの継続的な配信を設定する方法を示すステップ バイ ステップ チュートリアルです。分散型ソース管理オプションである Git とは対照的に、TFVC は集中型ソース管理オプションです。
* [Continuous delivery to Azure using Visual Studio Online and Git (Visual Studio Online と Git を使用した Azure への継続的な配信)](../cloud-services-continuous-delivery-use-vso-git.md).上のチュートリアルに似ていますが、TFVC の代わりに Git を使用します。

##<a name="git"></a>Git を使用したリポジトリ Web サイト

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) は、人気の高い分散型ソース管理システムです。Azure には、Git リポジトリを格納する一般的な Web ベースのリポジトリ サイトから Web アプリへの自動的なデプロイを容易にする機能が組み込まれており、その中には [GitHub](http://www.github.com)、[CodePlex](http://www.codeplex.com/)、[BitBucket](https://bitbucket.org/) が含まれています。Git を使用したデプロイに伴う利点は、以前のデプロイが必要になったときに、比較的簡単に以前のデプロイにロールバックできることです。

詳細については、次のリソースを参照してください。

* [Git を使用したソース管理から Web アプリへの発行](web-sites-publish-source-control.md)。Git を使用してローカル コンピューターから Web アプリに直接発行する方法 (Azure では、この発行方法をローカル Git と呼びます)。また、GitHub、CodePlex、BitBucket からの Git リポジトリの継続的なデプロイを有効にする方法も示します。
* [Deploying to Web Apps with GitHub using Kudu (Kudu を使用した GitHub による Web アプリへのデプロイ)](http://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/)。Scott Hanselman と David Ebbo によるビデオ。GitHub から Web アプリへ直接 Web アプリをデプロイする方法を示します。
* [Web アプリに対する Azure ボタンのデプロイ](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/)。Git リポジトリからのデプロイをトリガーする方法に関するブログです。
* [Git、Mercurial、Dropbox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。

##<a name="mercurial"></a>Mercurial を使用したリポジトリ Web サイト

ソース管理システムとして [Mercurial](http://mercurial.selenic.com/) を使用し、リポジトリを [CodePlex](http://www.codeplex.com/) または [BitBucket](https://bitbucket.org/) に保存する場合は、Azure App Service の組み込み機能を使用してコンテンツを自動的にデプロイすることができます。

Mercurial を使用してデプロイを行う方法の詳細については、次のリソースを参照してください。

* [Git を使用したソース管理から Web アプリへの発行](web-sites-publish-source-control.md)。このチュートリアルでは、Git リポジトリを発行する方法を示していますが、CodePlex または BitBucket でホストされている Mercurial リポジトリに関するプロセスは類似しています。
* [Git、Mercurial、Dropbox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。

##<a name="dropbox"></a>Dropbox からのデプロイの自動化

[Dropbox](https://www.dropbox.com/) はソース管理システムではありませんが、ソース コードを DropBox に格納する場合は、DropBox アカウントからのデプロイを自動化することもできます。

* [Dropbox から Web アプリへのデプロイ](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx)。[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)を使用して Dropbox デプロイを設定する方法。
* [Web アプリに対する Dropbox デプロイ](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites)。このビデオでは、Dropbox フォルダーを Web アプリに接続する方法について説明し、Web アプリを迅速に設定して動作させる方法や、簡単なドラッグ アンド ドロップのデプロイを使用して Web サイトを保持する方法を示します。
* [Git、Mercurial、Dropbox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。

##<a name="vs"></a>Visual Studio から直接デプロイ

Visual Studio から Web アプリをデプロイする方法については、次のリソースを参照してください。

* [Azure と ASP.NET を使ってみる](web-sites-dotnet-get-started.md)Visual Studio と Web Deploy を使用して簡単な ASP.NET MVC Web プロジェクトを作成し、デプロイする方法。
* [Visual Studio を使用した Azure WebJobs のデプロイ方法](websites-dotnet-deploy-webjobs.md)。コンソール アプリケーション プロジェクトを Web ジョブとしてデプロイするための構成方法。  
* [メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションの Web アプリへのデプロイ](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。Visual Studio、Web Deploy、Entity Framework、Code First Migrations を使用して、ASP.NET MVC Web プロジェクトを SQL データベースと共に配置する方法。
* [Visual Studio と ASP.NET に関する Web デプロイの概要](http://msdn.microsoft.com/library/dd394698.aspx)Visual Studio を使用して Web デプロイを実行するための基本的な概要です。作成以来期間が経過していますが、現在も引き続き当てはまる情報が掲載されており、その中には Web アプリケーションと共にデータベースをデプロイするためのオプションの概要、実行する必要が生じる可能性のある付加的なデプロイ タスクの一覧、Visual Studio を独自に手動で構成する方法が含まれています。このトピックには、Web アプリへのデプロイのみではなく、デプロイ全般に関する情報も掲載されています。
* [Visual Studio を使用した ASP.NET Web デプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)。12 部構成のチュートリアル シリーズであり、このリスト内に掲載されている他のリソースより詳細にデプロイ タスクの範囲を網羅しています。チュートリアルを作成した後で、不足している事項を説明するためにメモが追加されたため、一部の Azure デプロイ機能が追加されました。
* [Git リポジトリから ASP.NET Web サイトを Visual Studio 2012 内にある Azure へ直接のデプロイ](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。Visual Studio 内で ASP.NET Web プロジェクトをデプロイし、Git プラグインを使用してコードを Git にコミットして、Azure を Git リポジトリに接続する方法について説明します。Visual Studio 2013 以降、Git のサポートは組み込まれており、プラグインのインストールは必要ありません。

##<a name="webmatrix"></a>WebMatrix から直接デプロイ

WebMatrix から Web アプリをデプロイする方法については、次のリソースを参照してください。

* [ WebMatrix を使用した Node.js Web サイトの構築と Azure へのデプロイ](web-sites-nodejs-use-webmatrix.md)
* [WebMatrix を使用した PHP-MySQL Web アプリの作成とデプロイ](web-sites-php-mysql-use-webmatrix.md)。
* [WebMatrix 3: Git の統合と Azure へのデプロイ](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD)。WebMatrix を使用して、Git ソース管理リポジトリからデプロイを実行する方法です。

詳細については、次のリソースを参照してください。

* [PHP-MySQL Web アプリの作成と FTP でのデプロイ](web-sites-php-mysql-deploy-use-ftp.md)。

##<a name="tfs"></a>Team Foundation Server (TFS) による継続的なデプロイ

Team Foundation Server は、ソース管理とチーム コラボレーションを対象にする、マイクロソフトの内部設置型ソリューションです。Web アプリへの継続的な配信を実行するように TFS を設定することができます。

詳細については、次のリソースを参照してください。

* [Windows Azure のクラウド サービスの継続的な配信](../cloud-services-dotnet-continuous-delivery.md)このドキュメントは Azure クラウド サービスを対象にしていますが、そのコンテンツの一部は Web アプリにも当てはまります。

##<a name="gitmercurial"></a>内部設置型の Git または Mercurial リポジトリ

Git または Mercurial を使用する任意のリポジトリからデプロイを実行するために、Azure でそのリポジトリの URL を入力することができます。ローカルの Git リポジトリから Web アプリへの直接のプッシュを実行することもできます。

詳細については、次のリソースを参照してください。

* [Git を使用したソース管理から Web アプリへの発行](web-sites-publish-source-control.md)。Git を使用してローカル コンピューターから Web アプリに直接発行する方法 (Azure では、この発行方法をローカル Git と呼びます)。また、GitHub、CodePlex、BitBucket からの Git リポジトリの継続的なデプロイを有効にする方法も示します。
* [任意の git/hg リポジトリから Web アプリへの発行](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。Web アプリで "外部リポジトリ" 機能を使用する方法を説明しているブログ。
* [Git、Mercurial、Dropbox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。
* [1 つの Git リポジトリから Azure への 2 つの Web サイトのデプロイ](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx)。Scott Hanselman 氏によるブログの投稿です。

##<a name="msbuild"></a>MSBuild を使用したデプロイの自動化

[Visual Studio IDE](#vs) を使用して開発を行う場合は、[MSBuild](http://msbuildbook.com/) を使用して IDE 内のすべての作業を自動化することができます。[Web Deploy](#webdeploy) または [FTP と FTPS](#ftp) を使用してファイルをコピーするように MSBuild を構成することができます。Web Deploy では、データベースのデプロイなど、デプロイに関連する多くのタスクを自動化することもできます。

MSBuild を使用したコマンドライン デプロイの詳細については、次のリソースを参照してください。

* [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。Visual Studio を使用して Azure のデプロイを行うチュートリアル シリーズの第 10 部。Visual Studio でプロファイルの発行を設定した後に、コマンド ラインを使用してデプロイを行う方法を示します。
* [『Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build』](http://msbuildbook.com/)。MSBuild を使用してデプロイを実行する方法に関する章が掲載されている書籍。

##<a name="ftp"></a>FTP ツールおよびスクリプトを使用したファイルのコピー

[FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) を使用してファイルをコピーすることで、コンテンツをアプリにデプロイすることができます。Web アプリ用の FTP 資格情報は簡単に作成できます。これらの資格情報は、スクリプトや FTP と連携するアプリケーション (Internet Explorer などのブラウザーや、[FileZilla](https://filezilla-project.org/) などのフル機能を備えた無料のユーティリティなど) で使用できます。Web アプリでは、より安全な FTPS プロトコルもサポートしています。

FTP ユーティリティを使用して Web アプリのファイルを Azure にコピーすることは簡単ですが、データベースのデプロイや接続文字列の変更のような関連するデプロイ タスク自動的な処理や調整が FTP ユーティリティによって実行されることはありません。また、多くの FTP ツールは、変更されていないファイルのコピーをスキップする目的でコピー元とコピー先のファイルを比較することもありません。大規模なアプリで常にすべてのファイルをコピーする場合は、小規模な更新であっても常にすべてのファイルがコピーされるため、デプロイに長時間を要する可能性があります。

詳細については、次のリソースを参照してください。

* [FTP バッチ スクリプトの使用](http://support.microsoft.com/kb/96269)。

##<a name="powershell"></a>Windows PowerShell を使用したデプロイの自動化

[Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) から MSBuild または FTP デプロイの機能を実行することができます。この機能を実行する場合は、Azure の REST 管理 API を簡単に呼び出せる一連の Windows PowerShell コマンドレットを使用することもできます。

詳細については、次のリソースを参照してください。

* [GitHub リポジトリにリンクされる Web アプリのデプロイ](app-service-web-arm-from-github-provision.md)
* [Web アプリと SQL Database をプロビジョニングする](app-service-web-arm-with-sql-database-provision.md)
* [Azure でマイクロサービスを予測どおりにデプロイする](app-service-deploy-complex-application-predictably.md)
* [Azure での実際のクラウド アプリケーションのビルド - すべてを自動化](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。電子書籍の中に掲載されているサンプル アプリケーションが、Windows PowerShell スクリプトをどのように使用して Azure テスト環境を作成し、その環境をデプロイするかを説明する、電子書籍の章。Azure PowerShell の他のドキュメントへのリンクを掲載している「[リソース](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources)」セクションを参照してください。
* [Windows PowerShell スクリプトを使用した開発環境およびテスト環境の発行](http://msdn.microsoft.com/library/dn642480.aspx)。Visual Studio で生成される Windows PowerShell デプロイ スクリプトの使用方法

##<a name="api"></a>.NET 管理 API を使用したデプロイの自動化

C# のコードを作成し、デプロイの目的で MSBuild や FTP の機能を実行することができます。この作業を実行する場合は、Azure の管理 REST API にアクセスして、サイト管理機能を実行することができます。

詳細については、次のリソースを参照してください。

* [Azure 管理ライブラリと .NET を使用してすべてを自動化](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)。.NET 管理 API の紹介とその他のドキュメントへのリンク。

##<a name="cli"></a>Azure コマンド ライン インターフェイス (Azure CLI) からのデプロイ

Windows、Mac、Linux マシンでコマンド ラインを使用し、FTP を使用してデプロイを実行することもできます。この作業を実行する場合は、Azure CLI を使用して Azure REST 管理 API にアクセスすることもできます。

詳細については、次のリソースを参照してください。

* [Azure コマンド ライン ツール](/downloads/#cmd-line-tools)。コマンド ライン ツールに関する情報を掲載している Azure.com のポータル ページです。

##<a name="webdeploy"></a>Web デプロイ コマンド ラインからのデプロイ

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) は IIS へのデプロイを行うマイクロソフトのソフトウェアであり、インテリジェントなファイルの同期機能に加えて、FTP を使用する場合は自動化できない他の多くのデプロイ関連タスクも実行または調整することができます。たとえば、Web Deploy を使用して Web アプリと共に新しいデータベースをデプロイ、またはデータベースの更新をデプロイすることができます。また、Web Deploy は、変更されたファイルのみをインテリジェントにコピーするため、既存のサイトを更新するのに要する時間を最小化することもできます。Microsoft WebMatrix、Visual Studio、Visual Studio Online、および Team Foundation Server は Web Deploy のビルトイン機能をサポートしていますが、コマンド ラインから Web Deploy を直接使用してデプロイを自動化することもできます。Web Deploy の各コマンドは非常に強力ですが、学習曲線を急上昇させることもできます。

詳細については、次のリソースを参照してください。

* [シンプルな Web アプリ: デプロイ](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/)。Web デプロイの使用を簡単にするために作成したツールに関する David Ebbo のブログ
* [Web デプロイ ツール](http://technet.microsoft.com/library/dd568996)。Microsoft TechNet サイトの公式ドキュメント。作成以来期間が経過していますが、現在も有効な出発点として使用できます。
* [Web Deploy の使用](http://www.iis.net/learn/publish/using-web-deploy)。Microsoft IIS.NET サイトの公式ドキュメント。同じく作成以来期間が経過していますが、現在も有効な出発点として使用できます。
* [StackOverflow](http://www.stackoverflow.com)。コマンドラインから Web Deploy を使用する方法に関する最新の情報を掲載した、出発点として最適な場所です。
* [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。MSBuild は Visual Studio によって使用されるビルド エンジンであり、Web アプリケーションを Web アプリにデプロイする目的でコマンド ラインから使用することもできます。このチュートリアルは、Visual Studio のデプロイに注目したシリーズの一部です。

##<a name="nextsteps"></a>次のステップ

シナリオによっては、Web アプリをステージングと運用バージョンの間で簡単に切り替えられる環境が必要です。詳細については、[Web アプリのステージングされたデプロイ](web-sites-staged-publishing.md)に関するページをご覧ください。

バックアップと復元計画を策定することは、デプロイ ワークフローの重要な部分です。Web Apps のバックアップと復元の機能については、[Web Apps のバックアップ](web-sites-backup.md)に関するページをご覧ください。

Azure のロール ベースのアクセス制御を使用して、Web アプリのデプロイへのアクセスを管理する方法については、[RBAC と Web アプリの公開](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing)に関するブログをご覧ください。

その他のデプロイのトピックについては、[Web アプリに関するドキュメント](/documentation/services/web-sites/)の「デプロイ」セクションをご覧ください。

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、「[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。
 

<!---HONumber=Nov15_HO3-->