---
title: "Azure で Linux VM を作成するさまざまな方法 | Microsoft Azure"
description: "Azure で Linux 仮想マシンを作成するさまざまな方法のほか、各方法のツールとチュートリアルへのリンクを紹介します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 44c46fff9ccf9c7dba9ee380faf5f8213b58e3c3
ms.openlocfilehash: 4397d84ef4d97bdee387777a193ec0b969f2d5e1


---
# <a name="different-ways-to-create-a-linux-vm-including-azure-cli-20-preview"></a>Azure CLI 2.0 (プレビュー) など、Linux VM を作成するさまざまな方法
Azure では、使いやすいツールとワークフローを使用して Linux 仮想マシン (VM) を柔軟に作成できます。 この記事は、それらの違いと、Linux VM を作成する例をまとめたものです。

## <a name="azure-cli"></a>Azure CLI

次のいずれかの CLI バージョンを使用してタスクを完了できます。

- Azure CLI 1.0 - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI
- [Azure CLI 2.0 (プレビュー)](../xplat-cli-install.md) - Resource Manager デプロイメント モデル用の次世代 CLI

Azure CLI 2.0 (プレビュー) はさまざまなプラットフォームで利用できます。その際は、npm パッケージ、ディストリビューション提供のパッケージ、Docker コンテナーのいずれかを使用します。 必ず **az login** を使用してログインしてください。

Azure CLI 2.0 (プレビュー) の使用例については、次のチュートリアルを参照してください。 次に示すコマンドの詳細については、各記事を参照してください。

* [Azure CLI 2.0 (プレビュー) を使用して Linux VM を作成する](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 次の例では、myResourceGroup という名前のリソース グループを作成します。 
    
    ```azurecli
    az group create -n myResourceGroup -l westus
    ```

  * 次の例では、最新の Debian イメージと `id_rsa.pub` という名前の公開キーを使用して、新しいリソース グループに VM を作成します。

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name mydns \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

* [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 次の例では、GitHub に格納されているテンプレートを使用して VM を作成します。
    
    ```azurecli
    az group deployment create -g myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Azure CLI を使用して、完全な Linux 環境を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 可用性セット内のロード バランサーと複数の VM の作成が含まれています。

* [Linux VM へのディスクの追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 次の例では、 `myVM`という名前の既存の VM に 5 Gb のディスクを追加します。
    
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://myStorage.blob.core.windows.net/vhds/myDataDisk1.vhd
    ```

## <a name="azure-portal"></a>Azure ポータル
[Azure Portal](https://portal.azure.com) を使用すると、システムに何もインストールする必要がないため、VM をすばやく作成できます。 Azure ポータルを使用して VM を作成する方法については、以下のページを参照してください。

* [Azure ポータルを使用して Linux VM を作成する](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Azure ポータルを使用してディスクを接続する](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>オペレーティング システムとイメージの選択肢
VM を作成するときは、実行するオペレーティング システムに基づいてイメージを選択します。 Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールがプレインストールされているイメージもあります。 または、独自のイメージのいずれかをアップロードすることができます ( [次のセクションで](#use-your-own-image)を参照してください)。

### <a name="azure-images"></a>Azure のイメージ
`az vm image` CLI コマンドを使用して、発行元、ディストリビューション リリース、ビルドごとに利用可能な内容を確認します。

利用可能な発行元を一覧表示する場合:

```azurecli
az vm image list-publishers -l WestUS
```

特定の発行元の利用可能な製品 (プラン) を一覧表示する場合:

```azurecli
az vm image list-offers --publisher-name Canonical -l WestUS
```

特定のプランの利用可能な SKU (ディストリビューション リリース) を一覧表示する場合:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer -l WestUS
```

特定のリリースの利用可能なすべてのイメージを一覧表示する場合:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS -l WestUS
```

提供されているイメージの探し方と使い方の例については、 [Azure CLI を使用した Azure 仮想マシン イメージの検索と選択](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。

`az vm create` コマンドにはエイリアスがあり、より一般的なディストリビューションとその最新リリースにすばやくアクセスするために使用できます。 一般的に、エイリアスを使う方が、VM を作成するたびに発行元、プラン、SKU、バージョンを指定するよりも簡単です。

| エイリアス | 発行元 | プラン | SKU | バージョン |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |最新 |
| CoreOS |CoreOS |CoreOS |安定版 |最新 |
| Debian |credativ |Debian |8 |最新 |
| openSUSE |SUSE |openSUSE |13.2 |最新 |
| RHEL |Redhat |RHEL |7.2 |最新 |
| SLES |SLES |SLES |12-SP1 |最新 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |最新 |

### <a name="use-your-own-image"></a>独自のイメージを使用する
特定のカスタマイズが必要な場合は、既存の Azure VM を *キャプチャ* してそれをベースにしたイメージを使用できます。 また、オンプレミスで作成したイメージをアップロードすることもできます。 サポートされているディストリビューションと独自のイメージの使用方法について詳しくは、次の記事を参照してください。

* [Azure での動作保証済みディストリビューション](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Resource Manager テンプレートとして使用する Linux 仮想マシンをキャプチャする方法](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 既存の VM をキャプチャするクイック スタート コマンドの例:
    
    ```azurecli
    az vm deallocate -g myResourceGroup -n myVM
    az vm generalize -g myResourceGroup -n myVM
    az vm capture -g myResourceGroup -n myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>次のステップ
* [ポータル](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[Azure Resource Manager テンプレート](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のいずれかの方法で Linux VM を作成します。
* Linux VM の作成後、 [データ ディスクを追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)します。
*  [パスワードや SSH キーをリセットしたり、ユーザーを管理したりする](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Jan17_HO1-->


