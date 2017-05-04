---
title: "Azure クイック スタート - CLI で VM を作成する | Microsoft Docs"
description: "Azure CLI で仮想マシンを作成する方法を簡単に説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: b26d3952adc4d0bb1993bb46cabf6c7d0850666a
ms.lasthandoff: 04/21/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Azure CLI で Linux 仮想マシンを作成する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用して、Ubuntu 16.04 LTS を実行する仮想マシンをデプロイする方法について詳しく説明します。 サーバーがデプロイされたら、NGINX をインストールするために SSH を使用して VM に接続します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) を作成してください。

また、Azure CLI がインストールされていることを確認してください。 詳細については、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)を参照してください。 

## <a name="log-in-to-azure"></a>Azure へのログイン 

[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#create) コマンドで VM を作成します。 

次の例では、`myVM` という名前の VM を作成し、既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 `publicIpAddress` を書き留めておきます。 このアドレスは、VM へのアクセスに使用されます。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く 

Azure にデプロイされている Linux 仮想マシンに対しては、既定で SSH 接続のみが許可されます。 この VM を Web サーバーとして使用する場合は、インターネットからポート 80 を開く必要があります。  目的のポートを開くには、1 つのコマンドを実行するだけで済みます。  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>VM への SSH 接続

次のコマンドを使用して、仮想マシンとの SSH セッションを作成します。 `<publicIpAddress>` を仮想マシンの正しいパブリック IP アドレスに置き換えます。  上記の例の IP アドレスは `40.68.254.142` でした。

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>NGINX のインストール

次の bash スクリプトを使用して、パッケージのソースを更新し、最新の NGINX パッケージをインストールします。 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>NGIX のようこそページの表示

NGINX をインストールし、VM のポート 80 をインターネットから開いたら、任意の Web ブラウザーを使用して NGINX の既定のようこそページを表示することができます。 上の手順で指定した `publicIpAddress` を使用して既定のページにアクセスします。 

![NGINX の既定のサイト](./media/quick-create-cli/nginx.png) 


## <a name="delete-virtual-machine"></a>仮想マシンの削除

必要がなくなったら、次のコマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

[可用性が高い仮想マシンの作成のチュートリアル](create-cli-complete.md)

[VM デプロイ CLI サンプルを探索する](cli-samples.md)

