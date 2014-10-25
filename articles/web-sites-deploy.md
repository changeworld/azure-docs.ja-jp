<properties linkid="manage-services-how-to-deploy-websites" pageTitle="How to deploy an Azure Website" metaKeywords="Azure deploy publish web site" description="Learn what methods are available for deploying content to an Azure Website." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Website" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/02/2014" ms.author="tdykstra"></tags>

# Azure Web サイトのデプロイ方法

独自のコンテンツを Azure Web サイトにデプロイするための多くのオプションがあります。このトピックでは、各オプションに関する簡単な概要を説明し、詳細情報へのリンクを掲載します。
-   [クラウドでホストされているソース管理システムからのデプロイ][クラウドでホストされているソース管理システムからのデプロイ]
    -   Visual Studio Online (VSO)
    -   Git を使用したリポジトリ Web サイト
    -   Mercurial を使用したリポジトリ Web サイト
    -   Dropbox
-   [IDE からのデプロイ][IDE からのデプロイ]
    -   Visual Studio
    -   WebMatrix
-   [FTP ユーティリティを使用したデプロイ][FTP ユーティリティを使用したデプロイ]
-   [内部設置型のソース管理システムからのデプロイ][内部設置型のソース管理システムからのデプロイ]
    -   Team Foundation Server (TFS)
    -   内部設置型の Git または Mercurial リポジトリ
-   [コマンド ライン ツールと Azure の REST 管理 API の使用][コマンド ライン ツールと Azure の REST 管理 API の使用]
    -   MSBuild
    -   FTP スクリプト
    -   Windows PowerShell
    -   .NET 管理 API
    -   クロスプラットフォーム コマンド ライン (xpat-cli)
    -   Web デプロイ コマンド ライン

## <a name="cloud"></a>クラウドでホストされているソース管理システムからのデプロイ

Web サイトをデプロイする最善の方法は、[継続的な配信ワークフロー][継続的な配信ワークフロー]を設定し、[ソース管理システム][ソース管理システム]に統合することです。自動化により、開発プロセスの効率が向上することに加え、バックアップと復元のプロセスの管理性と信頼性も向上します。

まだソース管理を設定していない場合に、作業を開始する最も簡単な方法は、クラウドでホストされているソース管理システムを使用することです。

### <a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online][Visual Studio Online] (旧 Team Foundation Service) は、ソース管理とチーム コラボレーションを対象にする、マイクロソフトのクラウドベースのソリューションです。開発者が 5 人以下のチームは、このサービスを無料で使用できます。Azure Web サイトへの継続的な配信を行うために VSO を設定することや、リポジトリで [Git と TFVC][Visual Studio Online] のいずれかを使用することができます。

詳細については、次のリソースを参照してください。

-   [VSO と TFVC を使用した Azure への継続的な配信][VSO と TFVC を使用した Azure への継続的な配信]。TFVC を使用して VSO から Azure Web サイトへの継続的な配信を設定する方法を示す短いステップ バイ ステップ チュートリアルです。分散型ソース管理オプションである Git とは対照的に、TFVC は VSO 内で使用する集中型ソース管理オプションです。
-   [VSO と TFVC を使用した Azure への継続的な配信][1]。上のチュートリアルに似ていますが、VSO アカウントへのサインアップ手順や、ソース管理へのプロジェクトのチェックイン方法についてもあわせて説明します。
-   [Continuous delivery to Azure using Visual Studio Online and Git (Visual Studio Online と Git を使用した Azure への継続的な配信)][Continuous delivery to Azure using Visual Studio Online and Git (Visual Studio Online と Git を使用した Azure への継続的な配信)].上のチュートリアルに似ていますが、TFVC の代わりに Git を使用します。

### <a name="git"></a>Git を使用したリポジトリ Web サイト

[Git][Visual Studio Online] は、人気の高い分散型ソース管理システムです。Azure には、Git リポジトリを格納する一般的な Web ベースのリポジトリ サイトから Azure Web サイトへの自動的なデプロイを容易にする [GitHub][GitHub]、[CodePlex][CodePlex]、[BitBucket][BitBucket] などの機能が組み込まれています。Git を使用したデプロイに伴う利点は、以前のデプロイが必要になったときに、比較的簡単に以前のデプロイにロールバックできることです。

詳細については、次のリソースを参照してください。

-   [ソース管理から Azure の Web サイトへの発行][ソース管理から Azure の Web サイトへの発行]。Git を使用してローカル コンピューターから Azure Web サイトに直接発行する方法 (Azure では、この発行方法をローカル Git と呼びます)。また、GitHub、CodePlex、BitBucket からの Git リポジトリの継続的なデプロイを有効にする方法も示します。
-   [Deploying to Web Sites with GitHub using Kudu (Kudu を使用した GitHub による Web サイトへのデプロイ)][Deploying to Web Sites with GitHub using Kudu (Kudu を使用した GitHub による Web サイトへのデプロイ)]。Scott Hanselman と David Ebbo によるビデオ。GitHub から Azure Web サイトへ直接 Web サイトをデプロイする方法を示します。
-   [Git、Mercurial、Dropbox に関する Azure フォーラム][Git、Mercurial、Dropbox に関する Azure フォーラム]。

### <a name="mercurial"></a>Mercurial を使用したリポジトリ Web サイト

ソース管理システムとして [Mercurial][Mercurial] を使用し、リポジトリを [CodePlex][CodePlex] または [BitBucket][BitBucket] に保存する場合は、Azure Web サイトの組み込み機能を使用してコンテンツを自動的にデプロイすることができます。

Mercurial を使用してデプロイを行う方法の詳細については、次のリソースを参照してください。

-   [ソース管理から Azure の Web サイトへの発行][ソース管理から Azure の Web サイトへの発行]。このチュートリアルでは、Git リポジトリを発行する方法を示していますが、CodePlex または BitBucket でホストされている Mercurial リポジトリに関するプロセスは類似しています。
-   [Git、Mercurial、Dropbox に関する Azure フォーラム][Git、Mercurial、Dropbox に関する Azure フォーラム]。

### <a name="dropbox"></a>Dropbox

[Dropbox][Dropbox] はソース管理システムではありませんが、ソース コードを DropBox に格納する場合は、DropBox アカウントからのデプロイを自動化することもできます。

-   [Dropbox を使用した Windows Azure へのデプロイ][Dropbox を使用した Windows Azure へのデプロイ]Azure の管理ポータルを使用して Dropbox のデプロイを設定する方法。
-   [Dropbox と Azure の Web サイト][Dropbox と Azure の Web サイト]。このビデオでは、Dropbox フォルダーを Azure Web サイトに接続する方法について説明し、Web サイトを迅速に設定して動作させる方法や、簡単なドラッグ アンド ドロップのデプロイを使用して Web サイトを保持する方法を示します。
-   [Git、Mercurial、Dropbox に関する Azure フォーラム][Git、Mercurial、Dropbox に関する Azure フォーラム]。

## <a name="ide"></a>IDE からのデプロイ

[Visual Studio][Visual Studio] と [WebMatrix][WebMatrix] は Microsoft の IDE (統合開発環境) であり、Web 開発に使用できます。どちらにも、Azure Web サイトへのデプロイを容易にする組み込み機能があります。どちらも [Web Deploy][Web Deploy] を使用して、データベースのデプロイや接続文字列の変更など、デプロイに関連する追加のタスクを自動化することができます。どちらも [FTP または FTPS][FTP または FTPS]) を使用してデプロイを実行することができます。

WebMatrix は迅速にインストールでき、習得が容易ですが、Visual Studio には Azure Web サイトを操作するためのさらに多くの機能が備わっています。Visual Studio IDE から Azure Web サイトを作成、停止、起動、削除することができ、リアルタイムで作成されたログを表示することや、リモートでデバッグすることなども可能です。Visual Studio には、[Visual Studio Online][2]、[Team Foundation Server][Team Foundation Server]、[Git リポジトリ][Git リポジトリ] のようなソース管理システムも統合されています。

### <a name="vs"></a>Visual Studio

Visual Studio から Azure Web サイトをデプロイする方法については、次のリソースを参照してください。

-   [Azure と ASP.NET を使ってみる][Azure と ASP.NET を使ってみる]Visual Studio と Web Deploy を使用して簡単な ASP.NET MVC Web プロジェクトを作成し、デプロイする方法。
-   [How to Deploy Azure WebJobs to Azure Websites (Azure Web ジョブを Azure Web サイトにデプロイする方法)][How to Deploy Azure WebJobs to Azure Websites (Azure Web ジョブを Azure Web サイトにデプロイする方法)]。コンソール アプリケーション プロジェクトを Web ジョブとしてデプロイするための構成方法。
-   [メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションを Windows Azure の Web サイトに展開する][メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションを Windows Azure の Web サイトに展開する]。Visual Studio、Web Deploy、Entity Framework、Code First Migrations を使用して、ASP.NET MVC Web プロジェクトを SQL データベースと共に配置する方法。
-   [Visual Studio と ASP.NET に関する Web デプロイの概要][Visual Studio と ASP.NET に関する Web デプロイの概要]Visual Studio を使用して Web デプロイを実行するための基本的な概要です。作成以来期間が経過していますが、現在も引き続き当てはまる情報が掲載されており、その中には Web アプリケーションと共にデータベースをデプロイするためのオプションの概要、実行する必要が生じる可能性のある付加的なデプロイ タスクの一覧、Visual Studio を独自に手動で構成する方法が含まれています。このトピックには、Azure Web サイトへのデプロイのみではなく、デプロイ全般に関する情報も掲載されています。
-   [Visual Studio を使用した ASP.NET Web デプロイ][Visual Studio を使用した ASP.NET Web デプロイ]。12 部構成のチュートリアル シリーズであり、このリスト内に掲載されている他のリソースより詳細にデプロイ タスクの範囲を網羅しています。
-   [Git リポジトリから ASP.NET Web サイトを Visual Studio 2012 内にある Azure へ直接のデプロイ][Git リポジトリから ASP.NET Web サイトを Visual Studio 2012 内にある Azure へ直接のデプロイ]。Visual Studio 内で ASP.NET Web プロジェクトをデプロイし、Git プラグインを使用してコードを Git にコミットして、Azure を Git リポジトリに接続する方法について説明します。

### <a name="webmatrix"></a>WebMatrix

WebMatrix から Azure Web サイトをデプロイする方法については、次のリソースを参照してください。

-   [Microsoft WebMatrix を使用して Web サイトを開発およびデプロイする][Microsoft WebMatrix を使用して Web サイトを開発およびデプロイする]WebMatrix テンプレートを使用して簡単な ASP.NET Web サイトを作成し、WebMatrix と Web Deploy を使用してその Web サイトを Azure Web サイトにデプロイする方法。
-   [WebMatrix を使用した Node.js Web サイトの構築と Azure へのデプロイ][WebMatrix を使用した Node.js Web サイトの構築と Azure へのデプロイ]
-   [WebMatrix を使用して PHP-MySQL Azure の Web サイトを作成およびデプロイする][WebMatrix を使用して PHP-MySQL Azure の Web サイトを作成およびデプロイする]
-   [WebMatrix 3: Git の統合と Azure へのデプロイ][WebMatrix 3: Git の統合と Azure へのデプロイ].WebMatrix を使用して、Git ソース管理リポジトリからデプロイを実行する方法です。

## <a name="ftp"></a>FTP ユーティリティを使用したデプロイ

どの IDE を使用しているかにかかわりなく、[FTP][FTP または FTPS] を使用してファイルをコピーする方法で、コンテンツをサイトにデプロイすることができます。Azure Web サイト用の FTP 資格情報は簡単に作成でき、Internet Explorer のようなブラウザーや、[FileZilla][FileZilla] のような多彩な機能を備えた無料のユーティリティを含め、FTP と組み合わせて動作する任意のアプリケーションでその資格情報を使用することができます。Azure Web サイトでは、より安全な FTPS プロトコルもサポートしています。

FTP ユーティリティを使用して Web サイトのファイルを Azure にコピーすることは簡単ですが、データベースのデプロイや接続文字列の変更のような関連するデプロイ タスクの処理や調整が自動的に FTP ユーティリティによって実行されることはありません。また、多くの FTP ツールは、変更されていないファイルのコピーをスキップする目的でコピー元とコピー先のファイルを比較することもありません。大規模なサイトで常にすべてのファイルをコピーする場合は、小規模な更新であっても常にすべてのファイルがコピーされるため、デプロイに長時間を要する可能性があります。

詳細については、次のリソースを参照してください。

-   [PHP-MySQL Azure の Web サイトを作成して FTP でデプロイする][PHP-MySQL Azure の Web サイトを作成して FTP でデプロイする]。
-   [Web サイトの管理方法][Web サイトの管理方法]。FTP の資格情報を設定する方法に関して、PHP のチュートリアルで網羅されていない追加の情報が含まれます。

## <a name="onpremises"></a>内部設置型のソース管理システムからのデプロイ

TFS、Git、または Mercurial を (クラウド ホステットではなく) 内部設置型リポジトリで使用している場合は、リポジトリから直接 Azure Web サイトにデプロイすることができます。

### <a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server は、ソース管理とチーム コラボレーションを対象にする、マイクロソフトの内部設置型ソリューションです。Azure Web サイトへの継続的な配信を実行するように TFS を設定することができます。

詳細については、次のリソースを参照してください。

-   [Windows Azure のクラウド サービスの継続的な配信][Windows Azure のクラウド サービスの継続的な配信]このドキュメントは Azure クラウド サービスを対象にしていますが、そのコンテンツの一部は Web サイトにも当てはまります。

### <a name="gitmercurial"></a>内部設置型の Git または Mercurial リポジトリ

Git または Mercurial を使用する任意のリポジトリからデプロイを実行するために、Azure でそのリポジトリの URL を入力することができます。ローカルの Git リポジトリから Azure Web サイトへの直接のプッシュを実行することもできます。

詳細については、次のリソースを参照してください。

-   [ソース管理から Azure の Web サイトへの発行][ソース管理から Azure の Web サイトへの発行]。Git を使用してローカル コンピューターから Azure Web サイトに直接発行する方法 (Azure では、この発行方法をローカル Git と呼びます)。また、GitHub、CodePlex、BitBucket からの Git リポジトリの継続的なデプロイを有効にする方法も示します。
-   [任意の git/hg リポジトリから Azure の Web サイトへの発行][任意の git/hg リポジトリから Azure の Web サイトへの発行]。David Ebbo 氏によって作成されたブログであり、Azure Web サイトで "外部リポジトリ" 機能を使用する方法について説明しています。
-   [Git、Mercurial、Dropbox に関する Azure フォーラム][Git、Mercurial、Dropbox に関する Azure フォーラム]。
-   [1 つの Git リポジトリから Azure への 2 つの Web サイトのデプロイ][1 つの Git リポジトリから Azure への 2 つの Web サイトのデプロイ]。Scott Hanselman 氏によるブログの投稿です。

## <a name="commandline"></a> コマンド ライン ツールと Azure の REST 管理 API の使用

いつでも開発ワークフローを自動化できるのが最善ですが、ソース管理システムからそのような自動化を直接実行できない場合は、コマンド ライン ツールを使用して手動で設定することができます。多くの場合、デプロイにはサイト管理機能とコンテンツのコピーが関係するため、一般的にこの作業には複数のツールやフレームワークを使用することが関係します。

Azure には、REST 管理 API と、その API を使用した作業をさらに簡単にする複数のフレームワークが備わっているため、Azure を使用しない場合に比べると、デプロイに関連して必要とされるサイト管理タスクが簡略化されます。

### <a name="msbuild"></a>MSBuild

[Visual Studio IDE][Visual Studio IDE] を使用して開発を行う場合は、[MSBuild][MSBuild] を使用して IDE 内のすべての作業を自動化することができます。[Web Deploy][3] または [FTP と FTPS][FTP ユーティリティを使用したデプロイ] を使用してファイルをコピーするように MSBuild を構成することができます。Web Deploy では、データベースのデプロイなど、デプロイに関連する多くのタスクを自動化することもできます。

MSBuild を使用したコマンドライン デプロイの詳細については、次のリソースを参照してください。

-   [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ][Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ]。Visual Studio を使用して Azure のデプロイを行うチュートリアル シリーズの第 10 部。Visual Studio でプロファイルの発行を設定した後に、コマンド ラインを使用してデプロイを行う方法を示します。
-   『[Inside the Microsoft Build Engine:Using MSBuild and Team Foundation Build][MSBuild]』MSBuild を使用してデプロイを実行する方法に関する章が掲載されている書籍。

### <a name="ftp2"></a>FTP スクリプト

Azure Web サイト用に [FTP と FTPS][FTP または FTPS] の資格情報を簡単に作成し、その後、FTP バッチ スクリプトでその資格情報を使用することができます。

詳細については、次のリソースを参照してください。

-   [FTP バッチ スクリプトの使用][FTP バッチ スクリプトの使用]。

### <a name="powershell"></a>Windows PowerShell

[Windows PowerShell][Windows PowerShell] から MSBuild または FTP デプロイの機能を実行することができます。この機能を実行する場合は、Azure の REST 管理 API を簡単に呼び出せる一連の Windows PowerShell コマンドレットを使用することもできます。

詳細については、次のリソースを参照してください。

-   [Azure での実際のクラウド アプリケーションのビルド - すべてを自動化][Azure での実際のクラウド アプリケーションのビルド - すべてを自動化]。電子書籍の中に掲載されているサンプル アプリケーションが、Windows PowerShell スクリプトをどのように使用して Azure テスト環境を作成し、その環境をデプロイするかを説明する、電子書籍の章。Azure PowerShell の他のドキュメントへのリンクを掲載している「[リソース][リソース]」セクションを参照してください。

### <a name="api"></a>.NET 管理 API

C# のコードを作成し、デプロイの目的で MSBuild や FTP の機能を実行することができます。この作業を実行する場合は、Azure の管理 REST API にアクセスして、サイト管理機能を実行することができます。

詳細については、次のリソースを参照してください。

-   [Azure 管理ライブラリと .NET を使用してすべてを自動化][Azure 管理ライブラリと .NET を使用してすべてを自動化]。.NET 管理 API の紹介とその他のドキュメントへのリンク。

### <a name="cli"></a>クロスプラットフォーム コマンド ライン (xpat-cli)

Mac または Linux マシンでコマンド ラインを使用し、FTP を使用してデプロイを実行することもできます。この作業を実行する場合は、Azure のクロスプラットフォーム コマンド ライン インターフェイス (xpat cli) を使用して Azure REST 管理 API にアクセスすることもできます。Windows マシンでも xpat-cli を使用できます。

詳細については、次のリソースを参照してください。

-   [コマンド ライン ツール][コマンド ライン ツール]。コマンド ライン ツールに関する情報を掲載している WindowsAzure.com のポータル ページです。

### <a name="webdeploy"></a>Web デプロイ コマンド ライン

[Web Deploy][Web Deploy] は IIS へのデプロイを行うマイクロソフトのソフトウェアであり、インテリジェントなファイルの同期機能に加えて、FTP を使用する場合は自動化できない他の多くのデプロイ関連タスクも実行または調整することができます。たとえば、Web Deploy を使用して Web サイトと共に新しいデータベースをデプロイ、またはデータベースの更新をデプロイすることができます。また、Web Deploy は、変更されたファイルのみをインテリジェントにコピーするため、既存のサイトを更新するのに要する時間を最小化することもできます。Microsoft WebMatrix、Visual Studio、Visual Studio Online、および Team Foundation Server は Web Deploy のビルトイン機能をサポートしていますが、コマンド ラインから Web Deploy を直接使用してデプロイを自動化することもできます。Web Deploy の各コマンドは非常に強力ですが、学習曲線を急上昇させることもできます。

詳細については、次のリソースを参照してください。

-   [Web デプロイ ツール][Web デプロイ ツール]。Microsoft TechNet サイトの公式ドキュメント。作成以来期間が経過していますが、現在も有効な出発点として使用できます。
-   [Web Deploy の使用][Web Deploy の使用]。Microsoft IIS.NET サイトの公式ドキュメント。同じく作成以来期間が経過していますが、現在も有効な出発点として使用できます。
-   [StackOverflow][StackOverflow]。コマンドラインから Web Deploy を使用する方法に関する最新の情報を掲載した、出発点として最適な場所です。
-   [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ][Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ]。MSBuild は Visual Studio によって使用されるビルド エンジンであり、Web アプリケーションを Azure Web サイトにデプロイする目的でコマンド ラインから使用することもできます。このチュートリアルは、Visual Studio のデプロイに注目したシリーズの一部です。

## <a name="nextsteps"></a> 次のステップ

シナリオによっては、Web サイトをステージングと運用バージョンの間で簡単に切り替えられる環境が必要です。これは Azure Web サイトのステージされたデプロイ機能を使用して実現できます。詳細については、[Microsoft Azure の Web サイトのステージングされた展開][Microsoft Azure の Web サイトのステージングされた展開] を参照してください。

バックアップと復元計画を策定することは、デプロイ ワークフローの重要な部分です。Azure Web サイトのバックアップと復元の機能については、「[Azure の Web サイトのバックアップ][Azure の Web サイトのバックアップ]」を参照してください。

その他のデプロイのトピックについては、[Azure の Web サイトに関するドキュメント][Azure の Web サイトに関するドキュメント]の「デプロイ」セクションを参照してください。

  [クラウドでホストされているソース管理システムからのデプロイ]: #cloud
  [IDE からのデプロイ]: #ide
  [FTP ユーティリティを使用したデプロイ]: #ftp
  [内部設置型のソース管理システムからのデプロイ]: #onpremises
  [コマンド ライン ツールと Azure の REST 管理 API の使用]: #commandline
  [継続的な配信ワークフロー]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [ソース管理システム]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [Visual Studio Online]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs
  [VSO と TFVC を使用した Azure への継続的な配信]: http://www.visualstudio.com/ja-jp/learn/continuous-delivery-in-vs
  [1]: /ja-jp/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [Continuous delivery to Azure using Visual Studio Online and Git (Visual Studio Online と Git を使用した Azure への継続的な配信)]: http://azure.microsoft.com/ja-jp/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
  [GitHub]: http://www.github.com
  [CodePlex]: http://www.codeplex.com/
  [BitBucket]: https://bitbucket.org/
  [ソース管理から Azure の Web サイトへの発行]: /ja-jp/documentation/articles/web-sites-publish-source-control/
  [Deploying to Web Sites with GitHub using Kudu (Kudu を使用した GitHub による Web サイトへのデプロイ)]: /ja-jp/documentation/videos/deploying-to-azure-from-github/
  [Git、Mercurial、Dropbox に関する Azure フォーラム]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit
  [Mercurial]: http://mercurial.selenic.com/
  [Dropbox]: https://www.dropbox.com/
  [Dropbox を使用した Windows Azure へのデプロイ]: http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx
  [Dropbox と Azure の Web サイト]: http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites
  [Visual Studio]: http://www.visualstudio.com/ja-jp/downloads/download-visual-studio-vs.aspx
  [WebMatrix]: http://www.microsoft.com/web/webmatrix/
  [Web Deploy]: http://www.iis.net/downloads/microsoft/web-deploy
  [FTP または FTPS]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [2]: #vso
  [Team Foundation Server]: #tfs
  [Git リポジトリ]: #git
  [Azure と ASP.NET を使ってみる]: /ja-jp/develop/net/tutorials/get-started/
  [How to Deploy Azure WebJobs to Azure Websites (Azure Web ジョブを Azure Web サイトにデプロイする方法)]: /ja-jp/documentation/articles/websites-dotnet-deploy-webjobs/
  [メンバーシップ、OAuth、SQL データベースを使用した安全な ASP.NET MVC 5 アプリケーションを Windows Azure の Web サイトに展開する]: /ja-jp/develop/net/tutorials/web-site-with-sql-database/
  [Visual Studio と ASP.NET に関する Web デプロイの概要]: http://msdn.microsoft.com/ja-jp/library/dd394698.aspx
  [Visual Studio を使用した ASP.NET Web デプロイ]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction
  [Git リポジトリから ASP.NET Web サイトを Visual Studio 2012 内にある Azure へ直接のデプロイ]: http://www.dotnetcurry.com/ShowArticle.aspx?ID=881
  [Microsoft WebMatrix を使用して Web サイトを開発およびデプロイする]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-dotnet-using-webmatrix/
  [WebMatrix を使用した Node.js Web サイトの構築と Azure へのデプロイ]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-nodejs-use-webmatrix/
  [WebMatrix を使用して PHP-MySQL Azure の Web サイトを作成およびデプロイする]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-php-mysql-use-webmatrix/
  [WebMatrix 3: Git の統合と Azure へのデプロイ]: http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD
  [FileZilla]: https://filezilla-project.org/
  [PHP-MySQL Azure の Web サイトを作成して FTP でデプロイする]: /ja-jp/documentation/articles/web-sites-php-mysql-deploy-use-ftp/
  [Web サイトの管理方法]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-manage#ftp-credentials
  [Windows Azure のクラウド サービスの継続的な配信]: /ja-jp/develop/net/common-tasks/continuous-delivery/
  [任意の git/hg リポジトリから Azure の Web サイトへの発行]: http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html
  [1 つの Git リポジトリから Azure への 2 つの Web サイトのデプロイ]: http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx
  [Visual Studio IDE]: #vs
  [MSBuild]: http://msbuildbook.com/
  [3]: #webdeploy
  [Visual Studio を使用した ASP.NET Web のデプロイ: コマンド ラインのデプロイ]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment
  [FTP バッチ スクリプトの使用]: http://support.microsoft.com/kb/96269
  [Windows PowerShell]: http://msdn.microsoft.com/ja-jp/library/dd835506.aspx
  [Azure での実際のクラウド アプリケーションのビルド - すべてを自動化]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [リソース]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources
  [Azure 管理ライブラリと .NET を使用してすべてを自動化]: http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx
  [コマンド ライン ツール]: /ja-jp/downloads/#cmd-line-tools
  [Web デプロイ ツール]: http://technet.microsoft.com/ja-jp/library/dd568996
  [Web Deploy の使用]: http://www.iis.net/learn/publish/using-web-deploy
  [StackOverflow]: http://www.stackoverflow.com
  [Microsoft Azure の Web サイトのステージングされた展開]: /ja-jp/documentation/articles/web-sites-staged-publishing/
  [Azure の Web サイトのバックアップ]: /ja-jp/documentation/articles/web-sites-backup/
  [Azure の Web サイトに関するドキュメント]: /ja-jp/documentation/services/web-sites/
