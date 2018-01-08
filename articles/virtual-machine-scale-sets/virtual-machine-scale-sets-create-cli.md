---
title: "Azure CLI 2.0 を使用して仮想マシン スケール セットを作成する | Microsoft Docs"
description: "Azure PowerShell を使用して仮想マシン スケール セットをすばやく作成する方法を説明します"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 222bfa1c3070fa4634cf5c48d934a6387c48a4b0
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Azure CLI 2.0 を使用して仮想マシン スケール セットを作成する
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケーリングするルールを定義したりできます。 この入門記事では、Azure CLI 2.0 を使用して仮想マシン スケール セットを作成します。 スケール セットは、[Azure PowerShell](virtual-machine-scale-sets-create-powershell.md)または [Azure ポータル](virtual-machine-scale-sets-create-portal.md)を使用して作成することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.20 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 


## <a name="create-a-scale-set"></a>スケール セットを作成する
スケール セットを作成する前に、[az group create](/cli/azure/group#create) を使ってリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

ここでは、[az vmss create](/cli/azure/vmss#create) を使って仮想マシン スケール セットを作成します。 次の例では、*myScaleSet* という名前のスケール セットを作成し、存在しない場合は SSH キーを生成します。

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="install-nginx-webserver"></a>NGINX Web サーバーをインストールする
スケール セットをテストするには、NGINX を VM インスタンスにインストールするスクリプトをカスタム スクリプト拡張機能を使用してダウンロードして実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 詳細については、「[Windows のカスタム スクリプト拡張機能](../virtual-machines/linux/extensions-customscript.md)」を参照してください。

次のように、NGINX をインストールするカスタム スクリプト拡張機能を適用します。

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>Web トラフィックを許可する
トラフィックが Web サーバーに到達できるようにするには、[az network lb rule creat](/cli/azure/network/lb/rule#create) を使用してロード バランサー ルールを作成します。 次の例では、*myLoadBalancerRuleWeb* という名前の規則を作成します。

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-web-server"></a>Web サーバーをテストする
Web サーバーが動いていることを確認するには、[az network public-ip show](/cli/azure/network/public-ip#show) でロード バランサーのパブリック IP アドレスを取得します。 次の例では、スケール セットの一部として作成された *myScaleSetLBPublicIP* の IP アドレスを取得します。

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

ロード バランサーのパブリック IP アドレスを Web ブラウザーに入力します。 ロード バランサーは、次の例に示すように、VM インスタンスのいずれかにトラフィックを配分します。

![NGINX の既定の Web ページ](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ
必要がなくなったら、次のように [az group delete](/cli/azure/group#delete) を使用して、リソース グループ、スケール セット、およびすべての関連リソースを削除できます。

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>次の手順
この入門記事では、基本的なスケール セットを作成し、カスタム スクリプト拡張機能を使用して基本的な NGINX Web サーバーを VM インスタンスにインストールしました。 スケーラビリティとオートメーションを高めるために、次の操作方法に関する記事を参照してスケール セットを拡張してください。

- [仮想マシン スケール セットへのアプリケーションのデプロイ](virtual-machine-scale-sets-deploy-app.md)
- [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md)[Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)または [Azure ポータル](virtual-machine-scale-sets-autoscale-portal.md)を使用した自動的なスケーリング
- [スケール セット VM インスタンスに対する OS の自動アップグレードの使用](virtual-machine-scale-sets-automatic-upgrade.md)