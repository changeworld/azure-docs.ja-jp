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
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: b2f93579eb1c7a69d0dbd1b0ef112aed9b2168c3
ms.contentlocale: ja-jp
ms.lasthandoff: 05/31/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Linux VM を作成するさまざまな方法
Azure では、使いやすいツールとワークフローを使用して Linux 仮想マシン (VM) を柔軟に作成できます。 この記事は、それらの違いと、Azure CLI 2.0 を含む、Linux VM を作成する例をまとめたものです。 また、[Azure CLI 1.0](creation-choices-nodejs.md) などの作成の選択肢も確認できます。

[Azure CLI 2.0](/cli/azure/install-az-cli2) はさまざまなプラットフォームで利用できます。利用時には、npm パッケージ、ディストリビューション提供のパッケージ、Docker コンテナーのいずれかを使用します。 環境に最適なビルドをインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインします。

* [Azure CLI 2.0 を使用して Linux VM を作成する](quick-create-cli.md)
  
  * [az group create](/cli/azure/group#create) を使用して、*myResourceGroup* という名前のリソース グループを作成します。 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * [az vm create](/cli/azure/vm#create) で、最新の *UbuntuLTS* イメージを使用した *myVM* という名前の VM を作成し、SSH キー (*~/.ssh* にまだ存在しない場合) を生成します。

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [Azure テンプレートを使用して Linux VM を作成する](create-ssh-secured-vm-from-template.md)
  
  * 次の例では、[az group deployment create](/cli/azure/group/deployment#create) を使用し、GitHub に格納されているテンプレートから VM を作成します。
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [Linux VM を作成し、cloud-init でカスタマイズする](tutorial-automate-vm-deployment.md)

* [複数の Linux VM 上に負荷分散された高可用性アプリケーションを作成する](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Azure ポータル
[Azure Portal](https://portal.azure.com) を使用すると、システムに何もインストールする必要がないため、VM をすばやく作成できます。 Azure ポータルを使用して VM を作成する方法については、以下のページを参照してください。

* [Azure ポータルを使用して Linux VM を作成する](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>オペレーティング システムとイメージの選択肢
VM を作成するときは、実行するオペレーティング システムに基づいてイメージを選択します。 Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールがプレインストールされているイメージもあります。 または、独自のイメージのいずれかをアップロードすることができます ( [次のセクションで](#use-your-own-image)を参照してください)。

### <a name="azure-images"></a>Azure のイメージ
[az vm image](/cli/azure/vm/image) コマンドを使用して、発行元、ディストリビューション リリース、ビルドごとに利用可能な内容を確認します。

利用可能な発行元を一覧表示する場合:

```azurecli
az vm image list-publishers --location eastus
```

特定の発行元の利用可能な製品 (プラン) を一覧表示する場合:

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

特定のプランの利用可能な SKU (ディストリビューション リリース) を一覧表示する場合:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

特定のリリースの利用可能なすべてのイメージを一覧表示する場合:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

提供されているイメージの探し方と使い方の例については、 [Azure CLI を使用した Azure 仮想マシン イメージの検索と選択](cli-ps-findimage.md)に関するページを参照してください。

[az vm create](/cli/azure/vm#create) コマンドにはエイリアスがあり、より一般的なディストリビューションとその最新リリースにすばやくアクセスするために使用できます。 一般的に、エイリアスを使う方が、VM を作成するたびに発行元、プラン、SKU、バージョンを指定するよりも簡単です。

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
特定のカスタマイズが必要な場合は、既存の Azure VM をキャプチャしてそれをベースにしたイメージを使用できます。 また、オンプレミスで作成したイメージをアップロードすることもできます。 サポートされているディストリビューションと独自のイメージの使用方法について詳しくは、次の記事を参照してください。

* [Azure での動作保証済みディストリビューション](endorsed-distros.md)
* [動作保証外のディストリビューションに関する情報](create-upload-generic.md)
* [既存の Azure VM からイメージを作成する方法](tutorial-custom-images.md)
  
  * 既存の Azure VM からイメージを作成するクイック スタート コマンドの例:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>次のステップ
* [CLI](quick-create-cli.md)、[ポータル](quick-create-portal.md)、[Azure Resource Manager テンプレート](../windows/cli-deploy-templates.md)のいずれかの方法で Linux VM を作成します。
* Linux VM を作成したら、[Azure ディスクとストレージについて確認します](tutorial-manage-disks.md)。
* [パスワードや SSH キーをリセットしたり、ユーザーを管理したりする](using-vmaccess-extension.md)ための簡単な手順。

