<properties
	pageTitle="Windows 仮想マシンを作成するさまざまな方法"
	description="Windows 仮想マシンを作成するさまざまな方法を紹介し、手順へのリンクを提供します。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Windows 仮想マシンを作成するさまざまな方法

仮想マシンは多様なユーザーや目的に適しているため、Azure には仮想マシンを作成するためのさまざまな方法が用意されています。これは、仮想マシンとその作成方法について、いくつかの選択を行う必要があることを意味します。この記事には、これらの選択肢の概要と、手順へのリンクが記載されています。

仮想マシンとその各種リソースを 1 つの論理的なデプロイメント単位として作成および管理する手段として、Azure リソース マネージャー テンプレートが最近導入されました。このアプローチに関する手順が、以下で該当する箇所に記載されています。Azure リソース マネージャー、およびリソースを 1 つの単位として管理する方法の詳細については、[概要][]に関するページを参照してください。

## ツールの選択肢

### GUI: Azure ポータルまたは Azure プレビュー ポータル

Azure ポータルのグラフィカル ユーザー インターフェイスを使用すれば、特に Azure を初めて使用する場合に、仮想マシンを簡単に試すことができます。仮想マシンの作成は、Azure ポータルまたは Azure プレビュー ポータルのいずれかを使用して行います。

[Windows を実行する仮想マシンの作成][]

### コマンド シェル: Azure CLI または Azure PowerShell

コマンド シェルを使用する場合は、Mac および Linux ユーザー向けの Azure コマンド ライン インターフェイス (CLI) と、Azure 用 Windows PowerShell コマンドレットおよびカスタム コンソールを備えた Azure PowerShell のいずれかを選択します。

Azure CLI については、「[Mac、Linux、および Windows 用 Azure CLI での VM 操作に使用するリソース マネージャーおよびサービス管理コマンドの対応][]」を参照してください。テンプレートを使用するには、「[Azure リソース マネージャー テンプレートと Azure CLI を使用した Virtual Machines のデプロイと管理][]」を参照してください。

Azure PowerShell については、「[Use Azure PowerShell to create and preconfigure Windows virtual machines (Azure PowerShell を使用して Windows 仮想マシンを作成し事前構成する)][]」を参照してください。テンプレートを使用するには、「[Azure リソース マネージャー テンプレートと Azure CLI を使用した Virtual Machines のデプロイと管理][]」を参照してください。サービス管理スタック内で仮想マシンを作成するには、「[Use Azure PowerShell to create and preconfigure Windows virtual machines (Azure PowerShell を使用して Windows 仮想マシンを作成し事前構成する)][]」を参照してください。

### 開発環境: Visual Studio

[Visual Studio による、 Web サイト用の仮想マシンの作成][]

[Compute、ネットワーク、およびストレージの .NET ライブラリを使用した Azure リソースのデプロイ][]

## オペレーティング システムとイメージの選択肢

実行するオペレーティング システムに基づいてイメージを選択します。Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールが含まれているイメージもあります。また、独自のイメージを使用することもできます。

### Azure のイメージ

この手順では、Azure のイメージを使用して、ネットワークや負荷分散などのオプションを使用してカスタマイズされた仮想マシンを作成する方法について説明しています。「[Windows を実行するカスタム仮想マシンを作成する方法][]」を参照してください。

### 独自のイメージを使用する

既存の Azure 仮想マシンをベースにしたイメージを使用するには、その仮想マシンを*キャプチャする*か、独自のイメージを仮想ハード ディスク (VHD) に格納してアップロードします。

- [イメージとして使用する Windows 仮想マシンのキャプチャ方法][]
- [Windows Server VHD の作成と Azure へのアップロード][]

## 次のステップ

[仮想マシンへのサインイン][]

[データ ディスクの接続][]

## その他のリソース
[基本構成テスト環境][]

[Azure ハイブリッド クラウド テスト環境][]

<!-- LINKS -->
[概要]: ../resource-group-overview.md

[Windows を実行する仮想マシンの作成]: virtual-machines-windows-tutorial.md

[Mac、Linux、および Windows 用 Azure CLI での VM 操作に使用するリソース マネージャーおよびサービス管理コマンドの対応]: xplat-cli-azure-manage-vm-asm-arm.md
[Azure リソース マネージャー テンプレートと Azure CLI を使用した Virtual Machines のデプロイと管理]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Create and preconfigure a Windows virtual machine with Resource Manager and Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Azure リソース マネージャー テンプレートと Azure CLI を使用した Virtual Machines のデプロイと管理]: virtual-machines-deploy-rmtemplates-powershell.md
[Use Azure PowerShell to create and preconfigure Windows virtual machines (Azure PowerShell を使用して Windows 仮想マシンを作成し事前構成する)]: virtual-machines-ps-create-preconfigure-windows-vms.md
[Windows を実行するカスタム仮想マシンを作成する方法]: virtual-machines-windows-create-custom.md

[イメージとして使用する Windows 仮想マシンのキャプチャ方法]: virtual-machines-capture-image-windows-server.md

[Windows Server VHD の作成と Azure へのアップロード]: virtual-machines-create-upload-vhd-windows-server.md


[Visual Studio による、 Web サイト用の仮想マシンの作成]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Compute、ネットワーク、およびストレージの .NET ライブラリを使用した Azure リソースのデプロイ]: virtual-machines-arm-deployment.md

[仮想マシンへのサインイン]: virtual-machines-log-on-windows-server.md

[データ ディスクの接続]: storage-windows-attach-disk.md

[基本構成テスト環境]: virtual-machines-base-configuration-test-environment.md

[Azure ハイブリッド クラウド テスト環境]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=Sept15_HO3-->