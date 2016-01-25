<properties
	pageTitle="Azure App Service へのアプリのデプロイ"
	description="Azure App Service にアプリをデプロイする方法について説明します。"
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cephalin;tdykstra"/>
    
# Azure App Service へのアプリのデプロイ

この記事は、Web アプリ、モバイル アプリ バックエンド、または API アプリのファイルを [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする際の最適なオプションを特定するのに役立ちます。また、選択したオプションに固有の手順が記載された適切な記事やブログも紹介しています。

## <a name="overview"></a>デプロイメント プロセスの概要

Azure App Service でアプリを作成またはプロビジョニングすると、コードをデプロイする前に、Azure App Service によってアプリケーション フレームワーク (ASP.NET、PHP、Node.js など) が自動的に保持されます。既定で有効化されるフレームワークもあれば、有効にするために簡単なチェックマークによる構成が必要なフレームワーク (Java や Python など) もあります。また、PHP のバージョンやランタイムのビット数など、アプリケーション フレームワークをカスタマイズすることもできます。詳細については、「[Azure App Service での Web アプリの構成](web-sites-configure.md)」をご覧ください。

Web サーバーやアプリケーション フレームワークを気にする必要がないので、アプリを App Service にデプロイするときは、コード、バイナリ、コンテンツ ファイル、それぞれのディレクトリ構造を、[Azure の **/site/wwwroot** ディレクトリ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (Web ジョブの場合は **/Data/Jobs** ディレクトリ) にデプロイすることが重要になります。App Service では、次の 3 つのデプロイメント プロセスをサポートしています。

- [FTP または FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): [FileZilla](https://filezilla-project.org) から [NetBeans](https://netbeans.org) などのフル機能を備えた IDE まで、好みの FTP または FTPS 対応ツールを使用して、ファイルを Azure に移動します。これは、厳密なファイル アップロード プロセスです。App Service からバージョン管理やファイル構造管理などの追加サービスは提供されません。 
- [Kudu (Git/Mercurial)](https://github.com/projectkudu/kudu/wiki/Deployment): App Service の[デプロイ エンジン](https://github.com/projectkudu/kudu/wiki)。任意のリポジトリから Kudu にコードを直接プッシュします。Kudu では、コードをプッシュしたときに、継続的なデプロイと他の自動タスクのための追加サービス (バージョン管理、パッケージの復元、MSBuild、[Web フック](https://github.com/projectkudu/kudu/wiki/Web-hooks)など) も提供されます。これらのサービスはすべてカスタマイズ可能であり、次の状況でトリガーされます。 
    - 構成済みの Git リポジトリから **git push** が実行されたとき
	- 構成済みの Mercurial リポジトリから **hg push** が実行されたとき 
    - Dropbox や OneDrive などのリンクされているクラウド ストレージが App Service と同期されたとき 
- [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): IIS サーバーへのデプロイを自動化するツールと同じものです。好みの Microsoft ツール (Visuall Studio、WebMatrix、Visual Studio Team Services など) から App Service にコードを直接デプロイします。このツールは、差分のみのデプロイ、データベースの作成、接続文字列の変換などをサポートします。Web Deploy は、Azure にデプロイする前にアプリケーション バイナリがビルドされる点で Kudu とは異なります。FTP と同様に、App Service から追加サービスは提供されません。

一般的な Web 開発ツールでは、これらのデプロイメント プロセスを 1 つ以上サポートします。選択したツールによって利用できるデプロイメント プロセスが決まりますが、自由に使える実際の DevOps 機能は、デプロイメント プロセスと選択したツールの組み合わせによって異なります。たとえば、[Visual Studio と Azure SDK](#vspros) から Web Deploy を実行する場合、Kudu の自動化機能を利用できなくても、Visual Studio でパッケージの復元と MSBuild による自動化が提供されます。Azure SDK には、必要な Azure リソースを Visual Studio インターフェイス内で直接作成できる使いやすいウィザードも用意されています。

>[AZURE.NOTE]これらのデプロイメント プロセスでは、実際には、App Service プラン、App Service アプリ、SQL Database など、アプリで必要な [Azure リソースがプロビジョニング](resource-group-portal)されるわけではありません。ただし、リンクされている手順に関する記事のほとんどは、アプリをプロビジョニングし、コードをエンド ツー エンドでデプロイする方法を示しています。また、「[コマンド ライン ツールを使用したデプロイの自動化](#automate)」では、Azure リソースをプロビジョニングするための追加オプションを紹介しています。

## <a name="ftp"></a>Azure へのファイルの手動コピーによるデプロイ
Web コンテンツを Web ホスト側に手動でコピーする(PHP 開発者向けの一般的なワークフロー) ことに慣れている場合は、エクスプローラーや [FileZilla](https://filezilla-project.org/) などの [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) ユーティリティを使用してファイルをコピーできます。

ファイルの手動コピーでは、デプロイに FTP プロトコルを利用します (「[デプロイメント プロセスの概要](#overview)」をご覧ください)。

ファイルの手動コピーの利点は次のとおりです。

- FTP ツールを使い慣れていること。 
- ファイルのコピー先が正確にわかります。
- FTPS を使用するとセキュリティが強化されます。
- 最小限のツールで Web 開発を行う場合 (メモ帳を使用した Web アプリの開発など) に最適なデプロイメント ソリューションです。

ファイルの手動コピーの欠点は次のとおりです。

- 開発者が、App Service の適切なディレクトリにファイルをデプロイする必要があります。
- エラーが発生したときにロールバックするためのバージョン管理が行われません。
- 多くの FTP ツールでは、差分のみのコピーは行われず、すべてのファイルがコピーされます。そのため、大規模なアプリの場合、小規模な更新でもデプロイに時間がかかります。

### <a name="howtoftp"></a>Azure にファイルを手動でコピーしてデプロイする方法
ファイルを Azure にコピーするには、次の簡単な手順を実行します。

1. [Azure ポータル](https://portal.azure.com)でアプリのデプロイ資格情報を作成します。そのためには、アプリのブレードで **[設定]**、**[デプロイ資格情報]** の順にクリックします。
2. デプロイ資格情報を構成したら、**[設定]** > **[プロパティ]** に移動し、**[FTP/デプロイ ユーザー]**、**[FTP ホスト名]**、**[FTPS ホスト名]** の値をコピーして、FTP 接続情報を取得します。
3. FTP クライアントから、収集した接続情報を使用してアプリに接続します。
4. ファイルとそれぞれのディレクトリ構造を、[Azure の **/site/wwwroot** ディレクトリ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (Web ジョブの場合は **/Data/Jobs** ディレクトリ) にコピーします。
5. アプリの URL を参照して、アプリが正しく動作していることを確認します。 

詳細については、次のリソースを参照してください。

* [PHP-MySQL Web アプリの作成と FTP でのデプロイ](web-sites-php-mysql-deploy-use-ftp.md)。
* [FTP バッチ スクリプトの使用](http://support.microsoft.com/kb/96269)。

## <a name="dropbox"></a>クラウド フォルダーとの同期によるデプロイ
[ファイルの手動コピー](#ftp)に代わる優れた方法として、OneDrive や Dropbox などのクラウド ストレージ サービスから App Service にファイルとフォルダーを同期する方法があります。[Azure ポータル](https://portal.azure.com)では、クラウド ストレージに特別なフォルダーを構成して、そのフォルダー内でアプリのコードとコンテンツの作業を行い、ボタンをクリックするだけで App Service に同期できます。

クラウド フォルダーとの同期では、デプロイに Kudu プロセスを利用します (「[デプロイメント プロセスの概要](#overview)」をご覧ください)。

クラウド フォルダーとの同期の利点は次のとおりです。

- デプロイのシンプルさ。OneDrive や Dropbox などのサービスは、デスクトップ同期クライアントを提供するので、ローカルの作業ディレクトリもデプロイメント ディレクトリになります。
- ワンクリックでデプロイできます。
- Kudu のすべての機能 (デプロイメントのバージョン管理、ロールバック、パッケージの復元、自動化など) を利用できます。
- 最小限のツールで Web 開発を行う場合に最適なデプロイメント ソリューションです。

クラウド フォルダーとの同期の欠点は次のとおりです。

- このソリューションは、チーム プロジェクトには適していません。

### <a name="howtodropbox"></a>クラウド フォルダーと同期してデプロイする方法
 
* [Dropbox から Web Apps へのデプロイ](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx)。[Azure ポータル](https://portal.azure.com)を使用して Dropbox デプロイを設定する方法。
* [Web アプリに対する Dropbox デプロイ](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites)。このビデオでは、Dropbox フォルダーを Web アプリに接続する方法について説明し、Web アプリを迅速に設定して動作させる方法や、簡単なドラッグ アンド ドロップのデプロイを使用して Web サイトを保持する方法を示します。
* [Git、Mercurial、Dropbox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。

## IDE を使用したデプロイ
[Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) と [Azure SDK](https://azure.microsoft.com/downloads/) または WebMatrix、あるいは [Xcode](https://developer.apple.com/xcode/) や [Eclipse](https://www.eclipse.org) などの他の IDE スイートを既に使用している場合は、IDE 内から直接 Azure にデプロイできます。このオプションは個人開発者に最適です。

Visual Studio は 3 つのデプロイメント プロセス (FTP、Git、Web Deploy) をすべてサポートしていますが、FTP または Git と統合されていれば、他の IDE でも App Service にデプロイできます (「[デプロイメント プロセスの概要](#overview)」をご覧ください)。

IDE を使用したデプロイの利点は次のとおりです。

- エンド ツー エンドのアプリケーション ライフ サイクルのために、ツールが最小限に抑えられる可能性があります。IDE の外部に移動せずに、アプリの開発、デバッグ、追跡、Azure へのデプロイをすべて行うことができます。 

IDE を使用したデプロイの欠点は次のとおりです。

- ツールの複雑さが増します。
- チーム プロジェクトの場合、ソース管理システムが引き続き必要になります。

<a name="vspros"></a>Visual Studio と Azure SDK を使用したデプロイのその他の利点は次のとおりです。

- Azure SDK により、Visual Studio で Azure リソースが主要リソースになります。アプリの作成、削除、編集、起動、および終了、バックエンドの SQL データベースのクエリ、Azure アプリのライブ デバッグなどが可能です。 
- Azure でのコード ファイルのライブ編集。
- Azure でのアプリのライブ デバッグ。
- Azure エクスプローラーの統合。
- 差分のみのデプロイ。 

###<a name="vs"></a>Visual Studio から直接デプロイする方法

* [Azure と ASP.NET を使ってみる](web-sites-dotnet-get-started.md)Visual Studio と Web Deploy を使用して簡単な ASP.NET MVC Web プロジェクトを作成し、デプロイする方法。
* [Visual Studio を使用した Azure WebJobs のデプロイ方法](websites-dotnet-deploy-webjobs.md)。コンソール アプリケーション プロジェクトを Web ジョブとしてデプロイするための構成方法。  
* [メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションの Web アプリへのデプロイ](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。Visual Studio、Web Deploy、Entity Framework、Code First Migrations を使用して、ASP.NET MVC Web プロジェクトを SQL データベースと共に配置する方法。
* [Visual Studio と ASP.NET に関する Web デプロイの概要](http://msdn.microsoft.com/library/dd394698.aspx)Visual Studio を使用して Web デプロイを実行するための基本的な概要です。作成以来期間が経過していますが、現在も引き続き当てはまる情報が掲載されており、その中には Web アプリケーションと共にデータベースをデプロイするためのオプションの概要、実行する必要が生じる可能性のある付加的なデプロイ タスクの一覧、Visual Studio を独自に手動で構成する方法が含まれています。このトピックには、Web Apps へのデプロイのみではなく、デプロイ全般に関する情報も掲載されています。
* [Visual Studio を使用した ASP.NET Web デプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)。12 部構成のチュートリアル シリーズであり、このリスト内に掲載されている他のリソースより詳細にデプロイ タスクの範囲を網羅しています。チュートリアルを作成した後で、不足している事項を説明するためにメモが追加されたため、一部の Azure デプロイ機能が追加されました。
* [Git リポジトリから ASP.NET Web サイトを Visual Studio 2012 内にある Azure へ直接のデプロイ](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。Visual Studio 内で ASP.NET Web プロジェクトをデプロイし、Git プラグインを使用してコードを Git にコミットして、Azure を Git リポジトリに接続する方法について説明します。Visual Studio 2013 以降、Git のサポートが組み込まれており、プラグインをインストールする必要はありません。

###<a name="webmatrix"></a>WebMatrix から直接デプロイする方法

* [ WebMatrix を使用した Node.js Web サイトの構築と Azure へのデプロイ](web-sites-nodejs-use-webmatrix.md)
* [WebMatrix を使用した PHP-MySQL Web アプリの作成とデプロイ](web-sites-php-mysql-use-webmatrix.md)。
* [WebMatrix 3: Git の統合と Azure へのデプロイ](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD)。WebMatrix を使用して、Git ソース管理リポジトリからデプロイを実行する方法です。

## <a name="onprem"></a>オンプレミスのソース管理システムからのデプロイ

任意の規模の開発チームで、[Team Foundation Server](https://www.visualstudio.com/products/tfs-overview-vs.aspx) (TFS)、[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs)、[Mercurial](http://mercurial.selenic.com/) などのオンプレミスのソース コード管理 (SCM) システムを使用している場合、リポジトリと統合し、ソース管理ワークフローで App Service に直接デプロイするように App Service を構成できます。TFS を使用している場合、App Service に継続的にデプロイするように TFS を構成することもできます。

TFS では Web Deploy を使用して App Service にデプロイしますが、Git/Mercurial リポジトリからのデプロイでは Kudu を使用します (「[デプロイメント プロセスの概要](#overview)」をご覧ください)。

オンプレミスのソース管理システムからのデプロイの利点は次のとおりです。

- 任意の言語フレームワーク、あるいは Git または Mercurial クライアント ([Xcode](https://developer.apple.com/xcode/) や [Eclipse](https://www.eclipse.org) など) からのデプロイをサポートします。
- 分岐固有のデプロイでは、個々の[スロット](web-sites-staged-publishing)にそれぞれ異なるバージョンをデプロイできます。
- あらゆる規模の開発チームに適しています。

オンプレミスのソース管理システムからのデプロイの欠点は次のとおりです。

- 選択した SCM システムの知識が必要です。
- 必要以上の機能が提供される場合があります。
- Git/Mercurial クライアント ツールからの継続的なデプロイと分岐固有のデプロイのためのターンキー ソリューションがありません。 

TFS を使用したデプロイのその他の利点は次のとおりです。

- ビルド、テスト、デプロイのための継続的インテグレーション (CI)。
- 既存の IDE またはエディターで動作する組み込みのコラボレーション ツール。
- 分散型バージョン管理では Git をサポートし、一元的なバージョン管理では Team Foundation バージョン管理 (TFVC) をサポート。 
- アジャイル デプロイメントのための豊富なツール。
- [Jenkins](https://jenkins-ci.org)、[Slack](https://slack.com)、[ZenDesk](https://www.zendesk.com)、[Trello](https://trello.com)、[Azure Service Bus](/services/service-bus/) などに対応する既製の統合。 
- 開発者が 5 人以下のチームには、[Team Foundation Server Express](https://www.microsoft.com/download/details.aspx?id=48259) を無料で提供。

###<a name="tfs"></a>TFS を使用して継続的にデプロイする方法

* [Microsoft Azure のクラウド サービスの継続的な配信](../cloud-services-dotnet-continuous-delivery.md)このドキュメントは Azure クラウド サービスを対象にしていますが、そのコンテンツの一部は Web Apps にも当てはまります。

###<a name="gitmercurial"></a>オンプレミスの Git または Mercurial リポジトリからデプロイする方法

* [Git を使用したソース管理から Web アプリへの発行](web-sites-publish-source-control.md)。Git を使用してローカル コンピューターから Web アプリに直接発行する方法 (Azure では、この発行方法をローカル Git と呼びます)。また、GitHub、CodePlex、BitBucket からの Git リポジトリの継続的なデプロイを有効にする方法も示します。
* [任意の git/hg リポジトリから Web アプリへの発行](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。Web Apps で "外部リポジトリ" 機能を使用する方法を説明しているブログ。
* [Git、Mercurial、Dropbox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。
* [1 つの Git リポジトリから Azure への 2 つの Web サイトのデプロイ](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx)。Scott Hanselman 氏によるブログの投稿です。

## クラウド ベースのソース管理サービスからのデプロイ
任意の規模の開発チームで、[Visual Studio Team Services](http://www.visualstudio.com/) (旧 Visual Studio Online)、[GitHub](https://www.github.com)、[GitLab](https://gitlab.com)、[BitBucket](https://bitbucket.org/)、[CodePlex](https://www.codeplex.com/)、[Codebase](https://www.codebasehq.com)、[Kiln](https://www.fogcreek.com/kiln/) などのクラウド ベースのソース コード管理 (SCM) サービスを使用している場合、リポジトリと統合し、継続的にデプロイするように App Service を構成できます。

Visual Studio Team Services では Web Deploy を使用して App Service にデプロイしますが、オンライン リポジトリからのデプロイでは Kudu を使用します (「[デプロイメント プロセスの概要](#overview)」をご覧ください)。

クラウド ベースのソース管理サービスからのデプロイの利点は次のとおりです。

- あらゆる言語フレームワークのサポート。
- Git/Mercurial リポジトリ用の構成を必要としない継続的なデプロイ。 
- 分岐固有のデプロイでは、個々の[スロット](web-sites-staged-publishing)にそれぞれ異なる分岐をデプロイできます。
- あらゆる規模の開発チームに適しています。

クラウド ベースのソース管理サービスからのデプロイの欠点は次のとおりです。

- 選択した SCM サービスの知識が必要です。
- 必要以上の機能が提供される場合があります。

Visual Studio Team Services を使用したデプロイのその他の利点は次のとおりです。

- 開発者が 5 人以下のチームには無料で提供。
- ビルド、テスト、デプロイのための継続的インテグレーション (CI)。
- 既存の IDE またはエディターで動作する組み込みのコラボレーション ツール。
- 分散型バージョン管理では Git をサポートし、一元的なバージョン管理では Team Foundation バージョン管理 (TFVC) をサポート。 
- アジャイル デプロイメントのための豊富なツール。
- [Jenkins](https://jenkins-ci.org)、[Slack](https://slack.com)、[ZenDesk](https://www.zendesk.com)、[Trello](https://trello.com)、[Azure Service Bus](/services/service-bus/) などに対応する既製の統合。 
- [Power BI に接続](https://www.visualstudio.com/get-started/report/report-on-vso-with-power-bi-vs)してレポートを簡単に作成できる豊富なダッシュボード。

###<a name="vsts"></a>Visual Studio Team Services を使用して継続的にデプロイする方法

- [Visual Studio Team Services と TFVC を使用した Azure への継続的な配信](../cloud-services-continuous-delivery-use-vso.md)。TFVC を使用して Visual Studio Team Services から Web アプリへの継続的な配信を設定する方法を示すステップ バイ ステップ チュートリアルです。 
- [Visual Studio Team Services と Git を使用した Azure への継続的な配信](../cloud-services-continuous-delivery-use-vso-git.md)。上のチュートリアルに似ていますが、TFVC の代わりに Git を使用します。

###<a name="cloudgitmercurial"></a>クラウド ベースの Git または Mercurial リポジトリからデプロイする方法

- [Git を使用したソース管理から Web アプリへの発行](web-sites-publish-source-control.md)。GitHub、CodePlex、または BitBucket からのリポジトリの継続的なデプロイを有効にする方法。このチュートリアルでは、Git リポジトリを発行する方法を示していますが、CodePlex または BitBucket でホストされている Mercurial リポジトリに関するプロセスは類似しています。
- [Deploying to Web Apps with GitHub using Kudu (Kudu を使用した GitHub による Web アプリへのデプロイ)](http://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/)。Scott Hanselman と David Ebbo によるビデオ。GitHub から App Service に直接 Web アプリをデプロイする方法を示します。
- [Web アプリに対する Azure ボタンのデプロイ](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/)。Git リポジトリからのデプロイをトリガーする方法に関するブログです。
- [Git、Mercurial、Dropbox に関する Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit)。

詳細については、次のリソースを参照してください。

- [Git を使用したソース管理から Web アプリへの発行](web-sites-publish-source-control.md)。Git を使用してローカル コンピューターから Web Apps に直接発行する方法 (Azure では、この発行方法をローカル Git と呼びます)。 

## <a name="automate"></a>コマンド ライン ツールを使用したデプロイの自動化

* [MSBuild を使用したデプロイの自動化](#msbuild)
* [FTP ツールとスクリプトを使用してファイルをコピー](#ftp)
* [Windows PowerShell を使用したデプロイの自動化](#powershell)
* [.NET 管理 API を使用したデプロイの自動化](#api)
* [Azure コマンド ライン インターフェイス (Azure CLI) からデプロイ](#cli)
* [Web デプロイ コマンド ラインからデプロイ](#webdeploy)
* [FTP バッチ スクリプトの使用](http://support.microsoft.com/kb/96269)。
 
もう 1 つのデプロイ オプションとして、[Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy) などのクラウド ベースのサービスを使用することもできます。詳細については、「[Deploy ASP.NET applications to Azure Web Sites](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)」をご覧ください。

###<a name="msbuild"></a>MSBuild を使用したデプロイの自動化

[Visual Studio IDE](#vs) を使用して開発を行う場合は、[MSBuild](http://msbuildbook.com/) を使用して IDE 内のすべての作業を自動化することができます。[Web Deploy](#webdeploy) または [FTP と FTPS](#ftp) を使用してファイルをコピーするように MSBuild を構成することができます。Web Deploy では、データベースのデプロイなど、デプロイに関連する多くのタスクを自動化することもできます。

MSBuild を使用したコマンドライン デプロイの詳細については、次のリソースを参照してください。

* [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。Visual Studio を使用して Azure のデプロイを行うチュートリアル シリーズの第 10 部。Visual Studio でプロファイルの発行を設定した後に、コマンド ラインを使用してデプロイを行う方法を示します。
* [『Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build』](http://msbuildbook.com/)。MSBuild を使用してデプロイを実行する方法に関する章が掲載されている書籍。

###<a name="powershell"></a>Windows PowerShell を使用したデプロイの自動化

[Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) から MSBuild または FTP デプロイの機能を実行することができます。この機能を実行する場合は、Azure の REST 管理 API を簡単に呼び出せる一連の Windows PowerShell コマンドレットを使用することもできます。

詳細については、次のリソースを参照してください。

* [GitHub リポジトリにリンクされる Web アプリのデプロイ](app-service-web-arm-from-github-provision.md)
* [Web アプリと SQL Database をプロビジョニングする](app-service-web-arm-with-sql-database-provision.md)
* [Azure でマイクロサービスを予測どおりにデプロイする](app-service-deploy-complex-application-predictably.md)
* [Azure での実際のクラウド アプリケーションのビルド - すべてを自動化](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。電子書籍の中に掲載されているサンプル アプリケーションが、Windows PowerShell スクリプトをどのように使用して Azure テスト環境を作成し、その環境をデプロイするかを説明する、電子書籍の章。Azure PowerShell の他のドキュメントへのリンクを掲載している「[リソース](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources)」セクションを参照してください。
* [Windows PowerShell スクリプトを使用した開発環境およびテスト環境の発行](http://msdn.microsoft.com/library/dn642480.aspx)。Visual Studio で生成される Windows PowerShell デプロイ スクリプトの使用方法

###<a name="api"></a>.NET 管理 API を使用したデプロイの自動化

C# のコードを作成し、デプロイの目的で MSBuild や FTP の機能を実行することができます。この作業を実行する場合は、Azure の管理 REST API にアクセスして、サイト管理機能を実行することができます。

詳細については、次のリソースを参照してください。

* [Azure 管理ライブラリと .NET を使用してすべてを自動化](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)。.NET 管理 API の紹介とその他のドキュメントへのリンク。

###<a name="cli"></a>Azure コマンド ライン インターフェイス (Azure CLI) からのデプロイ

Windows、Mac、Linux マシンでコマンド ラインを使用し、FTP を使用してデプロイを実行することもできます。この作業を実行する場合は、Azure CLI を使用して Azure REST 管理 API にアクセスすることもできます。

詳細については、次のリソースを参照してください。

* [Azure コマンド ライン ツール](/downloads/#cmd-line-tools)。コマンド ライン ツールに関する情報を掲載している Azure.com のポータル ページです。

###<a name="webdeploy"></a>Web デプロイ コマンド ラインからのデプロイ

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) は IIS へのデプロイを行うマイクロソフトのソフトウェアであり、インテリジェントなファイルの同期機能に加えて、FTP を使用する場合は自動化できない他の多くのデプロイ関連タスクも実行または調整することができます。たとえば、Web Deploy を使用して Web アプリと共に新しいデータベースをデプロイ、またはデータベースの更新をデプロイすることができます。また、Web Deploy は、変更されたファイルのみをインテリジェントにコピーするため、既存のサイトを更新するのに要する時間を最小化することもできます。Microsoft WebMatrix、Visual Studio、Visual Studio Online、および Team Foundation Server は Web Deploy のビルトイン機能をサポートしていますが、コマンド ラインから Web Deploy を直接使用してデプロイを自動化することもできます。Web Deploy の各コマンドは非常に強力ですが、学習曲線を急上昇させることもできます。

詳細については、次のリソースを参照してください。

* [シンプルな Web アプリ: デプロイ](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/)。Web デプロイの使用を簡単にするために作成したツールに関する David Ebbo のブログ
* [Web デプロイ ツール](http://technet.microsoft.com/library/dd568996)。Microsoft TechNet サイトの公式ドキュメント。作成以来期間が経過していますが、現在も有効な出発点として使用できます。
* [Web Deploy の使用](http://www.iis.net/learn/publish/using-web-deploy)。Microsoft IIS.NET サイトの公式ドキュメント。同じく作成以来期間が経過していますが、現在も有効な出発点として使用できます。
* [StackOverflow](http://www.stackoverflow.com)。コマンドラインから Web Deploy を使用する方法に関する最新の情報を掲載した、出発点として最適な場所です。
* [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。MSBuild は Visual Studio によって使用されるビルド エンジンであり、Web アプリケーションを Web Apps にデプロイする目的でコマンド ラインから使用することもできます。このチュートリアルは、Visual Studio のデプロイに注目したシリーズの一部です。

##<a name="nextsteps"></a>次のステップ

シナリオによっては、Web アプリをステージングと運用バージョンの間で簡単に切り替えられる環境が必要です。詳細については、[Web アプリのステージングされたデプロイ](web-sites-staged-publishing.md)に関するページをご覧ください。

バックアップと復元計画を策定することは、デプロイ ワークフローの重要な部分です。Web Apps のバックアップと復元の機能については、[Web Apps のバックアップ](web-sites-backup.md)に関するページをご覧ください。

Azure のロール ベースの Access Control を使用して、Web Apps のデプロイへのアクセスを管理する方法については、[RBAC と Web Apps の公開](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing)に関するブログをご覧ください。

その他のデプロイのトピックについては、[Web アプリに関するドキュメント](/documentation/services/web-sites/)の「デプロイ」セクションをご覧ください。

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
 

<!---HONumber=AcomDC_0114_2016-->