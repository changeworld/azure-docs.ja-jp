<properties 
	pageTitle="Azure .NET SDK とは" 
	description="Azure .NET SDK に含まれるものを紹介します。" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="tdykstra"/>

# Azure SDK for .NET とは

Azure SDK for .NET は、Visual Studio のツール、コマンドライン ツール、ランタイム バイナリ、およびクライアント ライブラリを 1 つにまとめたもので、Azure で実行するアプリケーションの開発、テスト、デプロイに役立ちます。この記事では、SDK のインストールにより、どのようなことができるかについて説明します。SDK は [Azure のダウンロード ページ](/ja-jp/downloads/)でダウンロードできます。 

Azure SDK for .NET には、Azure のサービスを利用するために必要なクライアント ライブラリも含まれています。これらのライブラリは [NuGet](http://go.microsoft.com/fwlink/?LinkId=510472) で個々にインストールできます。

## 目次

- [Azure SDK for .NET のインストールに含まれるもの](#included)
- [Azure SDK for .NET のインストールに含まれないもの](#notincluded)
- [FAQ](#faq)
- [リソース](#resources)

##<a id="included"></a>Azure SDK for .NET のインストールに含まれるもの

Azure SDK for .NET には次の製品が含まれており、インストールされます。

- [Visual Studio Express for Web](#vwd)
- [Microsoft ASP.NET and Web Tools for Visual Studio](#wte)
- [Microsoft Azure Tools for Microsoft Visual Studio](#tools)
- [Microsoft Azure Authoring Tools](#auth)
- [Microsoft Azure エミュレーター](#emulator)
- [Microsoft Azure ストレージ エミュレーター](#stgemulator)
- [Microsoft Azure Storage Tools](#stgtools)
- [Microsoft Azure Libraries for .NET](#libraries)
- [LightSwitch Azure Publishing add-on for Visual Studio](#ls)

###<a id="vwd"></a>Visual Studio Express for Web

コンピューターに Visual Studio がインストールされていない場合、SDK によって [Visual Studio Express for Web](http://www.visualstudio.com/ja-jp/products/visual-studio-express-vs.aspx) がインストールされます。 
 
###<a id="wte"></a>Microsoft ASP.NET and Web Tools for Visual Studio

この製品をインストールすると、Azure の Web サイトで次のことができるようになります。

* [Azure の Web サイトに Web プロジェクトを発行する](../web-sites-dotnet-get-started/)。
* [Azure の Web ジョブにコンソール アプリケーション プロジェクトを発行する](../websites-dotnet-deploy-webjobs/)。
* [Azure の Web サイトおよび SQL データベース リソースを作成して、新しい Web プロジェクトを作成または発行する](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/)。
* [PowerShell デプロイメント スクリプトを作成して、新規の Web サイトを作成する](http://msdn.microsoft.com/library/dn642480.aspx)。
* [サーバー エクスプローラーで Azure Web サイトの管理とトラブルシューティングを行う](../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement)。
* [Web サイトおよび Web ジョブに対してリモート デバッグを実行する](../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug)。 

>[WACOM.NOTE] これらの機能は Visual Studio の更新版にも含まれているため、Azure SDK for .NET をインストールしなくても利用できます。 

###<a id="tools"></a>Microsoft Azure Tools for Microsoft Visual Studio

この製品を利用すると、Azure のクラウド サービスおよび仮想マシンで次のことができるようになります。

* [クラウド サービス プロジェクトを作成、公開、発行する](../cloud-services-dotnet-get-started/)。
* [クラウド サービス プロジェクトのデプロイメント パッケージを作成する](http://msdn.microsoft.com/library/ff683672.aspx)。
* [Azure の仮想マシンを作成して、新規の Web プロジェクトを作成する](../virtual-machines-dotnet-create-visual-studio-powershell/)。
* [PowerShell スクリプトを作成して、新規の仮想マシンを作成する](http://msdn.microsoft.com/library/dn642480.aspx)。
* [Visual Studio のプロジェクト プロパティ ウィンドウでクラウド サービス プロジェクトの設定を表示し、管理する](http://msdn.microsoft.com/library/ee405486.aspx)。
* サーバー エクスプローラーで[クラウド サービス](http://msdn.microsoft.com/library/ff683675.aspx)、[仮想マシン](http://msdn.microsoft.com/library/jj131259.aspx)、および[サービス バス](http://msdn.microsoft.com/library/jj149828.aspx)を表示し、管理する。 
* [クラウド サービスおよび仮想マシンに対してリモート デバッグを実行する](http://msdn.microsoft.com/library/ff683670.aspx)。

###<a id="auth"></a>Microsoft Azure Authoring Tools

この製品には次のツールが含まれています。

* [CSPack コマンド ライン ツール](http://msdn.microsoft.com/library/gg432988.aspx)。デプロイメント パッケージを作成する場合に使用します。
* [CSEncrypt コマンド ライン ツール](http://msdn.microsoft.com/library/hh404001.aspx)。リモート デスクトップ接続を介してクラウド サービスのロール インスタンスにアクセスする際のパスワードを暗号化するために使用します。
* クラウド サービス プロジェクトがランタイム環境と通信し、診断する場合に必要な各種のランタイム バイナリ。これらのバイナリは、NuGet パッケージでは利用できません。

###<a id="emulator"></a>Microsoft Azure エミュレーター

[Azure エミュレーター](http://msdn.microsoft.com/library/dn339018.aspx)は、クラウド サービス環境をシミュレートします。これにより、クラウド サービス プロジェクトを Azure にデプロイする前に、ローカルのコンピューターでテストできます。

###<a id="stgemulator"></a>Microsoft Azure ストレージ エミュレーター

[Azure ストレージ エミュレーター](http://msdn.microsoft.com/library/hh403989.aspx)は、SQL Server のインスタンスとローカル ファイル システムを使用して Azure のストレージ (キュー、テーブル、BLOB) をシミュレートします。これにより、ローカルのコンピューターでテストできます。 

###<a id="stgtools"></a>Microsoft Azure Storage Tools

この製品は、コマンド ライン ツールの [AzCopy](http://aka.ms/AzCopy) をインストールします。このツールを使用すると、Azure のストレージ アカウントにデータを転送したり、このアカウントからデータを転送したりすることができます。

###<a id="libraries"></a>Microsoft Azure Libraries for .NET

この製品には次のツールが含まれています。

* Azure のストレージ、サービス バス、キャッシュ用の NuGet パッケージ。このパッケージは、ユーザーのコンピューターに格納されるため、Visual Studio による新規のクラウド サービス プロジェクトの作成がオフラインでできるようになります。
* Visual Studio プラグイン。Visual Studio で [In-Role Cache](http://msdn.microsoft.com/library/dn386103.aspx) プロジェクトの実行がローカルでできるようになります。 

###<a id="ls"></a>LightSwitch Azure Publishing add-on for Visual Studio

この製品を使用すると、[LightSwitch プロジェクトを Azure の Web サイトに発行](http://msdn.microsoft.com/library/jj131261.aspx)できます。LightSwitch アドオンは、Visual Studio のアップグレードの他、Azure SDK for .NET にも含まれています。この SDK をインストールすると、アドオンの最新バージョンを使用できます。 

##<a id="notincluded"></a>Azure SDK for .NET のインストールに含まれないもの

SDK をインストールしたときに、Azure 向けアプリケーションの開発に必要なものが含まれない場合もあります。主なものは次のとおりです。

* [クライアント ライブラリ](http://go.microsoft.com/fwlink/?LinkId=510472)。 

	Azure SDK には、Azure のサービスを使用するために必要なクライアント ライブラリが含まれています。ただし、SDK をインストールしたときに、そのすべてがインストールされるわけではありません。アプリケーションに必要なクライアント ライブラリが SDK によってインストールされない場合には、[NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472) で必要なライブラリを入手できます。クライアント ライブラリが SDK によってインストールされる場合でも、NuGet.org で現在のバージョンに更新することをお勧めいたします。

  	**クライアント ライブラリのローカル コピー。**Azure SDK for .NET は、いくつかの Azure クライアント ライブラリ (ストレージ、サービス バス、キャッシュなど) 用の NuGet パッケージをユーザーのコンピューターにコピーします。これらのクライアント ライブラリは、新規のクラウド サービス プロジェクトに自動的に含められるため、インターネットに接続していない場合でも、Visual Studio はローカルの NuGet パッケージでプロジェクトを作成できます。一般に、クライアント ライブラリは SDK よりも頻繁に更新されているため、NuGet.org で入手できるクライアント ライブラリの方が SDK によってインストールされるものよりも新しい場合が多いことに注意してください。 

	**クライアント ライブラリが含まれるプロジェクト テンプレート。**[Azure クラウド サービス](../cloud-services-dotnet-get-started/)および [Azure モバイル サービス](../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/)のプロジェクト テンプレートには、いくつかのクライアント ライブラリが自動的に含まれます。これらのテンプレートに他のライブラリを含める場合や、他のテンプレートを使用する場合は、必要な[クライアント ライブラリの NuGet パッケージ](http://go.microsoft.com/fwlink/?LinkId=510472)をインストールしてください。

* [Azure PowerShell](../install-configure-powershell/)。 

	Azure PowerShell を使用すると、[Azure 環境の作成とデプロイを自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)できます。

* [Azure モバイル サービスのプロジェクト テンプレート](../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/)。

	モバイル サービスのテンプレートは、Visual Studio 2013 Update 2 以降でのみ利用できます。Azure SDK for .NET をインストールしても、Visual Studio 2012 以前および Visual Studio 2013 Update 1 以前のバージョンでは利用できません。

##<a id="faq"></a>よく寄せられる質問

- [Visual Studio に Azure の機能の多くが含まれているのにもかかわらず、Azure SDK for .NET をインストールする必要がありますか。](#azinvs)
- [クライアント ライブラリを入手したいのですが、Azure SDK for .NET をインストールしないと入手できませんか。](#clientlib)
- [以前のバージョンの Azure SDK for .NET はどこにありますか。](#olderversions)
- [Azure SDK for .NET のバージョンのライフサイクル ポリシーを教えてください。](#lifecycle)
- [Azure SDK for .NET との互換性があるゲスト OS を教えてください。](#guestos)

###<a id="azinvs"></a>Visual Studio に Azure の機能の多くが含まれているのにもかかわらず、Azure SDK for .NET をインストールする必要がありますか。

最新のツールを使用して Azure 向けの開発をする場合は、SDK をインストールすることをお勧めします。できれば SDK をインストールしたくない場合、次の条件を満たしていれば、インストールする必要はありません。

* 既に最新の [Visual Studio Update](http://www.visualstudio.com/ja-jp/downloads/download-visual-studio-vs#DownloadFamilies_5) をインストールしている。
* 開発の対象が Azure Websites または Mobile Services 向けに限られており、Cloud Services または Virtual Machines 向けの開発ではない。
* アプリケーションがストレージを使用しない、または使用するとしてもストレージ エミュレーターも AzCopy ツールも必要ではない。

###<a id="clientlib"></a>クライアント ライブラリを入手したいのですが、Azure SDK for .NET をインストールしないと入手できませんか。

SDK でクライアント ライブラリがインストールされるのは、インターネットに接続していなくてもクラウド サービス プロジェクトを作成できるようにする場合だけです。現行バージョンのクライアント ライブラリは、[NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472) で入手できる NuGet パッケージに用意されています。詳細については、この記事の初めの方に記載されている [Azure SDK for .NET のインストールに含まれないもの](#notincluded)を参照してください。

###<a id="olderversions"></a>以前のバージョンの Azure SDK for .NET はどこにありますか。

以前のバージョンは、ダウンロード ページの「[Azure SDK for .NET](/ja-jp/downloads/archive-net-downloads/)」で確認してください。 

###<a id="lifecycle"></a>Azure SDK for .NET のバージョンのライフサイクル ポリシーを教えてください。

「[Microsoft Azure サポート ライフサイクル ポリシー](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq)」を参照してください。

###<a id="guestos"></a>Azure SDK for .NET との互換性があるゲスト OS を教えてください。

「[Azure ゲスト OS リリースと SDK の互換性対応表](http://msdn.microsoft.com/library/ee924680.aspx)」を参照してください。



##<a id="resources"></a>リソース

Azure SDK for .NET またはクライアント ライブラリをダウンロードするには、[Azure のダウンロード ページ](/ja-jp/downloads/)を参照してください。

Azure SDK for .NET (クライアント ライブラリを含む) のソース コードについては、[GitHub.com/Azure の Web サイト](https://github.com/azure/)を参照してください。

Azure のクライアント ライブラリのリファレンス ドキュメントについては、[Azure .NET のリファレンス ページ](/ja-jp/develop/net/reference/)を参照してください。 

<!--HONumber=46--> 
