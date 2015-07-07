<properties 
	pageTitle="Windows 仮想マシンを作成するさまざまな方法" 
	description="Windows 仮想マシンを作成するさまざまな方法を紹介し、手順へのリンクを提供します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-windows" 
	ms.workload="infrastructure-services"
	ms.date="05/14/2015" 
	ms.author="kathydav"/>

# Windows 仮想マシンを作成するさまざまな方法

VM は多様なユーザーや目的に適しているため、Azure には VM を作成するためのさまざまな方法が用意されています。このため、VM とその作成方法について、いくつかの選択をする必要があります。この記事には、これらの選択肢の概要と、手順へのリンクが記載されています。

仮想マシンとその各種リソースを 1 つの論理的なデプロイメント単位として作成および管理する手段として、Azure リソース マネージャー テンプレートが最近導入されました。このアプローチに関する手順が、以下で該当する箇所に記載されています。Azure リソース マネージャー、およびリソースを 1 つの単位として管理する方法の詳細については、[概要][]に関するページを参照してください。

## ツールの選択肢

### GUI: Azure ポータルまたは Azure プレビュー ポータル 

Azure ポータルのグラフィカル ユーザー インターフェイスを使用すれば、特に Azure を初めて使用する場合に、仮想マシンを簡単に試すことができます。VM の作成は、Azure ポータルと Azure プレビュー ポータルのいずれかを使用して行います。

[Windows を実行する仮想マシンの作成][]

### コマンド シェル: Azure CLI または Azure PowerShell

コマンド シェルを使用する場合は、Mac および Linux ユーザー向けの Azure コマンド ライン インターフェイス (CLI) と、Azure 用 Windows PowerShell コマンドレットおよびカスタム コンソールを備えた Azure PowerShell のいずれかを選択します。

Azure CLI の場合は、[Mac、Linux、および Windows 用 Azure CLI での VM 操作に使用するリソース マネージャーおよびサービス管理コマンドの対応][]に関するページを参照してください。テンプレートを使用する場合は、「[Azure リソース マネージャー テンプレートと Azure CLI を使用した Virtual Machines のデプロイと管理][]」を参照してください。

Azure PowerShell については、「[Mac、Linux、および Windows 用 Azure CLI での VM 操作に使用するリソース マネージャーおよびサービス管理コマンドの対応][]」を参照してください。テンプレートの使用方法については、「[Azure リソース マネージャー テンプレートと Azure CLI を使用した Virtual Machines のデプロイと管理][]」を参照してください。

### 開発環境: Visual Studio

[Visual Studio で Web サイト用の仮想マシンを作成する][]

[Compute、ネットワーク、およびストレージの .NET ライブラリを使用した Azure リソースのデプロイ][]

## オペレーティング システムとイメージの選択肢

実行するオペレーティング システムに基づいてイメージを選択します。Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールが含まれているイメージもあります。また、独自のイメージを使用することもできます。

### Azure のイメージ

この手順では、Azure のイメージを使用して、ネットワークや負荷分散などのオプションを使用してカスタマイズされた仮想マシンを作成する方法について説明しています。「[Windows を実行するカスタム仮想マシンを作成する方法][]」を参照してください。

### 独自のイメージを使用する

既存の Azure 仮想マシンをベースにしたイメージを使用するには、その VM を*キャプチャする*か、独自のイメージを仮想ハード ディスク (VHD) に格納してアップロードします。

- [テンプレートとして使用する Windows 仮想マシンのキャプチャ方法][]
- [Windows Server VHD の作成と Azure へのアップロード][]

## 次のステップ

[仮想マシンへのログオン][]

[データ ディスクの接続][]

## その他のリソース
[Azure VM 構成設定について][]

[基本構成テスト環境][]

[Azure ハイブリッド クラウド テスト環境][]

<!-- LINKS -->
[概要]: ../resource-group-overview.md

[Windows を実行する仮想マシンの作成]: virtual-machines-windows-tutorial.md

[Mac、Linux、および Windows 用 Azure CLI での VM 操作に使用するリソース マネージャーおよびサービス管理コマンドの対応]: xplat-cli-azure-manage-vm-asm-arm.md
[Azure リソース マネージャー テンプレートと Azure CLI を使用した Virtual Machines のデプロイと管理]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Azure リソース マネージャー テンプレートと Azure CLI を使用した Virtual Machines のデプロイと管理]: virtual-machines-deploy-rmtemplates-powershell.md
[Mac、Linux、および Windows 用 Azure CLI での VM 操作に使用するリソース マネージャーおよびサービス管理コマンドの対応]: virtual-machines-ps-create-preconfigure-windows-vms.md

[Windows を実行するカスタム仮想マシンを作成する方法]: virtual-machines-windows-create-custom.md

[テンプレートとして使用する Windows 仮想マシンのキャプチャ方法]: virtual-machines-capture-image-windows-server.md

[Windows Server VHD の作成と Azure へのアップロード]: virtual-machines-create-upload-vhd-windows-server.md


[Visual Studio で Web サイト用の仮想マシンを作成する]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Compute、ネットワーク、およびストレージの .NET ライブラリを使用した Azure リソースのデプロイ]: virtual-machines-arm-deployment.md

[仮想マシンへのログオン]: virtual-machines-log-on-windows-server.md

[データ ディスクの接続]: storage-windows-attach-disk.md

[Azure VM 構成設定について]: http://msdn.microsoft.com/library/azure/dn763935.aspx

[基本構成テスト環境]: virtual-machines-base-configuration-test-environment.md

[Azure ハイブリッド クラウド テスト環境]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=62-->