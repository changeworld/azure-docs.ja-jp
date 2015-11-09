<properties
	pageTitle="Windows VM を作成するさまざまな方法 | Microsoft Azure"
	description="リソース マネージャーで Windows 仮想マシンを作成するさまざまな方法を紹介します。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="10/22/2015"
	ms.author="cynthn"/>

# リソース マネージャーで Windows 仮想マシンを作成するさまざまな方法

仮想マシンは多様なユーザーや目的に適しているため、Azure には仮想マシンを作成するためのさまざまな方法が用意されています。これは、仮想マシンとその作成方法について、いくつかの選択を行う必要があることを意味します。この記事には、これらの選択肢の概要と、手順へのリンクが記載されています。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

仮想マシンとその各種リソースを 1 つの論理的なデプロイメント単位として作成および管理する手段として、Azure リソース マネージャー テンプレートが最近導入されました。このアプローチに関する手順が、以下で該当する箇所に記載されています。Azure リソース マネージャー、およびリソースを 1 つの単位として管理する方法の詳細については、[概要][]に関するページを参照してください。

## ツールの選択肢

### GUI: Azure プレビュー ポータル

Azure ポータルのグラフィカル ユーザー インターフェイスを使用すれば、特に Azure を初めて使用する場合に、仮想マシンを簡単に試すことができます。Azure プレビュー ポータルを使用して、仮想マシンを作成します。

[Windows を実行する仮想マシンの作成][]

### コマンド シェル: Azure CLI または Azure PowerShell

コマンド シェルを使用する場合は、Mac および Linux ユーザー向けの Azure コマンド ライン インターフェイス (CLI) と、Azure 用コマンドレットとカスタム コンソールを備えた Azure PowerShell のいずれかを選択します。

Azure CLI については、以下をご覧ください。

- [Mac、Linux、Windows 用 Azure CLI で VM タスクに使用するリソース マネージャーとサービス管理の同等のコマンド][]
- [Azure リソース マネージャー テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理][]

Azure PowerShell については、以下をご覧ください。

- [リソース マネージャーと PowerShell で Windows VM を作成する][]
- [リソース マネージャーと Azure PowerShell を使用して、Windows 仮想マシンを作成し、構成する][]
- [Azure リソース マネージャー テンプレートと PowerShell を使用した仮想マシンのデプロイと管理][]
- [リソース マネージャー テンプレートと PowerShell で Windows 仮想マシンを作成する][]

### 開発環境: Visual Studio

[Compute、Network、および Storage の .NET ライブラリを使用した Azure リソースのデプロイ][]

## オペレーティング システムとイメージの選択肢

実行するオペレーティング システムに基づいてイメージを選択します。Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールが含まれているイメージもあります。また、独自のイメージを使用することもできます。「[Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択][]」の情報を参考にして、アプリケーションに必要なプラットフォーム イメージを見つけます。

<!-- LINKS -->
[概要]: ../resource-group-overview.md

[Windows を実行する仮想マシンの作成]: virtual-machines-windows-tutorial.md

[Mac、Linux、Windows 用 Azure CLI で VM タスクに使用するリソース マネージャーとサービス管理の同等のコマンド]: xplat-cli-azure-manage-vm-asm-arm.md
[Azure リソース マネージャー テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理]: virtual-machines-deploy-rmtemplates-azure-cli.md
[リソース マネージャーと Azure PowerShell を使用して、Windows 仮想マシンを作成し、構成する]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Azure リソース マネージャー テンプレートと PowerShell を使用した仮想マシンのデプロイと管理]: virtual-machines-deploy-rmtemplates-powershell.md
[リソース マネージャーと PowerShell で Windows VM を作成する]: virtual-machines-create-windows-powershell-resource-manager.md
[リソース マネージャー テンプレートと PowerShell で Windows 仮想マシンを作成する]: virtual-machines-create-windows-powershell-resource-manager-template-simple.md


[Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択]: resource-groups-vm-searching.md
[Compute、Network、および Storage の .NET ライブラリを使用した Azure リソースのデプロイ]: virtual-machines-arm-deployment.md

[Sign in to the virtual machine]: virtual-machines-log-on-windows-server.md

[Base configuration test environment]: virtual-machines-base-configuration-test-environment.md

[Azure hybrid cloud test environments]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=Nov15_HO1-->