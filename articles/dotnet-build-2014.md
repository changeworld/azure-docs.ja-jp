<properties pageTitle="Azure Spring 2014 release highlights - .NET Dev Center" metaKeywords="azure .net sdk 2.3" description="Learn about the new tools and features available for Azure .NET developers." documentationCenter=".NET" title="Azure Spring 2014 release highlights" authors="mollybos" solutions="" manager="carolz" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="mollybos"></tags>

# Azure Spring 2014 リリースの重要事項

この記事では、Build 2014 会議で Azure .NET 開発者に示された新しいツール、機能、およびテーマの概要について説明します。これらは Azure SDK for .NET 2.3 や Visual Studio 2013 Update 2 などの Spring Release で入手できます。

**ツールの入手:**

-   [Visual Studio 2013 Update 2 RC][Visual Studio 2013 Update 2 RC]
-   [Azure SDK 2.3][Azure SDK 2.3]
-   [Azure PowerShell][Azure PowerShell]
-   [Azure クロスプラットフォーム コマンド ライン インターフェイス][Azure クロスプラットフォーム コマンド ライン インターフェイス]

Spring 2014 ツール リリースの完全な詳細については、「[Azure SDK for .NET 2.3 リリース ノート][Azure SDK for .NET 2.3 リリース ノート]」および「[Visual Studio 2013 の最新情報][Visual Studio 2013 の最新情報]」を参照してください。

[ビルドに関するビデオ][ビルドに関するビデオ]をオンデマンド ストリーミングでご覧ください。

## 目次

-   [Web 開発と発行][Web 開発と発行]
-   [診断とデバッグ][診断とデバッグ]
-   [Visual Studio での Azure サービスの管理][Visual Studio での Azure サービスの管理]
-   [PowerShell での自動化][PowerShell での自動化]
-   [.NET でのモバイル開発][.NET でのモバイル開発]
-   [ストレージ クライアント ライブラリ 3.0 と新しいストレージ エミュレーター][ストレージ クライアント ライブラリ 3.0 と新しいストレージ エミュレーター]
-   [リソース マネージャー][リソース マネージャー]

## <span id="webdeploy"></span></a>Web 開発と発行

Azure SDK 2.3 および Visual Studio 2013 Update 2 RC には、Azure での Web 開発および発行を合理化するいくつかの更新が含まれています。新しい Web アプリケーションを作成するときに Azure の Web サイトまたは仮想マシンを作成できるようになりました。Web サイトを発行する準備が整ったときに、更新された Web 発行ダイアログ、またはソリューションに追加される PowerShell スクリプトを使用することで、Azure の Web サイトまたは仮想マシンにサイトを直接デプロイできます。新機能を活用する方法の詳細およびチュートリアルについては、次のリソースを確認してください。

-   [Azure と ASP.NET の使用][Azure と ASP.NET の使用]
-   [Azure Tools for Visual Studio の概要][Azure Tools for Visual Studio の概要]
-   [Creating ASP.NET Web Projects in Visual Studio 2013 (Visual Studio 2013 での ASP.NET Web プロジェクトの作成)][Creating ASP.NET Web Projects in Visual Studio 2013 (Visual Studio 2013 での ASP.NET Web プロジェクトの作成)]
-   [Build 2014: What's New for ASP.NET and Web in Visual Studio 2013 Update 2 and Beyond (Build 2014: Visual Studio 2013 Update 2 の ASP.NET および Web の新機能など) (ビデオ)][Build 2014: What's New for ASP.NET and Web in Visual Studio 2013 Update 2 and Beyond (Build 2014: Visual Studio 2013 Update 2 の ASP.NET および Web の新機能など) (ビデオ)]

## <span id="diagnostics"></span></a>診断とデバッグ

仮想マシン用の新しいリモート デバッグおよびネイティブ コード デバッグを使用して、アプリケーションの問題をリモートで診断します。

-   [Visual Studio でのクラウド サービスまたは仮想マシンのデバッグ][Visual Studio でのクラウド サービスまたは仮想マシンのデバッグ]

エミュレーター Express は、クラウド サービス用の新しい軽量のローカル エミュレーターです。これを使用して、ローカル マシンでクラウド サービスをテストする方法について学習します。

-   [Emulator Express を使用したローカルでのクラウド サービス実行とデバッグ][Emulator Express を使用したローカルでのクラウド サービス実行とデバッグ]

Azure SDK 2.3 をインストールすると、サーバー エクスプローラーから直接 Web サイト ファイルの表示や編集をリモートで実行したり、サイトのログ ファイルを表示したりできます。編集したファイルを保存すると自分のサイトに保存されます。発行する必要はありません。詳細については、次のリンクを参照してください。

-   [Visual Studio での Azure の Web サイトのトラブルシューティング][Visual Studio での Azure の Web サイトのトラブルシューティング]

## <span id="service-management"></span></a>Visual Studio での Azure サービスの管理

IDE 内から VM を作成する機能など強化された仮想マシン管理を Visual Studio から利用できます。

-   [Visual Studio での Azure 仮想マシンの作成][Visual Studio での Azure 仮想マシンの作成]
-   [サーバー エクスプローラーから Azure の仮想マシンへのアクセス][サーバー エクスプローラーから Azure の仮想マシンへのアクセス]

サーバー エクスプローラーから他の Azure サービスをより効果的に管理するための改良もいくつか行われました。詳細については、次のリンクを参照してください。

-   [Visual Studio サーバー エクスプローラーを使用して Service Bus のリソースを参照する][Visual Studio サーバー エクスプローラーを使用して Service Bus のリソースを参照する]
-   [サーバー エクスプローラーを使用したストレージ リソースの参照][サーバー エクスプローラーを使用したストレージ リソースの参照]

## <span id="automation"></span></a>PowerShell と API による自動化

Azure Powershell をインストールして、Web サイトや Web ジョブなどの新しいコマンドレットを利用できます。.NET 用のサービス管理 API を使用してカスタムの自動化をさらに記述できます。詳細については、次のリンクを参照してください。

-   [Azure PowerShell のインストールおよび構成方法][Azure PowerShell のインストールおよび構成方法]
-   [Azure PowerShell][1]
-   [Build 2014: Automation Everywhere with New SDKs, Tools, and Services in Azure (Build 2014: Azure 内の新しい SDK、ツール、およびサービスであらゆる場所を自動化) (ビデオ)][Build 2014: Automation Everywhere with New SDKs, Tools, and Services in Azure (Build 2014: Azure 内の新しい SDK、ツール、およびサービスであらゆる場所を自動化) (ビデオ)]

Visual Studio で PowerShell スクリプトを直接作成し、これらを使用して環境の作成を自動化します。

-   [Windows PowerShell スクリプトを使用した開発環境およびテスト環境の発行][Windows PowerShell スクリプトを使用した開発環境およびテスト環境の発行]

## <span id="mobile"></span></a>.NET でのモバイル開発

Azure モバイル サービスにより、Windows Store、Windows Phone、iOS、Android などのモバイル プラットフォームを対象としたモバイル アプリケーションに対して、.NET ベースのバックエンドを使用するためのオプションが提供されます。詳細については、次のリンクを参照してください。

-   [Cloud Cover Episode 137: The Azure Mobile Services .NET Backend with Yavor Georgiev (Cloud Cover エピソード 137: Yavor Georgiev による Azure モバイル サービスの .NET バックエンド) (ビデオ)][Cloud Cover Episode 137: The Azure Mobile Services .NET Backend with Yavor Georgiev (Cloud Cover エピソード 137: Yavor Georgiev による Azure モバイル サービスの .NET バックエンド) (ビデオ)]
-   [Azure モバイル デベロッパー センター][Azure モバイル デベロッパー センター]

Visual Studio 2013 Update 2 には、サーバー エクスプローラーでのリモート デバッグのサポートや通知ハブの統合など、モバイル開発の新しいサポートも含まれています。詳細については、次のリンクを参照してください。

-   [クイック スタート: モバイル サービスの追加][クイック スタート: モバイル サービスの追加]
-   [Visual Studio で実行中のアプリにプッシュ通知を送信する方法][Visual Studio で実行中のアプリにプッシュ通知を送信する方法]
-   [モバイル サービスでカスタム API とスケジュールされたジョブを作成する方法][モバイル サービスでカスタム API とスケジュールされたジョブを作成する方法]

## <span id="storage"></span></a>ストレージ クライアント ライブラリ 3.0

Azure SDK 2.3 には、更新されたストレージ エミュレーターが含まれ、ストレージ クライアント ライブラリ 3.0 が SDK のプロジェクト テンプレートに統合されています。

詳細については、次のリンクを参照してください。

-   [Azure ストレージ クライアント ライブラリ 3.0][Azure ストレージ クライアント ライブラリ 3.0]
-   [Microsoft Azure ストレージの概要][Microsoft Azure ストレージの概要]
-   [Build 2014: Microsoft Azure Storage – What's New, Best Practices and Patterns (Build 2014: Microsoft Azure ストレージ – 新機能、ベスト プラクティス、およびパターン) (ビデオ)][Build 2014: Microsoft Azure Storage – What's New, Best Practices and Patterns (Build 2014: Microsoft Azure ストレージ – 新機能、ベスト プラクティス、およびパターン) (ビデオ)]
-   [Microsoft Azure Storage @ BUILD 2014 (Microsoft Azure ストレージ @ BUILD 2014)][Microsoft Azure Storage @ BUILD 2014 (Microsoft Azure ストレージ @ BUILD 2014)]

## <span id="arm"></span></a>リソース マネージャー

リソース マネージャーは、リソース全体のアプリケーションのデプロイと管理のための新しいフレームワークです。新しい JSON エディター、PowerShell コマンドレット、および CLI サポートを使用してリソース マネージャーを試してみてください。詳細については、次のリンクを参照してください。

-   [リソース マネージャーでの Windows PowerShell の使用][リソース マネージャーでの Windows PowerShell の使用]
-   [リソース マネージャーでの Azure クロスプラットフォーム コマンドライン インターフェイスの使用][リソース マネージャーでの Azure クロスプラットフォーム コマンドライン インターフェイスの使用]
-   [Build 2014: Azure Resource Group Model: Modern Management for Modern Cloud (Build 2014: Azure リソース グループ モデル: 最先端のクラウドに対する最先端の管理) (ビデオ)][Build 2014: Azure Resource Group Model: Modern Management for Modern Cloud (Build 2014: Azure リソース グループ モデル: 最先端のクラウドに対する最先端の管理) (ビデオ)]

  [Visual Studio 2013 Update 2 RC]: http://aka.ms/vs2013update2rc
  [Azure SDK 2.3]: http://www.windowsazure.com/ja-jp/downloads/
  [Azure PowerShell]: http://go.microsoft.com/?linkid=9811175
  [Azure クロスプラットフォーム コマンド ライン インターフェイス]: http://go.microsoft.com/?linkid=9828653
  [Azure SDK for .NET 2.3 リリース ノート]: http://go.microsoft.com/fwlink/p/?LinkId=393548
  [Visual Studio 2013 の最新情報]: http://go.microsoft.com/fwlink/?LinkId=272487
  [ビルドに関するビデオ]: http://go.microsoft.com/fwlink/?LinkId=394377&clcid=0x409
  [Web 開発と発行]: #webdeploy
  [診断とデバッグ]: #diagnostics
  [Visual Studio での Azure サービスの管理]: #service-management
  [PowerShell での自動化]: #automation
  [.NET でのモバイル開発]: #mobile
  [ストレージ クライアント ライブラリ 3.0 と新しいストレージ エミュレーター]: #storage
  [リソース マネージャー]: #arm
  [Azure と ASP.NET の使用]: http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-dotnet-get-started/
  [Azure Tools for Visual Studio の概要]: http://msdn.microsoft.com/ja-jp/library/azure/ff687127.aspx
  [Creating ASP.NET Web Projects in Visual Studio 2013 (Visual Studio 2013 での ASP.NET Web プロジェクトの作成)]: http://asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio
  [Build 2014: What's New for ASP.NET and Web in Visual Studio 2013 Update 2 and Beyond (Build 2014: Visual Studio 2013 Update 2 の ASP.NET および Web の新機能など) (ビデオ)]: http://channel9.msdn.com/Events/Build/2014/3-602
  [Visual Studio でのクラウド サービスまたは仮想マシンのデバッグ]: http://msdn.microsoft.com/ja-jp/library/azure/ff683670.aspx
  [Emulator Express を使用したローカルでのクラウド サービス実行とデバッグ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn339018.aspx
  [Visual Studio での Azure の Web サイトのトラブルシューティング]: http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio
  [Visual Studio での Azure 仮想マシンの作成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn569263.aspx
  [サーバー エクスプローラーから Azure の仮想マシンへのアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj131259.aspx
  [Visual Studio サーバー エクスプローラーを使用して Service Bus のリソースを参照する]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj149828.aspx
  [サーバー エクスプローラーを使用したストレージ リソースの参照]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff683677.aspx
  [Azure PowerShell のインストールおよび構成方法]: http://www.windowsazure.com/ja-jp/documentation/articles/install-configure-powershell/
  [1]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156055.aspx
  [Build 2014: Automation Everywhere with New SDKs, Tools, and Services in Azure (Build 2014: Azure 内の新しい SDK、ツール、およびサービスであらゆる場所を自動化) (ビデオ)]: http://channel9.msdn.com/Events/Build/2014/3-621
  [Windows PowerShell スクリプトを使用した開発環境およびテスト環境の発行]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn642480.aspx
  [Cloud Cover Episode 137: The Azure Mobile Services .NET Backend with Yavor Georgiev (Cloud Cover エピソード 137: Yavor Georgiev による Azure モバイル サービスの .NET バックエンド) (ビデオ)]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-137-The-Azure-Mobile-Services-NET-Backend-with-Yavor-Georgiev
  [Azure モバイル デベロッパー センター]: /ja-jp/develop/mobile/
  [クイック スタート: モバイル サービスの追加]: http://msdn.microsoft.com/ja-jp/library/windows/apps/xaml/dn629482.aspx
  [Visual Studio で実行中のアプリにプッシュ通知を送信する方法]: http://msdn.microsoft.com/ja-jp/library/windows/apps/xaml/dn614131.aspx
  [モバイル サービスでカスタム API とスケジュールされたジョブを作成する方法]: http://msdn.microsoft.com/ja-jp/library/windows/apps/xaml/dn614130.aspx
  [Azure ストレージ クライアント ライブラリ 3.0]: http://go.microsoft.com/fwlink/?LinkId=394927
  [Microsoft Azure ストレージの概要]: /ja-jp/documentation/articles/storage-introduction/
  [Build 2014: Microsoft Azure Storage – What's New, Best Practices and Patterns (Build 2014: Microsoft Azure ストレージ – 新機能、ベスト プラクティス、およびパターン) (ビデオ)]: http://channel9.msdn.com/Events/Build/2014/3-628
  [Microsoft Azure Storage @ BUILD 2014 (Microsoft Azure ストレージ @ BUILD 2014)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/08/microsoft-azure-storage-build-2014.aspx
  [リソース マネージャーでの Windows PowerShell の使用]: http://go.microsoft.com/fwlink/?LinkID=394767
  [リソース マネージャーでの Azure クロスプラットフォーム コマンドライン インターフェイスの使用]: /ja-jp/documentation/articles/xplat-cli-azure-resource-manager/
  [Build 2014: Azure Resource Group Model: Modern Management for Modern Cloud (Build 2014: Azure リソース グループ モデル: 最先端のクラウドに対する最先端の管理) (ビデオ)]: http://channel9.msdn.com/Events/Build/2014/2-607
