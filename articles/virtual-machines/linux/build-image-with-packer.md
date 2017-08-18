---
title: "Packer を使用して Linux Azure VM のイメージを作成する方法 | Microsoft Docs"
description: "Packer を使用して Azure に Linux 仮想マシンのイメージを作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fa30f7b9aebf3b9a3fb1e037983e8460aa76442e
ms.contentlocale: ja-jp
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Packer を使用して Azure に Linux 仮想マシンのイメージを作成する方法
Azure の各仮想マシン (VM) は、Linux ディストリビューションと OS のバージョンを定義するイメージから作成されます。 イメージには、プリインストールされているアプリケーションと構成を含めることができます。 Azure Marketplace には、ほとんどのディストリビューションおよびアプリケーション環境用の自社製およびサード パーティ製のイメージが数多く用意されています。また、ニーズに合わせて独自のイメージを作成することもできます。 この記事では、オープン ソース ツール [Packer](https://www.packer.io/) を使用して Azure に独自のイメージを定義およびビルドする方法について、詳しく説明します。


## <a name="create-supporting-azure-resources"></a>関連する Azure リソースを作成する
ビルド プロセス中、Packer はソース VM をビルドする際に一時的な Azure リソースを作成します。 イメージとして使用するためにそのソース VM をキャプチャするには、リソース グループおよびストレージ アカウントを定義する必要があります。 Packer のビルド プロセスからの出力は、このリソース グループおよびストレージ アカウントに格納されます。

最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create -n myResourceGroup -l eastus
```

次に、[az storage account create](/cli/azure/storage/account#create) でストレージ アカウントを作成します。 ストレージ アカウント名は一意である必要があり、長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 次の例では、*mystorageaccount* という名前のストレージ アカウントを作成します。

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --sku Standard_LRS
```


## <a name="create-azure-credentials"></a>Azure 資格情報を作成する
Packer はサービス プリンシパルを使用して Azure で認証されます。 Azure のサービス プリンシパルは、アプリケーション、サービス、および Packer などのオートメーション ツールで使用できるセキュリティ ID です。 Azure で実行できる操作やサービス プリンシパルを設定するアクセス許可をコントロールおよび定義します。

[az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) でサービス プリンシパルを作成し、Packer が必要とする資格情報を出力します。

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

上記のコマンドの出力例は次のとおりです。

```azurecli
"f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
"0e760437-bf34-4aad-9f8d-870be799c55d",
"72f988bf-86f1-41af-91ab-2d7cd011db47"
```

Azure に対して認証するには、[az account show](/cli/azure/account#show) で Azure サブスクリプション ID を取得する必要もあります。

```azurecli
az account show --query [id] --output tsv
```

次の手順でこれら 2 つのコマンドからの出力を使用します。


## <a name="define-packer-template"></a>Packer テンプレートを定義する
イメージをビルドするには、テンプレートを JSON ファイルとして作成します。 テンプレートでは、実際のビルド プロセスを実行するビルダーとプロビジョナーを定義します。 Packer には [Azure 用のプロビジョナー](https://www.packer.io/docs/builders/azure.html)が用意されており、前の手順で作成したサービス プリンシパルの資格情報などの Azure リソースを定義できます。

*ubuntu.json* というファイルを作成し、次のコンテンツを貼り付けます。 次に対応する実際の値を入力します。

| パラメーター       | 入手場所 |
|-----------------|----------------------------------------------------|
| *client_id*      | `az ad sp` 作成コマンドの出力の最初の行 - *appId* |
| *client_secret*  | `az ad sp` 作成コマンドの出力の 2 つ目の行 - *password* |
| *tenant_id*      | `az ad sp` 作成コマンドの出力の 3 つ目の行 - *tenant* |
| *subscription_id* | `az account show` コマンドからの出力 |
| *storage_account* | `az storage account create` で指定した名前 |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04.0-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

このテンプレートでは、Ubuntu 16.04 LTS イメージをビルドし、NGINX をインストールした後、VM をプロビジョニング解除します。

> [!NOTE]
> このテンプレートでユーザーの資格情報を展開する場合は、Azure エージェントをプロビジョニング解除するプロビジョナー コマンドを `deprovision+user` ではなく `-deprovision` を読み取るように調整します。
> `+user` フラグは、ソース VM からすべてのユーザー アカウントを削除します。


## <a name="build-packer-image"></a>Packer イメージをビルドする
まだローカル コンピューターに Packer がインストールされていない場合は、[手順に従って Packer をインストールしてください](https://www.packer.io/docs/install/index.html)。

次のように Packer テンプレート ファイルを指定してイメージをビルドします。

```bash
./packer build ubuntu.json
```

上記のコマンドの出力例は次のとおりです。

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : engineering
==> azure-arm:  ->> task : image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '13.90.250.248'
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /tmp/packer-shell529418469
    azure-arm: Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
    azure-arm: Hit:2 http://azure.archive.ubuntu.com/ubuntu xenial InRelease
    azure-arm: Get:3 http://azure.archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
    azure-arm: Get:4 http://azure.archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
    [snip]
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd?se=2017-07-08T20%3A57%3A53Z&sig=yl1yl3I2gKnO0I%2B7paw%2FQzKT5dawf5i%2B
LPmATMt5ot4%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json?se=2017-07-08T20%3A57%3A53Z&sig=GB1iSl0hhw1ZYG4nl%2BCfR9WEaquCF
OEhNtKlvp%2B5TdE%3D&sp=r&sr=b&sv=2015-02-21
```


## <a name="create-azure-image"></a>Azure イメージを作成する
Packer ビルド プロセスからの出力は、指定したストレージ アカウントの仮想ハード ディスク (VHD) です。 [az image create](/cli/azure/image#create) を使用してこの VHD から Azure イメージを作成し、Packer ビルド出力の最後に記された `OSDiskUri` パスを指定します。 次の例では、`myImage` という名前のイメージを作成します。

```azurecli
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --os-type linux \
    --source https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
```

このイメージを使用して、Azure サブスクリプション全体に VM を作成できます。 VM を作成するリソース グループは、ソース イメージと同じリソース グループに限定されません。


## <a name="create-vm-from-azure-image"></a>Azure イメージから VM を作成する
[az vm create](/cli/azure/vm#create) を使用して、イメージから VM を作成できるようになりました。 `--image` パラメーターで作成したイメージを指定します。 次の例では、*myImage* から *myVM* という名前の VM を作成し、SSH キーを生成します (まだ存在していない場合)。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM の作成には数分かかります。 VM が作成されたら、Azure CLI によって表示される `publicIpAddress` をメモしてください。 このアドレスは、Web ブラウザーから NGINX サイトにアクセスするために使用します。

Web トラフィックが VM にアクセスできるようにするには、[az vm open-port](/cli/azure/vm#open-port) を使用してインターネットからポート 80 を開きます。

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>VM と NGINX をテストする
Web ブラウザーを開き、アドレス バーに「`http://publicIpAddress`」と入力できるようになりました。 VM 作成処理で取得した独自のパブリック IP アドレスを指定します。 既定の NGINX ページは次の例のように表示されます。

![NGINX の既定のサイト](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>次のステップ
この例では、Packer を使用して NGINX が既にインストールされた VM イメージを作成しました。 この VM イメージは、アプリを Ansible、Chef、Puppet でイメージから作成した VM にデプロイするなど、既存のデプロイ ワークフローとともに使用できます。

他の Linux ディストリビューション用の追加の Packer テンプレートの例については、[この GitHub リポジトリ](https://github.com/hashicorp/packer/tree/master/examples/azure)をご覧ください。
