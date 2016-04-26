<properties
	pageTitle="Linux VM を作成するさまざまな方法 | Microsoft Azure"
	description="Azure で Linux 仮想マシンを作成するさまざまな方法を紹介し、手順へのリンクを提供します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# リソース マネージャーで Linux 仮想マシンを作成する各種の方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

多様なユーザーや目的に対応するために、Azure には VM を作成するためのさまざまな方法が用意されています。この記事は、それらの違いと、Linux 仮想マシンを作成する場合の選択肢をまとめたものです。

仮想マシンとその各種リソースを 1 つの論理的なデプロイ単位として作成および管理する手段として、Azure Resource Manager テンプレートを使用することもできます。Azure リソース マネージャー、およびリソースを 1 つの単位として管理する方法の詳細については、[概要](../resource-group-overview.md)に関するページを参照してください。

## ツールの選択肢

### コマンド シェル: Azure CLI 

CLI から、Azure コマンド ライン インターフェイスを使用します。Azure CLI の使用については以下のチュートリアルを参照してください。

* [Create a Linux VM from the CLI for dev and test (開発用とテスト用の Linux VM を CLI から作成する)](virtual-machines-linux-quick-create-cli.md) 

* [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

### GUI: Azure ポータル

[Azure ポータル](https://portal.azure.com)のグラフィカル ユーザー インターフェイスを使用すれば、特に Azure を初めて使用する場合に、仮想マシンを簡単に試すことができます。Azure ポータルを使用して VM を作成する方法については、以下のページを参照してください。

* [Linux を実行する仮想マシンを Azure ポータルを使用して作成する](virtual-machines-linux-portal-create.md) 

## オペレーティング システムとイメージの選択肢

実行するオペレーティング システムに基づいてイメージを選択します。Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールが含まれているイメージもあります。また、独自のイメージを使用することもできます。

### Azure のイメージ

前述のすべての記事では、簡単に、既存の Azure イメージを使用し、仮想マシンを作成して、それをネットワークや負荷分散などに合わせてカスタマイズすることができます。Azure で提供されているイメージは、ポータルから Azure Marketplace にアクセスすることで入手できます。コマンド ラインを使用して、同様の一覧を取得することができます。たとえば、場所と発行元ごとの利用可能なすべてのイメージの一覧を取得するには、Azure CLI で `azure vm image list` を実行します。「[Azure CLI を使用した Azure 仮想マシン イメージへの移動と選択](virtual-machines-linux-cli-ps-findimage.md)」を参照してください。

### 独自のイメージを使用する

既存の Azure 仮想マシンを*キャプチャ*してその VM をベースにしたイメージを使用するか、独自のイメージを仮想ハード ディスク (VHD) に格納してアップロードします。詳細については、次を参照してください。

* [Azure での動作保証済みディストリビューション](virtual-machines-linux-endorsed-distros.md)

* [動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md)

* [リソース マネージャーのテンプレートとして使用する Linux 仮想マシンをキャプチャする方法](virtual-machines-linux-capture-image.md)

## 次のステップ

* いずれかのチュートリアルを読んで、[ポータル](virtual-machines-linux-portal-create.md)、[CLI](virtual-machines-linux-quick-create-cli.md)、Azure Resource Manager [テンプレート](virtual-machines-linux-cli-deploy-templates.md)のいずれかの方法で Linux VM を作成してみましょう。

* Linux VM の作成後、簡単に[データ ディスクを追加](virtual-machines-linux-add-disk.md)できます。

<!---HONumber=AcomDC_0420_2016-->