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

多様なユーザーや目的に対応するために、Azure には、Resource Manager デプロイ モデルを使用して VM を作成するためのさまざまな方法が用意されています。この記事は、それらの違いと、Linux 仮想マシン (VM) を作成する場合の選択肢をまとめたものです。

## ツールの選択肢

### コマンド シェル: Azure CLI 

CLI から、Azure コマンド ライン インターフェイスを使用します。[こちら](../xplat-cli-install.md)のページで、npm、Docker コンテナー、インストール スクリプトを使用して Azure CLI をインストールする方法について詳しく説明しています。Azure CLI の使用例については、次のチュートリアルを参照してください。

* [開発用とテスト用の Linux VM を Azure CLI から作成する](virtual-machines-linux-quick-create-cli.md) 

* [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [Azure CLI を使用して新しく Linux VM を作成する](virtual-machines-linux-create-cli-complete.md)

### GUI: Azure ポータル

[Azure ポータル](https://portal.azure.com)のグラフィカル ユーザー インターフェイスを使用すると VM を手軽に試すことができます。ご利用のシステムには何もインストールする必要がないので、特に Azure を初めて使用する場合には有利な方法です。Azure ポータルを使用して VM を作成する方法については、以下のページを参照してください。

* [Linux を実行する仮想マシンを Azure ポータルを使用して作成する](virtual-machines-linux-quick-create-portal.md) 

## オペレーティング システムとイメージの選択肢

どちらの方法も、実行するオペレーティング システムに基づいてイメージを選択します。Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールがプレインストールされているイメージもあります。または、独自のイメージのいずれかをアップロードすることができます。

### Azure のイメージ

前述のすべての記事では、簡単に、既存の Azure イメージを使用し、VM を作成して、それをネットワークや負荷分散などに合わせてカスタマイズすることができます。Azure で提供されているイメージは、ポータルから Azure Marketplace にアクセスすることで入手できます。コマンド ラインを使用して、同様の一覧を取得することができます。たとえば、場所と発行元ごとの利用可能なすべてのイメージの一覧を取得するには、Azure CLI で `azure vm image list` を実行します。提供されているイメージの探し方と使い方の例については、[Azure CLI を使用した Azure 仮想マシン イメージの検索と選択](virtual-machines-linux-cli-ps-findimage.md)に関するページを参照してください。

### 独自のイメージを使用する

特定のカスタマイズが必要な場合は、既存の Azure VM を*キャプチャ*してそれをベースにしたイメージを使用するか、仮想ハード ディスク (VHD) に保存してある独自のイメージをアップロードすることができます。サポートされているディストリビューションと独自のイメージの使用方法について詳しくは、次の記事を参照してください。

* [Azure での動作保証済みディストリビューション](virtual-machines-linux-endorsed-distros.md)

* [動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md)

* [Resource Manager テンプレートとして使用する Linux 仮想マシンをキャプチャする方法](virtual-machines-linux-capture-image.md)

## 次のステップ

* いずれかのチュートリアルを読んで、[ポータル](virtual-machines-linux-quick-create-portal.md)、[CLI](virtual-machines-linux-quick-create-cli.md)、Azure Resource Manager [テンプレート](virtual-machines-linux-cli-deploy-templates.md)のいずれかの方法で Linux VM を作成してみましょう。

* Linux VM の作成後、簡単に[データ ディスクを追加](virtual-machines-linux-add-disk.md)できます。

* [パスワードや SSH キーをリセットしたりユーザーを管理したりするための手順](virtual-machines-linux-using-vmaccess-extension.md)を参照します。

<!---HONumber=AcomDC_0608_2016-->