---
title: "Azure クイック スタート - CLI で Windows VM を作成する | Microsoft Docs"
description: "Azure CLI で Windows 仮想マシンを作成する方法を簡単に説明します。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7461a0006e57608d9baa538175174788692db5f5
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>Azure CLI で Windows 仮想マシンを作成する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用して、Windows Server 2016 を実行する仮想マシンをデプロイする方法について詳しく説明します。 デプロイが完了したら、サーバーに接続し、IIS をインストールします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) を作成してください。

また、Azure CLI がインストールされていることを確認してください。 詳細については、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)を参照してください。

## <a name="log-in-to-azure"></a>Azure へのログイン 

[az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *westeurope* の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#create) を使用して VM を作成します。 

次の例では、*myVM* という名前の VM を作成します。 この例では、管理ユーザーの名前に *azureuser*、パスワードに *myPassword12* を使用します。 これらの値を、環境に適した内容に更新します。 これらの値は、仮想マシンとの接続を作成する際に必要です。

```azurecli
az vm create `
  --resource-group myResourceGroup `
  --name myVM --image win2016datacenter `
  --admin-username azureuser `
  --admin-password myPassword12
```

VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 `publicIpAaddress` を書き留めておきます。 このアドレスは、VM へのアクセスに使用されます。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く 

Azure にデプロイされている Windows 仮想マシンに対しては、既定で RDP 接続のみが許可されます。 この VM を Web サーバーとして使用する場合は、インターネットからポート 80 を開く必要があります。 [az vm open-port](/cli/azure/vm#open-port) コマンドを使用して、目的のポートを開きます。  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```


## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

次のコマンドを使用して、仮想マシンとのリモート デスクトップ セッションを作成します。 IP アドレスを仮想マシンのパブリック IP アドレスに置き換えます。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。

```bash 
mstsc /v:<Public IP Address>
```

## <a name="install-iis-using-powershell"></a>PowerShell を使用した IIS のインストール

Azure VM にログインしたら、1 行の PowerShell を使用して、IIS をインストールし、Web トラフィックを許可するローカル ファイアウォール規則を有効にできます。 PowerShell プロンプトを開き、次のコマンドを実行します。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS のようこそページの表示

IIS をインストールし、VM のポート 80 をインターネットから開いたら、任意の Web ブラウザーを使用して IIS の既定のようこそページを表示することができます。 上の手順で指定したパブリック IP アドレスを使用して既定のページにアクセスします。 

![IIS の既定のサイト](./media/quick-create-powershell/default-iis-website.png) 
## <a name="delete-virtual-machine"></a>仮想マシンの削除

必要がなくなったら、[az group delete](/cli/azure/group#delete) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

[ロールのインストールとファイアウォールの構成のチュートリアル](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[VM デプロイ CLI サンプルを探索する](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
