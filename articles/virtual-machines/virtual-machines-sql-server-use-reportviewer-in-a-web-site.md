<properties 
	pageTitle="Web サイトで ReportViewer を使用する | Microsoft Azure"
	description="このトピックでは、Microsoft Azure 仮想マシンに保存されたレポートを表示する Visual Studio ReportViewer コントロールを使用して、Microsoft Azure Web サイトを作成する方法を説明します。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth" />

# Azure でホストされる Web サイトで ReportViewer を使用する

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


Microsoft Azure 仮想マシンに保存されたレポートを表示する Visual Studio ReportViewer コントロールを使用して、Microsoft Azure Web サイトを作成できます。ReportViewer コントロールは、ASP.NET Web アプリケーション テンプレートを使用して作成する Web アプリケーション内にあります。

>[AZURE.IMPORTANT]ASP.NET MVC Web アプリケーション テンプレートでは、ReportViewer コントロールはサポートされません。

ReportViewer を Microsoft Azure Web サイトに組み込むには、次のタスクを完了する必要があります。

- デプロイ パッケージにアセンブリを**追加する**

- 認証と承認を**構成する**

- ASP.NET Web アプリケーションを Azure に**発行する**

## 前提条件

「[Azure Virtual Machines での SQL Server Business Intelligence](virtual-machines-sql-server-business-intelligence.md)」の「一般的な推奨事項とベスト プラクティス」セクションを参照してください。

>[AZURE.NOTE]ReportViewer コントロールは、Standard Edition 以上のエディションの Visual Studio に含まれています。Web Developer Express Edition を使用している場合、ReportViewer ランタイム機能を使用するには、[Microsoft Report Viewer 2012 Runtime](https://www.microsoft.com/download/details.aspx?id=35747) をインストールする必要があります。
>
>Microsoft Azure では、ローカル処理モードで構成された ReportViewer はサポートされません。

ホワイト ペーパー「[Reporting Services report viewer control and Microsoft Azure virtual machine based report servers (Reporting Services レポート ビューアー コントロールと Microsoft Azure 仮想マシン ベースのレポート サーバー)](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)」を参照してください。

## デプロイ パッケージにアセンブリを追加する

ASP.NET アプリケーションをオンプレミスでホストする場合、ReportViewer アセンブリは通常、Visual Studio のインストール時に IIS サーバーのグローバル アセンブリ キャッシュ (GAC) 内にインストールされるため、アプリケーションから直接アクセスが可能です。ただし、ASP.NET アプリケーションをクラウドでホストする場合、Microsoft Azure では GAC へのインストールが許可されないため、ReportViewer アセンブリがアプリケーションでローカルで使用できるようにしておく必要があります。このためには、プロジェクトにアセンブリへの参照を追加し、これらがローカルでコピーされるように構成します。

リモート処理モードでは、次のアセンブリが ReportViewer コントロールで使用されます。

- **Microsoft.ReportViewer.WebForms.dll**: ページ内で ReportViewer を使用するために必要な ReportViewer コードが含まれています。プロジェクト内の ASP.NET ページに ReportViewer コントロールをドロップすると、このアセンブリへの参照がプロジェクトに追加されます。

- **Microsoft.ReportViewer.Common.dll**: 実行時に ReportViewer コントロールによって使用されるクラスが含まれています。自動的にはプロジェクトに追加されません。

### Microsoft.ReportViewer.Common に参照を追加する

- プロジェクトの **[参照]** ノードを右クリックして **[参照の追加]** を選択し、[.NET] タブからアセンブリを選択して **[OK]** をクリックします。

### ASP.NET アプリケーションからアセンブリにローカル アクセスできるようにする

1. **[参照]** フォルダー内の Microsoft.ReportViewer.Common アセンブリをクリックすると、プロパティがプロパティ ペインに表示されます。

1. プロパティ ペインで、**[ローカルにコピー]** を True に設定します。

1. Microsoft.ReportViewer.WebForms についても、手順 1. と 2. を繰り返します。

### ReportViewer Language Pack を入手する

1. [Microsoft ダウンロード センター](http://go.microsoft.com/fwlink/?LinkId=317386)から、適切な Microsoft Report Viewer 2012 Runtime 再頒布可能パッケージをインストールします。

1. ドロップダウン リストから言語を選択すると、ダウンロード センターの対応するページにリダイレクトされます。

1. **[ダウンロード]** をクリックして、ReportViewerLP.exe のダウンロードを開始します。

1. ReportViewerLP.exe をダウンロードしたら、**[実行]** をクリックしてすぐにインストールするか、**[保存]** をクリックしてコンピューターに保存します。**[保存]** をクリックした場合は、ファイルを保存したフォルダーの名前を覚えておいてください。

1. ファイルを保存したフォルダーを見つけます。ReportViewerLP.exe を右クリックし、**[管理者として実行]**、**[はい]** の順にクリックします。

1. ReportViewerLP.exe を実行した後、C:\\windows\\assembly にリソース ファイル **Microsoft.ReportViewer.Webforms.Resources** および **Microsoft.ReportViewer.Common.Resources** が含まれていることが確認できます。

### ローカライズされた ReportViewer コントロール用に構成する

1. 前の手順に従って、Microsoft Report Viewer 2012 Runtime 再頒布可能パッケージをダウンロードしてインストールします。

1. プロジェクトに <language> フォルダーを作成し、関連付けられたリソース アセンブリ ファイルをそこにコピーします。コピーするリソース アセンブリ ファイルは、**Microsoft.ReportViewer.Webforms.Resources.dll** と **Microsoft.ReportViewer.Common.Resources.dll** です。リソース アセンブリ ファイルを選択し、プロパティ ウィンドウで **[出力ディレクトリにコピー]** を “**Copy always**” に設定します。

1. Web プロジェクトのカルチャと UI カルチャを設定します。ASP.NET Web ページのカルチャと UI カルチャを設定する方法の詳細については、「[方法 : ASP.NET Web ページのグローバリゼーション用のカルチャおよび UI カルチャを設定する](http://go.microsoft.com/fwlink/?LinkId=237461)」を参照してください。

## 認証と承認を構成する

ReportViewer は、適切な資格情報を使用してレポート サーバーに対する認証を行う必要があります。必要なレポートにアクセスするには、その資格情報がレポート サーバーによって承認される必要があります。認証についての詳細は、ホワイト ペーパー「[Reporting Services report viewer control and Microsoft Azure virtual machine based report servers (Reporting Services レポート ビューアー コントロールと Microsoft Azure 仮想マシン ベースのレポート サーバー)](https://msdn.microsoft.com/library/azure/dn753698.aspx)」を参照してください。

## ASP.NET Web アプリケーションを Azure に発行する

ASP.NET Web アプリケーションを Azure に発行する手順については、「[方法:Visual Studio から Azure クラウド サービスへの Web アプリケーションの移行および発行](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)」および「[Web Apps と ASP.NET の概要](../app-service-web/web-sites-dotnet-get-started.md)」を参照してください。

>[AZURE.IMPORTANT] [Azure デプロイ プロジェクトの追加] または [Azure クラウド サービス プロジェクトの追加] コマンドがソリューション エクスプローラーのショートカット メニューに表示されない場合は、必要に応じてプロジェクトの [ターゲット フレームワーク] を .NET Framework 4 に変更してください。
>
>これら 2 つのコマンド機能は、実質的には同じです。インストールされている Microsoft Azure SDK のバージョンに合わせて応じて、いずれか一方のコマンドがショートカット メニューに表示されます。

## リソース

[Microsoft レポート](http://go.microsoft.com/fwlink/?LinkId=205399)

[Azure Virtual Machines での SQL Server Business Intelligence](virtual-machines-sql-server-business-intelligence.md)

[ネイティブ モードのレポート サーバーを実行する Azure VM を PowerShell を使用して作成する](virtual-machines-sql-server-create-native-mode-report-server-powershell.md)

[Reporting Services のレポート ビューアー制御と Microsoft Azure 仮想マシン ベースのレポート サーバー](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)

<!---HONumber=Nov15_HO3-->