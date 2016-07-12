<properties
	pageTitle="Linux VM を作成するさまざまな方法 | Microsoft Azure"
	description="Azure で Linux 仮想マシンを作成するさまざまな方法のほか、各方法のツールとチュートリアルへのリンクを紹介します。"
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
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# リソース マネージャーで Linux 仮想マシンを作成する各種の方法

多様なユーザーや目的に対応するために、Azure には、Resource Manager デプロイ モデルを使用して VM を作成するためのさまざまな方法が用意されています。この記事は、それらの違いと、Linux 仮想マシン (VM) を作成する場合の選択肢をまとめたものです。

## Azure CLI 

Azure CLI はさまざまなプラットフォームで利用可能です。その際は、npm パッケージ、ディストリビューション提供のパッケージ、Docker コンテナーのいずれかを使用します。詳細については、[Azure CLI のインストールと構成の方法](../xplat-cli-install.md)に関する記事を参照してください。Azure CLI の使用例については、次のチュートリアルを参照してください。次に示す CLI のクイック スタート コマンドの詳細については、各記事を参照してください。

* [開発用とテスト用の Linux VM を Azure CLI から作成する](virtual-machines-linux-quick-create-cli.md)

	```bash
	azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
	```

* [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

* [Azure CLI を使用して新しく Linux VM を作成する](virtual-machines-linux-create-cli-complete.md)

* [Linux VM へのディスクの追加](virtual-machines-linux-add-disk.md)

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
	```

## Azure ポータル

[Azure ポータル](https://portal.azure.com)のグラフィカル ユーザー インターフェイスを使用すると VM を手軽に試すことができます。ご利用のシステムに何もインストールする必要がないので、特に Azure を初めて使用する場合には便利な方法です。Azure ポータルを使用して VM を作成する方法については、以下のページを参照してください。

* [Azure ポータルを使用して Linux VM を作成する](virtual-machines-linux-quick-create-portal.md)
* [Azure ポータルを使用してディスクを接続する](virtual-machines-linux-attach-disk-portal.md)

## オペレーティング システムとイメージの選択肢
VM を作成するときは、実行するオペレーティング システムに基づいてイメージを選択します。Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールがプレインストールされているイメージもあります。または、独自のイメージのいずれかをアップロードすることができます (次を参照)。

### Azure のイメージ
`azure vm image` CLI コマンドを使用して、発行元、ディストリビューション リリース、ビルドごとに利用可能な内容を確認できます。

利用可能な発行元を一覧表示する場合:

```bash
azure vm image list-publishers --location WestUS
```

特定の発行元の利用可能な製品 (プラン) を一覧表示する場合:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

特定のプランの利用可能な SKU (ディストリビューション リリース) を一覧表示する場合:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

特定のリリースの利用可能なすべてのイメージを一覧表示する場合:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

提供されているイメージの探し方と使い方の例については、[Azure CLI を使用した Azure 仮想マシン イメージの検索と選択](virtual-machines-linux-cli-ps-findimage.md)に関するページを参照してください。

`azure vm quick-create` コマンドと `azure vm create` コマンドにはエイリアスもいくつかあり、より一般的なディストリビューションと、その最新リリースにすばやくアクセスするために使用できます。VM を作成するたびに発行元、プラン、SKU、バージョンを指定する必要がある方法よりも簡単です。

| エイリアス | 発行元 | プラン | SKU | バージョン |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7\.2 | 最新 |
| CoreOS | CoreOS | CoreOS | 安定版 | 最新 |
| Debian | credativ | Debian | 8 | 最新 |
| openSUSE | SUSE | openSUSE | 13\.2 | 最新 |
| RHEL | Redhat | RHEL | 7\.2 | 最新 |
| SLES | SLES | SLES | 12-SP1 | 最新 |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | 最新 |

### 独自のイメージを使用する

特定のカスタマイズが必要な場合は、既存の Azure VM を*キャプチャ*してそれをベースにしたイメージを使用するか、仮想ハード ディスク (VHD) に保存してある独自のイメージをアップロードすることができます。サポートされているディストリビューションと独自のイメージの使用方法について詳しくは、次の記事を参照してください。

* [Azure での動作保証済みディストリビューション](virtual-machines-linux-endorsed-distros.md)

* [動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md)

* [Resource Manager テンプレートとして Linux 仮想マシンをキャプチャする方法](virtual-machines-linux-capture-image.md)クイック スタート コマンドは次のとおりです。

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## 次のステップ

* いずれかのチュートリアルを読んで、[ポータル](virtual-machines-linux-quick-create-portal.md)、[CLI](virtual-machines-linux-quick-create-cli.md)、Azure Resource Manager [テンプレート](virtual-machines-linux-cli-deploy-templates.md)のいずれかの方法で Linux VM を作成してみましょう。

* Linux VM の作成後、簡単に[データ ディスクを追加](virtual-machines-linux-add-disk.md)できます。

* [パスワードや SSH キーをリセットしたり、ユーザーを管理したりする](virtual-machines-linux-using-vmaccess-extension.md)ための手順を参照します。

<!---HONumber=AcomDC_0706_2016-->