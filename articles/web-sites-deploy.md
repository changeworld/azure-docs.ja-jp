<properties linkid="manage-services-how-to-deploy-websites" urlDisplayName="Azure の Web サイトをデプロイする方法" pageTitle="Azure の Web サイトをデプロイする方法" metaKeywords="Azure デプロイ 発行 Web サイト" description="Azure の Web サイトにコンテンツをデプロイするために使用できる方法について説明します。" metaCanonical="" services="web-sites" documentationCenter="" title="Azure の Web サイトをデプロイする方法" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

#Azure の Web サイトのデプロイ方法

独自のコンテンツを Azure の Web サイトにデプロイするための多くのオプションがあります。このトピックでは、各オプションに関する簡単な概要を説明し、詳細情報へのリンクを掲載します。

* [クラウドでホストされているソース管理システムからのデプロイ](#cloud)
	* Visual Studio Online (VSO)
	* Git を使用したリポジトリ Web サイト
	* Mercurial を使用したリポジトリ Web サイト
    * Dropbox
* [IDE からのデプロイ](#ide)
	* Visual Studio
	* WebMatrix
* [FTP ユーティリティを使用したデプロイ](#ftp)
* [内部設置型のソース管理システムからのデプロイ](#onpremises)
	* Team Foundation Server (TFS)
	* 内部設置型の Git または Mercurial リポジトリ
* [コマンド ライン ツールと Azure の REST 管理 API の使用](#commandline)
	* MSBuild
	* FTP スクリプト
	* Windows PowerShell
	* .NET 管理 API
	* クロスプラットフォーム コマンド ライン (xpat-cli)
	* Web デプロイ コマンド ライン


##<a name="cloud"></a>クラウドでホストされているソース管理システムからのデプロイ

Web サイトをデプロイする最善の方法は、[継続的な配置ワークフロー](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)を設定し、[ソース管理システム](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)に統合することです。自動化により、開発プロセスの効率が向上することに加え、バックアップと復元のプロセスの管理性と信頼性も向上します。

まだソース管理を設定していない場合に、作業を開始する最も簡単な方法は、クラウドでホストされているソース管理システムを使用することです。

###<a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) (旧 Team Foundation Service) は、ソース管理とチーム コラボレーションを対象にする、マイクロソフトのクラウドベースのソリューションです。開発者が 5 人以下のチームは、このサービスを無料で使用できます。Azure の Web サイトへの継続的な配置を行うために VSO を設定することや、リポジトリで [Git と TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) のいずれかを使用することができます。

詳細については、次のリソースを参照してください。

* [Azure への継続的な配置](http://www.visualstudio.com/ja-jp/learn/continuous-delivery-in-vs)TFVC を使用して VSO から Azure の Web サイトへの継続的な配置を設定する方法を示すステップ バイ ステップ チュートリアルです。分散型ソース管理オプションである Git とは対照的に、TFVC は VSO 内で使用する集中型ソース管理オプションです。
* [Visual Studio Online を使用した Azure への継続的な配置](/ja-jp/documentation/articles/cloud-services-continuous-delivery-use-vso/)。VSO へのサインアップ方法、ソース管理へのプロジェクトのチェックイン方法、および Azure への継続的な配置 (自動デプロイ) を設定する方法。クラウド サービス向けに作成されていますが、Visual Studio Online 向けにデプロイを設定するプロセスの多くは、Web サイトの場合でも類似しています。

###<a name="git"></a>Git を使用したリポジトリ Web サイト

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) は、人気の高い分散型ソース管理システムです。Azure には、Git リポジトリを格納する一般的な Web ベースのリポジトリ サイトから Azure の Web サイトへの自動的なデプロイを容易にする機能が組み込まれており、その中には [GitHub](http://www.github.com)、[CodePlex](http://www.codeplex.com/)、[BitBucket](https://bitbucket.org/) が含まれています。Git を使用したデプロイに伴う利点は、以前のデプロイが必要になったときに、比較的簡単に以前のデプロイにロールバックできることです。

詳細については、次のリソースを参照してください。

* [ソース管理から Azure の Web サイトへの発行](/ja-jp/documentation/articles/web-sites-publish-source-control/)。Git を使用してローカル コンピューターから Azure の Web サイトに直接発行する方法 (Azure では、この発行方法をローカル Git と呼びます)。また、GitHub、CodePlex、BitBucket からの Git リポジトリの継続的なデプロイを有効にする方法も示します。
* [Git、Mercurial、DropBox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/ja-jp/home?forum=azuregit)

###<a name="mercurial"></a>Mercurial を使用したリポジトリ Web サイト

ソース管理システムとして [Mercurial](http://mercurial.selenic.com/) を使用し、リポジトリを [CodePlex](http://www.codeplex.com/) または [BitBucket](https://bitbucket.org/) に保存する場合は、Azure の Web サイトの組み込み機能を使用してコンテンツを自動的にデプロイすることができます。

Mercurial を使用してデプロイを行う方法の詳細については、次のリソースを参照してください。

* [ソース管理から Azure の Web サイトへの発行](/ja-jp/documentation/articles/web-sites-publish-source-control/)。このチュートリアルでは、Git リポジトリを発行する方法を示していますが、CodePlex または BitBucket でホストされている Mercurial リポジトリに関するプロセスは類似しています。
* [Git、Mercurial、DropBox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/ja-jp/home?forum=azuregit)

###<a name="dropbox"></a>DropBox

[DropBox](https://www.dropbox.com/) はソース管理システムではありませんが、ソース コードを DropBox に格納する場合は、DropBox アカウントからのデプロイを自動化することもできます。

* [Dropbox を使用した Azure へのデプロイ](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx)Azure の管理ポータルを使用して DropBox のデプロイを設定する方法。
* [DropBox と Azure の Web サイト](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites)。このビデオでは、DropBox フォルダーを Azure の Web サイトに接続する方法について説明し、Web サイトを迅速に設定して動作させる方法や、簡単なドラッグ アンド ドロップのデプロイを使用して Web サイトを保持する方法を示します。
* [Git、Mercurial、DropBox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/ja-jp/home?forum=azuregit)







##<a name="ide"></a>IDE からのデプロイ

[Visual Studio](http://www.visualstudio.com/ja-jp/downloads/download-visual-studio-vs.aspx) と [WebMatrix](http://www.microsoft.com/web/webmatrix/) は Microsoft の IDE (統合開発環境) であり、Web 開発に使用できます。どちらにも、Azure の Web サイトのデプロイを容易にする組み込みの機能があります。どちらも [Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) を使用して、データベースのデプロイや接続文字列の変更など、デプロイに関連する追加のタスクを自動化することができます。どちらも [FTP または FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol)) を使用してデプロイを実行することができます。

WebMatrix は迅速にインストールでき、習得が容易ですが、Visual Studio には Azure の Web サイトを操作するためのさらに多くの機能が備わっています。Visual Studio IDE から Azure の Web サイトを作成、停止、起動、削除することができ、リアルタイムで作成されたログを表示することや、リモートでデバッグすることなども可能です。Visual Studio には、[Visual Studio Online](#vso)、[Team Foundation Server](#tfs)、[Git リポジトリ](#git) のようなソース管理システムも統合されています。

###<a name="vs"></a>Visual Studio

Visual Studio から Azure の Web サイトをデプロイする方法については、次のリソースを参照してください。

* [Azure と ASP.NET を使ってみる](/ja-jp/develop/net/tutorials/get-started/)Visual Studio と Web Deploy を使用して簡単な ASP.NET MVC Web プロジェクトを作成し、デプロイする方法。
* [メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションを Azure の Web サイトに展開する](/ja-jp/develop/net/tutorials/web-site-with-sql-database/)。Visual Studio、Web Deploy、Entity Framework、Code First Migrations を使用して、ASP.NET MVC Web プロジェクトを SQL データベースと共に配置する方法。
* [Visual Studio と ASP.NET に関する Web デプロイの概要](http://msdn.microsoft.com/ja-jp/library/dd394698.aspx)Visual Studio を使用して Web デプロイを実行するための基本的な概要です。作成以来期間が経過していますが、現在も引き続き当てはまる情報が掲載されており、その中には Web アプリケーションと共にデータベースをデプロイするためのオプションの概要、実行する必要が生じる可能性のある付加的なデプロイ タスクの一覧、Visual Studio を独自に手動で構成する方法が含まれています。このトピックには、Azure の Web サイトへのデプロイのみではなく、デプロイ全般に関する情報も掲載されています。
* [Visual Studio を使用した ASP.NET Web デプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)12 部構成のチュートリアル シリーズであり、このリスト内に掲載されている他のリソースより詳細にデプロイ タスクの範囲を網羅しています。
* [Git リポジトリから Visual Studio 2012 内にある ASP.NET Web サイトへの直接のデプロイ](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。Visual Studio 内で ASP.NET Web プロジェクトをデプロイし、Git プラグインを使用してコードを Git にコミットして、Azure を Git リポジトリに接続する方法について説明します。

###<a name="webmatrix"></a>WebMatrix

WebMatrix から Azure の Web サイトをデプロイする方法については、次のリソースを参照してください。

* [Microsoft WebMatrix を使用して Web サイトを開発およびデプロイする](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-dotnet-using-webmatrix/)WebMatrix テンプレートを使用して簡単な ASP.NET Web サイトを作成し、WebMatrix と Web Deploy を使用してその Web サイトを Azure の Web サイトにデプロイする方法。
* [ WebMatrix を使用した Node.js Web サイトの構築と Azure へのデプロイ](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-nodejs-use-webmatrix/)
* [WebMatrix を使用して PHP-MySQL Azure の Web サイトを作成およびデプロイする](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-php-mysql-use-webmatrix/)
* [WebMatrix 3: Git の統合と Azure へのデプロイ](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD)。WebMatrix を使用して、Git ソース管理リポジトリからデプロイを実行する方法です。








##<a name="ftp"></a>FTP ユーティリティを使用したデプロイ

どの IDE を使用しているかにかかわりなく、[FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) を使用してファイルをコピーする方法で、コンテンツをサイトにデプロイすることができます。Azure の Web サイト用の FTP 資格情報は簡単に作成でき、Internet Explorer のようなブラウザーや、[FileZilla](https://filezilla-project.org/) のような多彩な機能を備えた無料のユーティリティを含め、FTP と組み合わせて動作する任意のアプリケーションでその資格情報を使用することができます。Azure の Web サイトでは、より安全な FTPS プロトコルもサポートしています。

FTP ユーティリティを使用して Web サイトのファイルを Azure にコピーすることは簡単ですが、データベースのデプロイや接続文字列の変更のような関連するデプロイ タスク自動的な処理や調整が FTP ユーティリティによって実行されることはありません。また、多くの FTP ツールは、変更されていないファイルのコピーをスキップする目的でコピー元とコピー先のファイルを比較することもありません。大規模なサイトで常にすべてのファイルをコピーする場合は、小規模な更新であっても常にすべてのファイルがコピーされるため、デプロイに長時間を要する可能性があります。

詳細については、次のリソースを参照してください。

* [PHP-MySQL Azure の Web サイトを作成して FTP でデプロイする](/ja-jp/documentation/articles/web-sites-php-mysql-deploy-use-ftp/)。
* [Web サイトの管理方法](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-manage/)。FTP の資格情報を設定する方法に関して、PHP のチュートリアルで網羅されていない追加の情報が含まれます。資格情報を取得する方法に関して、このドキュメント自体に記載されていない点の詳細については、ページの下部にあるコメントを参照してください。








##<a name="onpremises"></a>内部設置型のソース管理システムからのデプロイ

TFS、Git、または Mercurial を (クラウド ホステットではなく) 内部設置型リポジトリで使用している場合は、リポジトリから直接 Azure の Web サイトにデプロイすることができます。

###<a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server は、ソース管理とチーム コラボレーションを対象にする、マイクロソフトの内部設置型ソリューションです。Azure の Web サイトへの継続的な配置を実行するように TFS を設定することができます。

詳細については、次のリソースを参照してください。

* [Azure のクラウド サービスの継続的な配信](/ja-jp/develop/net/common-tasks/continuous-delivery/)このドキュメントは Azure クラウド サービスを対象にしていますが、そのコンテンツの一部は Web サイトにも当てはまります。

###<a name="gitmercurial"></a>内部設置型の Git または Mercurial リポジトリ

Git または Mercurial を使用する任意のリポジトリからデプロイを実行するために、Azure でそのリポジトリの URL を入力することができます。ローカルの Git リポジトリから Azure の Web サイトへの直接のプッシュを実行することもできます。

詳細については、次のリソースを参照してください。

* [ソース管理から Azure の Web サイトへの発行](/ja-jp/documentation/articles/web-sites-publish-source-control/)。Git を使用してローカル コンピューターから Azure の Web サイトに直接発行する方法 (Azure では、この発行方法をローカル Git と呼びます)。また、GitHub、CodePlex、BitBucket からの Git リポジトリの継続的なデプロイを有効にする方法も示します。
* [任意の git/hg リポジトリから Azure の Web サイトへの発行](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。David Ebbo 氏によって作成されたブログであり、Azure の Web サイトで "外部リポジトリ" 機能を使用する方法を説明しています。
* [Git、Mercurial、DropBox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/ja-jp/home?forum=azuregit)
* [1 つの Git リポジトリから Azure への 2 つの Web サイトのデプロイ](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx)。Scott Hanselman 氏によるブログの投稿です。











##<a name="commandline"></a> コマンド ライン ツールと Azure の REST 管理 API の使用

いつでも開発ワークフローを自動化できるのが最善ですが、ソース管理システムからそのような自動化を直接実行できない場合は、コマンド ライン ツールを使用して手動で設定することができます。多くの場合、デプロイにはサイト管理機能とコンテンツのコピーが関係するため、一般的にこの作業には複数のツールやフレームワークを使用することが関係します。

Azure には、REST 管理 API と、その API を使用した作業をさらに簡単にする複数のフレームワークが備わっているため、Azure を使用しない場合に比べると、デプロイに関連して必要とされるサイト管理タスクが簡略化されます。

###<a name=msbuild></a>MSBuild

[Visual Studio IDE](#vs) を使用して開発を行う場合は、[MSBuild](http://msbuildbook.com/) を使用して IDE 内のすべての作業を自動化することができます。[Web Deploy](#webdeploy) または [FTP と FTPS](#ftp) を使用してファイルをコピーするように MSBuild を構成することができます。Web Deploy では、データベースのデプロイなど、デプロイに関連する多くのタスクを自動化することもできます。

MSBuild を使用したコマンドライン デプロイの詳細については、次のリソースを参照してください。

* [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。Visual Studio を使用して Azure のデプロイを行うチュートリアル シリーズの第 10 部。Visual Studio でプロファイルの発行を設定した後に、コマンド ラインを使用してデプロイを行う方法を示します。
『* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/)』。MSBuild を使用してデプロイを実行する方法に関する章が掲載されている書籍。

###<a name="ftp2"></a>FTP スクリプト

Azure の Web サイト用に [FTP と FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) の資格情報を簡単に作成し、その後、FTP バッチ スクリプトでその資格情報を使用することができます。

詳細については、次のリソースを参照してください。

* [FTP バッチ スクリプトの使用](http://support.microsoft.com/kb/96269)。

###<a name="powershell"></a>Windows PowerShell

[Windows PowerShell](http://msdn.microsoft.com/ja-jp/library/dd835506.aspx) から MSBuild または FTP の機能を実行することができます。この機能を実行する場合は、Azure の REST 管理 API を簡単に呼び出せる一連の Windows PowerShell コマンドレットを使用することもできます。

詳細については、次のリソースを参照してください。

* [Azure での実際のクラウド アプリケーションのビルド - すべてを自動化](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。電子書籍の中に掲載されているサンプル アプリケーションが、Windows PowerShell スクリプトをどのように使用して Azure テスト環境を作成し、その環境をデプロイするかを説明する、電子書籍の章。Azure PowerShell の他のドキュメントへのリンクを掲載している「[リソース](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources)」セクションを参照してください。

###<a name="api"></a>.NET 管理 API

C# のコードを作成し、デプロイの目的で MSBuild や FTP の機能を実行することができます。この作業を実行する場合は、Azure の管理 REST API にアクセスして、サイト管理機能を実行することができます。

詳細については、次のリソースを参照してください。

* [Azure 管理ライブラリと .NET を使用してすべてを自動化](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)。.NET 管理 API の紹介とその他のドキュメントへのリンク。

###<a name="cli"></a>クロスプラットフォーム コマンド ライン (xpat-cli)

Mac または Linux マシンでコマンド ラインを使用し、FTP を使用してデプロイを実行することもできます。この作業を実行する場合は、Azure のクロスプラットフォーム コマンド ライン インターフェイス (xpat cli) を使用して Azure REST 管理 API にアクセスすることもできます。Windows マシンでも xpat-cli を使用できます。

詳細については、次のリソースを参照してください。

* [コマンド ライン ツール](/ja-jp/downloads/#cmd-line-tools)。コマンド ライン ツールに関する情報を掲載している WindowsAzure.com のポータル ページです。

###<a name="webdeploy"></a>Web デプロイ コマンド ライン

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) は IIS へのデプロイを行うマイクロソフトのソフトウェアであり、インテリジェントなファイルの同期機能に加えて、FTP を使用する場合は自動化できない他の多くのデプロイ関連タスクも実行または調整することができます。たとえば、Web Deploy を使用して Web サイトと共に新しいデータベースをデプロイ、またはデータベースの更新をデプロイすることができます。また、Web Deploy は、変更されたファイルのみをインテリジェントにコピーするため、既存のサイトを更新するのに要する時間を最小化することもできます。Microsoft WebMatrix、Visual Studio、Visual Studio Online、および Team Foundation Server は Web Deploy のビルトイン機能をサポートしていますが、コマンド ラインから Web Deploy を直接使用してデプロイを自動化することもできます。Web Deploy の各コマンドは非常に強力ですが、学習曲線を急上昇させることもできます。

詳細については、次のリソースを参照してください。

* [Web デプロイ ツール](http://technet.microsoft.com/ja-jp/library/dd568996)。Microsoft TechNet サイトの公式ドキュメント。作成以来期間が経過していますが、現在も有効な出発点として使用できます。
* [Web Deploy の使用](http://www.iis.net/learn/publish/using-web-deploy)。Microsoft IIS.NET サイトの公式ドキュメント。同じく作成以来期間が経過していますが、現在も有効な出発点として使用できます。
* [StackOverflow](http://www.stackoverflow.com)。コマンドラインから Web Deploy を使用する方法に関する最新の情報を掲載した、出発点として最適な場所です。
* [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。MSBuild は Visual Studio によって使用されるビルド エンジンであり、Web アプリケーションを Azure の Web サイトにデプロイする目的でコマンド ラインから使用することもできます。このチュートリアルは、Visual Studio のデプロイに注目したシリーズの一部です。








##<a name="nextsteps"></a>次のステップ
詳細については、[Azure の Web サイトに関するドキュメント](/ja-jp/documentation/services/web-sites/)の「デプロイ」セクションを参照してください。

