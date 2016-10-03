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
	ms.date="09/06/2016"
	ms.author="cephalin;dariac"/>
    
# Azure App Service へのアプリのデプロイ

この記事は、Web アプリ、モバイル アプリ バックエンド、または API アプリのファイルを [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする際の最適なオプションを特定するのに役立ちます。また、選択したオプションに固有の手順が記載された適切なリソースも紹介しています。

## <a name="overview"></a>Azure App Service のデプロイの概要

Azure App Service では、アプリケーション フレームワーク (ASP.NET、PHP、Node.js など) を自動的に保持します。既定で有効化されるフレームワークもあれば、有効にするために簡単なチェックマークによる構成が必要なフレームワーク (Java や Python など) もあります。また、PHP のバージョンやランタイムのビット数など、アプリケーション フレームワークをカスタマイズすることもできます。詳細については、「[Azure App Service での Web アプリの構成](web-sites-configure.md)」をご覧ください。

Web サーバーやアプリケーション フレームワークを気にする必要がないため、アプリを App Service にデプロイするときは、コード、バイナリ、コンテンツ ファイル、それぞれのディレクトリ構造を、[Azure の **/site/wwwroot** ディレクトリ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (WebJobs の場合は **/site/wwwroot/App\_Data/Jobs/** ディレクトリ) にデプロイすることが重要になります。App Service では、次のデプロイ オプションをサポートしています。

- [FTP または FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): [FileZilla](https://filezilla-project.org) から [NetBeans](https://netbeans.org) などのフル機能を備えた IDE まで、好みの FTP または FTPS 対応ツールを使用して、ファイルを Azure に移動します。これは、厳密なファイル アップロード プロセスです。App Service からバージョン管理やファイル構造管理などの追加サービスは提供されません。

- [Kudu (Git/Mercurial または OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment): App Service の[デプロイ エンジン](https://github.com/projectkudu/kudu/wiki)を使用します。任意のリポジトリから Kudu にコードを直接プッシュします。Kudu では、コードをプッシュしたときに、継続的なデプロイと他の自動タスクのための追加サービス (バージョン管理、パッケージの復元、MSBuild、[Web フック](https://github.com/projectkudu/kudu/wiki/Web-hooks)など) も提供されます。Kudu デプロイ エンジンでは、次の 3 つの異なる種類のデプロイ ソースをサポートしています。
    * OneDrive および Dropbox からのコンテンツの同期
    * GitHub、Bitbucket、および Visual Studio Team Services からの自動同期によるリポジトリ ベースの継続的なデプロイ
    * ローカル Git からの手動での同期によるリポジトリ ベースのデプロイ

- [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): Visual Studio など、好きな Microsoft ツールから、IIS サーバーへのデプロイを自動化するのと同じツールを使用して、直接 App Service にコードをデプロイします。このツールは、差分のみのデプロイ、データベースの作成、接続文字列の変換などをサポートします。Web Deploy は、Azure にデプロイする前にアプリケーション バイナリがビルドされる点で Kudu とは異なります。FTP と同様に、App Service から追加サービスは提供されません。

一般的な Web 開発ツールでは、これらのデプロイメント プロセスを 1 つ以上サポートします。選択したツールによって利用できるデプロイメント プロセスが決まりますが、自由に使える実際の DevOps 機能は、デプロイメント プロセスと選択したツールの組み合わせによって異なります。たとえば、[Visual Studio と Azure SDK](#vspros) から Web Deploy を実行する場合、Kudu の自動化機能を利用できなくても、Visual Studio でパッケージの復元と MSBuild による自動化が提供されます。

>[AZURE.NOTE] これらのデプロイ プロセスでは、実際には、アプリで必要な [Azure リソースがプロビジョニング](../resource-group-template-deploy-portal.md)されるわけではありません。ただし、リンクされている手順に関する記事のほとんどは、アプリをプロビジョニングし、コードをエンド ツー エンドでデプロイする方法を示しています。また、「[コマンド ライン ツールを使用したデプロイの自動化](#automate)」では、Azure リソースをプロビジョニングするための追加オプションを紹介しています。
     
## <a name="ftp"></a>Azure へのファイルの手動コピーによる FTP 経由でのデプロイ
Web コンテンツを Web サーバーに手動でコピーすることに慣れている場合は、エクスプローラーや [FileZilla](https://filezilla-project.org/) などの [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) ユーティリティを使用してファイルをコピーできます。

ファイルの手動コピーの利点は次のとおりです。

- FTP ツールを使い慣れていて、複雑さが最小限で済みます。
- ファイルのコピー先が正確にわかります。
- FTPS を使用するとセキュリティが強化されます。

ファイルの手動コピーの欠点は次のとおりです。

- App Service の適切なディレクトリにファイルをデプロイする方法を把握する必要があります。
- エラーが発生したときにロールバックするためのバージョン管理が行われません。
- デプロイに関する問題のトラブルシューティングに利用できる組み込みのデプロイ履歴がありません。
- 多くの FTP ツールでは、差分のみのコピーは行われず、すべてのファイルがコピーされるため、デプロイ時間が長くなる可能性があります。

### <a name="howtoftp"></a>Azure にファイルを手動でコピーしてデプロイする方法
ファイルを Azure にコピーするには、次の簡単な手順を実行します。

1. デプロイ資格情報を構成したら、**[設定]**、**[プロパティ]** の順に移動し、**[FTP/デプロイ ユーザー]**、**[FTP ホスト名]**、**[FTPS ホスト名]** の値をコピーして、FTP 接続情報を取得します。**[FTP/デプロイ ユーザー]** のユーザー値は、FTP サーバーの適切なコンテキストを提供するために、Azure ポータルに表示されているとおり、アプリ名を含めてコピーしてください。
2. FTP クライアントから、収集した接続情報を使用してアプリに接続します。
3. ファイルとそれぞれのディレクトリ構造を、[Azure の **/site/wwwroot** ディレクトリ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (WebJobs の場合は **/site/wwwroot/App\_Data/Jobs/** ディレクトリ) にコピーします。
4. アプリの URL を参照して、アプリが正しく動作していることを確認します。

詳細については、次のリソースを参照してください。

* [PHP-MySQL Web アプリの作成と FTP でのデプロイ](web-sites-php-mysql-deploy-use-ftp.md)。

## <a name="dropbox"></a>クラウド フォルダーとの同期によるデプロイ
[ファイルの手動コピー](#ftp)に代わる優れた方法として、OneDrive や Dropbox などのクラウド ストレージ サービスから App Service にファイルとフォルダーを同期する方法があります。クラウド フォルダーとの同期では、デプロイに Kudu プロセスを利用します (「[デプロイメント プロセスの概要](#overview)」を参照してください)。

クラウド フォルダーとの同期の利点は次のとおりです。

- デプロイのシンプルさ。OneDrive や Dropbox などのサービスは、デスクトップ同期クライアントを提供するので、ローカルの作業ディレクトリもデプロイメント ディレクトリになります。
- ワンクリックでデプロイできます。
- Kudu デプロイ エンジンのすべての機能 (パッケージの復元、自動化など) を利用できます。

クラウド フォルダーとの同期の欠点は次のとおりです。

- エラーが発生したときにロールバックするためのバージョン管理が行われません。
- デプロイは自動化されず、手動で同期する必要があります。

### <a name="howtodropbox"></a>クラウド フォルダーと同期してデプロイする方法
[Azure ポータル](https://portal.azure.com)では、コンテンツを同期する OneDrive または Dropbox クラウド ストレージのフォルダーを指定し、そのフォルダー内でアプリのコードとコンテンツの作業を行い、ボタンをクリックするだけで App Service に同期できます。

* [クラウド フォルダーから Azure App Service へのコンテンツの同期](app-service-deploy-content-sync.md)。

## <a name="continuousdeployment"></a>クラウド ベースのソース管理サービスからの継続的なデプロイ
開発チームで [Visual Studio Team Services](http://www.visualstudio.com/)、[GitHub](https://www.github.com)、[BitBucket](https://bitbucket.org/) などのクラウド ベースのソース コード管理 (SCM) サービスを使用している場合、リポジトリと統合し、継続的にデプロイするように App Service を構成できます。

クラウド ベースのソース管理サービスからのデプロイの利点は次のとおりです。

- バージョン管理により、ロールバックが有効になります。
- Git (および該当する場合は、Mercurial) リポジトリの継続的なデプロイを構成できます。
- 分岐固有のデプロイでは、個々の[スロット](web-sites-staged-publishing.md)にそれぞれ異なる分岐をデプロイできます。
- Kudu デプロイ エンジンのすべての機能 (デプロイのバージョン管理、ロールバック、パッケージの復元、自動化など) を利用できます。

クラウド ベースのソース管理サービスからのデプロイの欠点は次のとおりです。

- 各 SCM サービスの知識が必要です。

###<a name="vsts"></a>クラウド ベースのソース管理サービスから継続的にデプロイする方法
[Azure ポータル](https://portal.azure.com)では、GitHub、Bitbucket、および Visual Studio Team Services からの継続的なデプロイを構成できます。

* [Azure App Service への継続的なデプロイ](app-service-continuous-deployment.md)。

## <a name="localgitdeployment"></a>ローカル Git からのデプロイ
開発チームで Git に基づくオンプレミスのローカル ソース コード管理 (SCM) サービスを使用している場合は、App Service へのデプロイ ソースとしてこれを構成できます。

ローカル Git からのデプロイの利点は次のとおりです。

- バージョン管理により、ロールバックが有効になります。
- 分岐固有のデプロイでは、個々の[スロット](web-sites-staged-publishing.md)にそれぞれ異なる分岐をデプロイできます。
- Kudu デプロイ エンジンのすべての機能 (デプロイのバージョン管理、ロールバック、パッケージの復元、自動化など) を利用できます。

ローカル Git からのデプロイの欠点は次のとおりです。

- 各 SCM システムの知識が必要です。
- 継続的なデプロイのためのターンキー ソリューションがありません。

###<a name="vsts"></a>ローカル Git からデプロイする方法
[Azure ポータル](https://portal.azure.com)で、ローカル Git デプロイを構成できます。

* [Azure App Service へのローカル Git デプロイ](app-service-deploy-local-git.md)。
* [任意の git/hg リポジトリから Web アプリへの発行](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。

## IDE を使用したデプロイ
[Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) と [Azure SDK](https://azure.microsoft.com/downloads/)、[Xcode](https://developer.apple.com/xcode/)、[Eclipse](https://www.eclipse.org)、[IntelliJ IDEA](https://www.jetbrains.com/idea/) などの IDE スイートを既に使用している場合は、IDE 内から直接 Azure にデプロイできます。このオプションは個人開発者に最適です。

Visual Studio は 3 つのデプロイ プロセス (FTP、Git、Web Deploy) をすべてサポートしていますが、FTP または Git と統合されていれば、他の IDE でも App Service にデプロイできます (「[デプロイメント プロセスの概要](#overview)」を参照してください)。

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
* [Visual Studio を使用した ASP.NET Web デプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)。12 部構成のチュートリアル シリーズであり、このリスト内に掲載されている他のリソースより詳細にデプロイ タスクの範囲を網羅しています。チュートリアルを作成した後で、不足している事項を説明するためにメモが追加されたため、一部の Azure デプロイ機能が追加されました。
* [Git リポジトリから ASP.NET Web サイトを Visual Studio 2012 内にある Azure へ直接のデプロイ](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。Visual Studio 内で ASP.NET Web プロジェクトをデプロイし、Git プラグインを使用してコードを Git にコミットして、Azure を Git リポジトリに接続する方法について説明します。Visual Studio 2013 以降、Git のサポートが組み込まれており、プラグインをインストールする必要はありません。

###<a name="aztk"></a>Azure Toolkit for Eclipse や Azure Toolkit for IntelliJ IDEA を使用してデプロイする方法

Azure には、[Azure Toolkit for Eclipse](../azure-toolkit-for-eclipse.md) や [Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij.md) を使用することで、Eclipse や IntelliJ から直接 Web Apps をデプロイすることができます。以下のチュートリアルでは、いずれかの IDE を使用して単純な "Hello" world Web App を Azure にデプロイする手順を説明しています。

*  [Eclipse で Azure 用の Hello World Web アプリを作成する](./app-service-web-eclipse-create-hello-world-web-app.md)このチュートリアルでは、Azure Toolkit for Eclipse を使用して、Azure 用の Hello World Web アプリを作成、デプロイする方法について説明します。
*  [IntelliJ で Azure 用の Hello World Web アプリを作成する](./app-service-web-intellij-create-hello-world-web-app.md)。このチュートリアルでは、Azure Toolkit for IntelliJ を使用して、Azure 用の Hello World Web アプリを作成、デプロイする方法について説明します。


## <a name="automate"></a>コマンド ライン ツールを使用したデプロイの自動化

* [MSBuild を使用したデプロイの自動化](#msbuild)
* [FTP ツールとスクリプトを使用してファイルをコピー](#ftp)
* [Windows PowerShell を使用したデプロイの自動化](#powershell)
* [.NET 管理 API を使用したデプロイの自動化](#api)
* [Azure コマンド ライン インターフェイス (Azure CLI) からデプロイ](#cli)
* [Web デプロイ コマンド ラインからデプロイ](#webdeploy)
* [FTP バッチ スクリプトの使用](http://support.microsoft.com/kb/96269)。
 
もう 1 つのデプロイ オプションとして、[Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy) などのクラウド ベースのサービスを使用することもできます。詳細については、「[Deploy ASP.NET applications to Azure Web Sites (ASP.NET Web アプリケーションを Azure の Web サイトにデプロイする)](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)」をご覧ください。

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
* [Windows PowerShell スクリプトを使用した開発環境およびテスト環境の発行](../vs-azure-tools-publishing-using-powershell-scripts.md)。Visual Studio で生成される Windows PowerShell デプロイ スクリプトの使用方法

###<a name="api"></a>.NET 管理 API を使用したデプロイの自動化

C# のコードを作成し、デプロイの目的で MSBuild や FTP の機能を実行することができます。この作業を実行する場合は、Azure の管理 REST API にアクセスして、サイト管理機能を実行することができます。

詳細については、次のリソースを参照してください。

* [Azure 管理ライブラリと .NET を使用してすべてを自動化](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)。.NET 管理 API の紹介とその他のドキュメントへのリンク。

###<a name="cli"></a>Azure コマンド ライン インターフェイス (Azure CLI) からのデプロイ

Windows、Mac、Linux マシンでコマンド ラインを使用し、FTP を使用してデプロイを実行することもできます。この作業を実行する場合は、Azure CLI を使用して Azure REST 管理 API にアクセスすることもできます。

詳細については、次のリソースを参照してください。

* [Azure コマンド ライン ツール](/downloads/#cmd-line-tools)。コマンド ライン ツールに関する情報を掲載している Azure.com のポータル ページです。

###<a name="webdeploy"></a>Web デプロイ コマンド ラインからのデプロイ

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) は IIS へのデプロイを行うマイクロソフトのソフトウェアであり、インテリジェントなファイルの同期機能に加えて、FTP を使用する場合は自動化できない他の多くのデプロイ関連タスクも実行または調整することができます。たとえば、Web Deploy を使用して Web アプリと共に新しいデータベースをデプロイ、またはデータベースの更新をデプロイすることができます。また、Web Deploy は、変更されたファイルのみをインテリジェントにコピーするため、既存のサイトを更新するのに要する時間を最小化することもできます。Microsoft Visual Studio および Team Foundation Server では Web Deploy のビルトイン機能をサポートしていますが、コマンド ラインから Web Deploy を直接使用してデプロイを自動化することもできます。Web Deploy の各コマンドは非常に強力ですが、学習曲線を急上昇させることもできます。

詳細については、次のリソースを参照してください。

* [シンプルな Web アプリ: デプロイ](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/)。Web デプロイの使用を簡単にするために作成したツールに関する David Ebbo のブログ
* [Web デプロイ ツール](http://technet.microsoft.com/library/dd568996)。Microsoft TechNet サイトの公式ドキュメント。作成以来期間が経過していますが、現在も有効な出発点として使用できます。
* [Web Deploy の使用](http://www.iis.net/learn/publish/using-web-deploy)。Microsoft IIS.NET サイトの公式ドキュメント。同じく作成以来期間が経過していますが、現在も有効な出発点として使用できます。
* [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。MSBuild は Visual Studio によって使用されるビルド エンジンであり、Web アプリケーションを Web Apps にデプロイする目的でコマンド ラインから使用することもできます。このチュートリアルは、Visual Studio のデプロイに注目したシリーズの一部です。

##<a name="nextsteps"></a>次のステップ

シナリオによっては、アプリをステージングと運用バージョンの間で簡単に切り替えることができる環境が必要です。詳細については、[Web アプリのステージングされたデプロイ](web-sites-staged-publishing.md)に関するページをご覧ください。

バックアップと復元計画を策定することは、デプロイ ワークフローの重要な部分です。App Service のバックアップと復元の機能については、[Web Apps のバックアップ](web-sites-backup.md)に関するページを参照してください。

Azure のロールベースのアクセス許可を使用して、App Service のデプロイへのアクセスを管理する方法については、[RBAC と Web Apps の公開](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/)に関するブログを参照してください。


 

<!---HONumber=AcomDC_0921_2016-->