<properties
	pageTitle="Linux VM を作成するさまざまな方法 | Microsoft Azure"
	description="Azure で Linux 仮想マシンを作成するさまざまな方法を紹介し、手順へのリンクを提供します。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/20/2016"
	ms.author="dkshir"/>

# Linux 仮想マシンを作成するさまざまな方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

多様なユーザーや目的に対応するために、Azure には VM を作成するためのさまざまな方法が用意されています。この記事は、それらの違いと、Linux 仮想マシンを作成する場合の選択肢をまとめたものです。

仮想マシンとその各種リソースを 1 つの論理的なデプロイ単位として作成および管理する手段として、Azure リソース マネージャー テンプレートを使用することもできます。そのアプローチに関する手順は、該当する箇所に記載されています。Azure リソース マネージャー、およびリソースを 1 つの単位として管理する方法の詳細については、[概要][]に関するページを参照してください。

## ツールの選択肢

### GUI: Azure クラシック ポータルまたは Azure ポータル

ポータルのグラフィカル ユーザー インターフェイスを使用すれば、特に Azure を初めて使用する場合に、仮想マシンを簡単に試すことができます。VM の作成は、[Azure クラシック ポータル](https://manage.windowsazure.com)と [Azure ポータル](https://portal.azure.com)のいずれかを使用して行います。一般的な手順については、「[カスタム仮想マシンの作成][]」をご覧ください。その後、**[ギャラリー]** から Linux イメージを選びます。[Azure クラシック ポータル](https://manage.windowsazure.com)は、クラシック デプロイ モデルのみを使用して仮想マシンを作成することにご注意ください。

### コマンド シェル: Azure CLI または Azure PowerShell

コマンド シェルを使用する場合は、Mac、Linux、および Windows ユーザー向けの Azure コマンド ライン インターフェイス (CLI) と、Azure 用 Windows PowerShell コマンドレットおよびカスタム コンソールを備えた Azure PowerShell のいずれかを選択します。

Azure CLI を使用してリソース マネージャー デプロイ モデルの仮想マシンを作成するには、「[Linux 仮想マシンの作成][]」を参照してください。Azure PowerShell およびテンプレートの使用手順を確認するには、この記事のタブまたは記事セレクターをご利用ください。

クラシック デプロイ モデルについては、「[カスタム Linux VM を作成する方法](virtual-machines-linux-create-custom.md)」と「[Azure PowerShell を使用して Linux ベースの仮想マシンを作成と事前構成する][]」を参照してください。


### 開発環境: Visual Studio

Visual Studio も、Azure 仮想マシンの作成をサポートしています。クラシック デプロイ モデルについては、「[Visual Studio での Web アプリケーション用仮想マシンの作成][]」をお読みください。リソース マネージャー デプロイ モデルについては、「[Compute、Network、および Storage .NET ライブラリを使用した Azure リソースのデプロイ][]」をお読みください。


## オペレーティング システムとイメージの選択肢

実行するオペレーティング システムに基づいてイメージを選択します。Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールが含まれているイメージもあります。また、独自のイメージを使用することもできます。


### Azure のイメージ

前述のすべての記事では、簡単に、既存の Azure イメージを使用し、仮想マシンを作成して、それをネットワークや負荷分散などに合わせてカスタマイズすることができます。各ポータルには、Azure で提供されるイメージのギャラリーやイメージの一覧があります。コマンド ラインを使用して、同様の一覧を取得することができます。たとえば、場所と発行元ごとの利用可能なすべてのイメージの一覧を取得するには、Azure CLI で `azure vm image list` を実行します。


### 独自のイメージを使用する

既存の Azure 仮想マシンを*キャプチャ*してその VM をベースにしたイメージを使用するか、独自のイメージを仮想ハード ディスク (VHD) に格納してアップロードします。クラシック デプロイ モデルについては、「[テンプレートとして使用するために CLI を使って Linux 仮想マシンをキャプチャする方法][]」と「[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード][]」を参照してください。リソース マネージャー デプロイ モデルについては、「[リソース マネージャーのテンプレートとして使用する Linux 仮想マシンをキャプチャする方法](virtual-machines-linux-capture-image-resource-manager.md)」を参照してください。

## 次のステップ

[仮想マシンへのログオン][]

[データ ディスクの接続][]

## その他のリソース

[基本構成テスト環境][]

[Azure ハイブリッド クラウド テスト環境][]

[Mac、Linux、および Windows 用 Azure CLI での VM 操作に使用するリソース マネージャーおよびサービス管理コマンドの対応][]

<!-- LINKS -->
[概要]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Mac、Linux、および Windows 用 Azure CLI での VM 操作に使用するリソース マネージャーおよびサービス管理コマンドの対応]: xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Azure PowerShell を使用して Linux ベースの仮想マシンを作成と事前構成する]: virtual-machines-ps-create-preconfigure-linux-vms.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-create-custom.md
[テンプレートとして使用するために CLI を使って Linux 仮想マシンをキャプチャする方法]: virtual-machines-linux-capture-image.md

[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード]: virtual-machines-linux-create-upload-vhd.md

[Visual Studio での Web アプリケーション用仮想マシンの作成]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Compute、Network、および Storage .NET ライブラリを使用した Azure リソースのデプロイ]: virtual-machines-arm-deployment.md

[仮想マシンへのログオン]: virtual-machines-linux-how-to-log-on.md

[データ ディスクの接続]: virtual-machines-linux-how-to-attach-disk.md

[基本構成テスト環境]: virtual-machines-base-configuration-test-environment.md
[Azure ハイブリッド クラウド テスト環境]: virtual-machines-hybrid-cloud-test-environments.md

[Linux 仮想マシンの作成]: virtual-machines-linux-tutorial.md
[カスタム仮想マシンの作成]: virtual-machines-create-custom.md

<!---HONumber=AcomDC_0128_2016-->