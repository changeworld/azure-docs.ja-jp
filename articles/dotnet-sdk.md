<properties pageTitle="What is the Azure .NET SDK" metaKeywords="azure .net sdk" description="Learn what is included in the Azure .NET SDK." documentationCenter=".NET" title="What is the Azure .NET SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra" />

# Azure SDK for .NET とは

Azure SDK for .NET は、Visual Studio のツール、コマンドライン ツール、ランタイム バイナリ、およびクライアント ライブラリを 1 つにまとめたもので、Azure で実行するアプリケーションの開発、テスト、デプロイに役立ちます。この記事では、SDK のインストールにより、どのようなことができるかについて説明します。SDK は [Azure のダウンロード ページ][Azure のダウンロード ページ]でダウンロードできます。

Azure SDK for .NET には、Azure のサービスを利用するために必要なクライアント ライブラリも含まれています。これらのライブラリは [NuGet][NuGet] で個々にインストールできます。

## 目次

-   [Azure SDK for .NET のインストールに含まれるもの][Azure SDK for .NET のインストールに含まれるもの]
-   [Azure SDK for .NET のインストールに含まれないもの][Azure SDK for .NET のインストールに含まれないもの]
-   [FAQ][FAQ]
-   [リソース][リソース]

## <span id="included"></span></a>Azure SDK for .NET のインストールに含まれるもの

Azure SDK for .NET には次の製品が含まれており、インストールされます。

-   [Visual Studio Express for Web][Visual Studio Express for Web]
-   [Microsoft ASP.NET and Web Tools for Visual Studio][Microsoft ASP.NET and Web Tools for Visual Studio]
-   [Microsoft Azure Tools for Microsoft Visual Studio][Microsoft Azure Tools for Microsoft Visual Studio]
-   [Microsoft Azure Authoring Tools][Microsoft Azure Authoring Tools]
-   [Microsoft Azure エミュレーター][Microsoft Azure エミュレーター]
-   [Microsoft Azure ストレージ エミュレーター][Microsoft Azure ストレージ エミュレーター]
-   [Microsoft Azure Storage Tools][Microsoft Azure Storage Tools]
-   [Microsoft Azure Libraries for .NET][Microsoft Azure Libraries for .NET]
-   [LightSwitch Azure Publishing add-on for Visual Studio][LightSwitch Azure Publishing add-on for Visual Studio]

### <span id="vwd"></span></a>Visual Studio Express for Web

コンピューターに Visual Studio がインストールされていない場合、SDK によって [Visual Studio Express for Web][1] がインストールされます。

### <span id="wte"></span></a>Microsoft ASP.NET and Web Tools for Visual Studio

この製品をインストールすると、Azure の Web サイトで次のことができるようになります。

-   [Azure の Web サイトに Web プロジェクトを発行する][Azure の Web サイトに Web プロジェクトを発行する]
-   [Azure の Web ジョブにコンソール アプリケーション プロジェクトを発行する][Azure の Web ジョブにコンソール アプリケーション プロジェクトを発行する]
-   [Azure の Web サイトおよび SQL データベース リソースを作成して、新しい Web プロジェクトを作成または発行する][Azure の Web サイトおよび SQL データベース リソースを作成して、新しい Web プロジェクトを作成または発行する]
-   [PowerShell デプロイメント スクリプトを作成して、新規の Web サイトを作成する][PowerShell デプロイメント スクリプトを作成して、新規の Web サイトを作成する]
-   [サーバー エクスプローラーで Azure Web サイトの管理とトラブルシューティングを行う][サーバー エクスプローラーで Azure Web サイトの管理とトラブルシューティングを行う]
-   [Web サイトおよび Web ジョブに対してリモート デバッグを実行する][Web サイトおよび Web ジョブに対してリモート デバッグを実行する]

> [WACOM.NOTE] これらの機能は Visual Studio の更新版にも含まれているため、Azure SDK for .NET をインストールしなくても利用できます。

### <span id="tools"></span></a>Microsoft Azure Tools for Microsoft Visual Studio

この製品を利用すると、Azure のクラウド サービスおよび仮想マシンで次のことができるようになります。

-   [クラウド サービス プロジェクトを作成、公開、発行する][クラウド サービス プロジェクトを作成、公開、発行する]
-   [クラウド サービス プロジェクトのデプロイメント パッケージを作成する][クラウド サービス プロジェクトのデプロイメント パッケージを作成する]
-   [Azure の仮想マシンを作成して、新規の Web プロジェクトを作成する][Azure の仮想マシンを作成して、新規の Web プロジェクトを作成する]
-   [PowerShell デプロイメント スクリプトを作成して、新規の仮想マシンを作成する][PowerShell デプロイメント スクリプトを作成して、新規の Web サイトを作成する]
-   [Visual Studio のプロジェクト プロパティ ウィンドウでクラウド サービス プロジェクトの設定を表示し、管理する][Visual Studio のプロジェクト プロパティ ウィンドウでクラウド サービス プロジェクトの設定を表示し、管理する]
-   サーバー エクスプローラーで[クラウド サービス][クラウド サービス]、[仮想マシン][仮想マシン]、および[サービス バス][サービス バス]を表示し、管理する
-   [クラウド サービスおよび仮想マシンに対してリモート デバッグを実行する][クラウド サービスおよび仮想マシンに対してリモート デバッグを実行する]

### <span id="auth"></span></a>Microsoft Azure Authoring Tools

この製品には次のツールが含まれています。

-   [CSPack コマンド ライン ツール。][CSPack コマンド ライン ツール。]デプロイメント パッケージを作成する場合に使用します。
-   [CSEncrypt コマンド ライン ツール。][CSEncrypt コマンド ライン ツール。]リモート デスクトップ接続を介してクラウド サービスのロール インスタンスにアクセスする際のパスワードを暗号化するために使用します。
-   クラウド サービス プロジェクトがランタイム環境と通信し、診断する場合に必要な各種のランタイム バイナリ。これらのバイナリは、NuGet パッケージでは利用できません。

### <span id="emulator"></span></a>Microsoft Azure エミュレーター

[Azure エミュレーター][Azure エミュレーター]は、クラウド サービス環境をシミュレートします。これにより、クラウド サービス プロジェクトを Azure にデプロイする前に、ローカルのコンピューターでテストできます。

### <span id="stgemulator"></span></a>Microsoft Azure ストレージ エミュレーター

[Azure ストレージ エミュレーター][Azure ストレージ エミュレーター]は、SQL サーバーのインスタンスとローカル ファイル システムを使用して Azure のストレージ (キュー、テーブル、BLOB) をシミュレートします。これにより、ローカルのコンピューターでテストできます。

### <span id="stgtools"></span></a>Microsoft Azure Storage Tools

この製品は、コマンド ライン ツールの [AzCopy][AzCopy] をインストールします。このツールを使用すると、Azure のストレージ アカウントにデータを転送したり、このアカウントからデータを転送したりすることができます。

### <span id="libraries"></span></a>Microsoft Azure Libraries for .NET

この製品には次のツールが含まれています。

-   Azure のストレージ、サービス バス、キャッシュ用の NuGet パッケージ。このパッケージは、ユーザーのコンピューターに格納されるため、Visual Studio による新規のクラウド サービス プロジェクトの作成がオフラインでできるようになります。
-   Visual Studio プラグイン。Visual Studio で [In-Role Cache][In-Role Cache] プロジェクトの実行がローカルでできるようになります。

### <span id="ls"></span></a>LightSwitch Azure Publishing add-on for Visual Studio

この製品を使用すると、[LightSwitch プロジェクトを Azure の Web サイトに発行][LightSwitch プロジェクトを Azure の Web サイトに発行]できます。LightSwitch アドオンは、Visual Studio のアップグレードの他、Azure SDK for .NET にも含まれています。この SDK をインストールすると、アドオンの最新バージョンを使用できます。

## <span id="notincluded"></span></a>Azure SDK for .NET のインストールに含まれないもの

SDK をインストールしたときに、Azure 向けアプリケーションの開発に必要なものが含まれない場合もあります。主なものは次のとおりです。

-   [クライアント ライブラリ][NuGet]

    Azure SDK には、Azure のサービスを使用するために必要なクライアント ライブラリが含まれています。ただし、SDK をインストールしたときに、そのすべてがインストールされるわけではありません。アプリケーションに必要なクライアント ライブラリが SDK によってインストールされない場合には、[NuGet.org][NuGet] で必要なライブラリを入手できます。クライアント ライブラリが SDK によってインストールされる場合でも、NuGet.org で現在のバージョンに更新することをお勧めいたします。

    **クライアント ライブラリのローカル コピー。**Azure SDK for .NET は、いくつかの Azure クライアント ライブラリ (ストレージ、サービス バス、キャッシュなど) 用の NuGet パッケージをユーザーのコンピューターにコピーします。これらのクライアント ライブラリは、新規のクラウド サービス プロジェクトに自動的に含められるため、インターネットに接続していない場合でも、Visual Studio はローカルの NuGet パッケージでプロジェクトを作成できます。一般に、クライアント ライブラリは SDK よりも頻繁に更新されているため、NuGet.org で入手できるクライアント ライブラリの方が SDK によってインストールされるものよりも新しい場合が多いことに注意してください。

    **クライアント ライブラリが含まれるプロジェクト テンプレート。**[Azure クラウド サービス][クラウド サービス プロジェクトを作成、公開、発行する]および [Azure モバイル サービス][Azure モバイル サービス]のプロジェクト テンプレートには、いくつかのクライアント ライブラリが自動的に含まれます。これらのテンプレートに他のライブラリを含める場合や、他のテンプレートを使用する場合は、必要な[クライアント ライブラリの NuGet パッケージ][NuGet]をインストールしてください。

-   [Azure PowerShell][Azure PowerShell]

    Azure PowerShell を使用すると、[Azure 環境の作成とデプロイを自動化][Azure 環境の作成とデプロイを自動化]できます。

-   [Azure モバイル サービスのプロジェクト テンプレート][Azure モバイル サービス]

    モバイル サービスのテンプレートは、Visual Studio 2013 Update 2 以降でのみ利用できます。Azure SDK for .NET をインストールしても、Visual Studio 2012 以前および Visual Studio 2013 Update 1 以前のバージョンでは利用できません。

## <span id="faq"></span></a>FAQ (よく寄せられる質問)

-   [Visual Studio に Azure の機能の多くが含まれているのにもかかわらず、Azure SDK for .NET をインストールする必要がありますか。][Visual Studio に Azure の機能の多くが含まれているのにもかかわらず、Azure SDK for .NET をインストールする必要がありますか。]
-   [クライアント ライブラリを入手したいのですが、Azure SDK for .NET をインストールしないと入手できませんか。][クライアント ライブラリを入手したいのですが、Azure SDK for .NET をインストールしないと入手できませんか。]
-   [以前のバージョンの Azure SDK for .NET はどこにありますか。][以前のバージョンの Azure SDK for .NET はどこにありますか。]
-   [Azure SDK for .NET のバージョンのライフサイクル ポリシーを教えてください。][Azure SDK for .NET のバージョンのライフサイクル ポリシーを教えてください。]
-   [Azure SDK for .NET との互換性があるゲスト OS を教えてください。][Azure SDK for .NET との互換性があるゲスト OS を教えてください。]

### <span id="azinvs"></span></a>Visual Studio に Azure の機能の多くが含まれているのにもかかわらず、Azure SDK for .NET をインストールする必要がありますか。

最新のツールを使用して Azure 向けの開発をする場合は、SDK をインストールすることをお勧めします。できれば SDK をインストールしたくない場合、次の条件を満たしていれば、インストールする必要はありません。

-   既に最新の [Visual Studio Update][Visual Studio Update] をインストールしている。
-   開発の対象が Azure Websites または Mobile Services 向けに限られており、Cloud Services または Virtual Machines 向けの開発ではない。
-   アプリケーションがストレージを使用しない、または使用するとしてもストレージ エミュレーターも AzCopy ツールも必要ではない。

### <span id="clientlib"></span></a>クライアント ライブラリを入手したいのですが、Azure SDK for .NET をインストールしないと入手できませんか。

SDK でクライアント ライブラリがインストールされるのは、インターネットに接続していなくてもクラウド サービス プロジェクトを作成できるようにする場合だけです。現行バージョンのクライアント ライブラリは、[NuGet.org][NuGet] で入手できる NuGet パッケージに用意されています。詳細については、この記事の初めの方に記載されている [Azure SDK for .NET のインストールに含まれないもの][Azure SDK for .NET のインストールに含まれないもの]を参照してください。

### <span id="olderversions"></span></a>以前のバージョンの Azure SDK for .NET はどこにありますか。

以前のバージョンは、ダウンロード ページの「[Azure SDK for .NET][Azure SDK for .NET]」で確認してください。

### <span id="lifecycle"></span></a> Azure SDK for .NET のバージョンのライフサイクル ポリシーを教えてください。

「[Microsoft Azure サポート ライフサイクル ポリシー][Microsoft Azure サポート ライフサイクル ポリシー]」を参照してください。

### <span id="guestos"></span></a>Azure SDK for .NET との互換性があるゲスト OS を教えてください。

「[Azure ゲスト OS リリースと SDK の互換性対応表][Azure ゲスト OS リリースと SDK の互換性対応表]」を参照してください。

## <span id="resources"></span></a>リソース

Azure SDK for .NET またはクライアント ライブラリをダウンロードするには、[Azure のダウンロード ページ][Azure のダウンロード ページ]を参照してください。

Azure SDK for .NET (クライアント ライブラリを含む) のソース コードについては、[GitHub.com/Azure の Web サイト][GitHub.com/Azure の Web サイト]を参照してください。

Azure のクライアント ライブラリのリファレンス ドキュメントについては、[Azure .NET のリファレンス ページ][Azure .NET のリファレンス ページ]を参照してください。

  [Azure のダウンロード ページ]: /ja-jp/downloads/
  [NuGet]: http://go.microsoft.com/fwlink/?LinkId=510472
  [Azure SDK for .NET のインストールに含まれるもの]: #included
  [Azure SDK for .NET のインストールに含まれないもの]: #notincluded
  [FAQ]: #faq
  [リソース]: #resources
  [Visual Studio Express for Web]: #vwd
  [Microsoft ASP.NET and Web Tools for Visual Studio]: #wte
  [Microsoft Azure Tools for Microsoft Visual Studio]: #tools
  [Microsoft Azure Authoring Tools]: #auth
  [Microsoft Azure エミュレーター]: #emulator
  [Microsoft Azure ストレージ エミュレーター]: #stgemulator
  [Microsoft Azure Storage Tools]: #stgtools
  [Microsoft Azure Libraries for .NET]: #libraries
  [LightSwitch Azure Publishing add-on for Visual Studio]: #ls
  [1]: http://www.visualstudio.com/ja-jp/products/visual-studio-express-vs.aspx
  [Azure の Web サイトに Web プロジェクトを発行する]: ../web-sites-dotnet-get-started/
  [Azure の Web ジョブにコンソール アプリケーション プロジェクトを発行する]: ../websites-dotnet-deploy-webjobs/
  [Azure の Web サイトおよび SQL データベース リソースを作成して、新しい Web プロジェクトを作成または発行する]: ../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [PowerShell デプロイメント スクリプトを作成して、新規の Web サイトを作成する]: http://msdn.microsoft.com/ja-jp/library/dn642480.aspx
  [サーバー エクスプローラーで Azure Web サイトの管理とトラブルシューティングを行う]: ../web-sites-dotnet-troubleshoot-visual-studio/#sitemanagement
  [Web サイトおよび Web ジョブに対してリモート デバッグを実行する]: ../web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [クラウド サービス プロジェクトを作成、公開、発行する]: ../cloud-services-dotnet-get-started/
  [クラウド サービス プロジェクトのデプロイメント パッケージを作成する]: http://msdn.microsoft.com/ja-jp/library/ff683672.aspx
  [Azure の仮想マシンを作成して、新規の Web プロジェクトを作成する]: ../virtual-machines-dotnet-create-visual-studio-powershell/
  [Visual Studio のプロジェクト プロパティ ウィンドウでクラウド サービス プロジェクトの設定を表示し、管理する]: http://msdn.microsoft.com/ja-jp/library/ee405486.aspx
  [クラウド サービス]: http://msdn.microsoft.com/ja-jp/library/ff683675.aspx
  [仮想マシン]: http://msdn.microsoft.com/ja-jp/library/jj131259.aspx
  [サービス バス]: http://msdn.microsoft.com/ja-jp/library/jj149828.aspx
  [クラウド サービスおよび仮想マシンに対してリモート デバッグを実行する]: http://msdn.microsoft.com/ja-jp/library/ff683670.aspx
  [CSPack コマンド ライン ツール。]: http://msdn.microsoft.com/ja-jp/library/gg432988.aspx
  [CSEncrypt コマンド ライン ツール。]: http://msdn.microsoft.com/ja-jp/library/hh404001.aspx
  [Azure エミュレーター]: http://msdn.microsoft.com/ja-jp/library/dn339018.aspx
  [Azure ストレージ エミュレーター]: http://msdn.microsoft.com/ja-jp/library/hh403989.aspx
  [AzCopy]: http://aka.ms/AzCopy
  [In-Role Cache]: http://msdn.microsoft.com/ja-jp/library/dn386103.aspx
  [LightSwitch プロジェクトを Azure の Web サイトに発行]: http://msdn.microsoft.com/ja-jp/library/jj131261.aspx
  [Azure モバイル サービス]: ../mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/
  [Azure PowerShell]: ../install-configure-powershell/
  [Azure 環境の作成とデプロイを自動化]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [Visual Studio に Azure の機能の多くが含まれているのにもかかわらず、Azure SDK for .NET をインストールする必要がありますか。]: #azinvs
  [クライアント ライブラリを入手したいのですが、Azure SDK for .NET をインストールしないと入手できませんか。]: #clientlib
  [以前のバージョンの Azure SDK for .NET はどこにありますか。]: #olderversions
  [Azure SDK for .NET のバージョンのライフサイクル ポリシーを教えてください。]: #lifecycle
  [Azure SDK for .NET との互換性があるゲスト OS を教えてください。]: #guestos
  [Visual Studio Update]: http://www.visualstudio.com/ja-jp/downloads/download-visual-studio-vs#DownloadFamilies_5
  [Azure SDK for .NET]: /ja-jp/downloads/archive-net-downloads/
  [Microsoft Azure サポート ライフサイクル ポリシー]: http://support.microsoft.com/gp/azure-cloud-lifecycle-faq
  [Azure ゲスト OS リリースと SDK の互換性対応表]: http://msdn.microsoft.com/ja-jp/library/ee924680.aspx
  [GitHub.com/Azure の Web サイト]: https://github.com/azure/
  [Azure .NET のリファレンス ページ]: /ja-jp/develop/net/reference/
