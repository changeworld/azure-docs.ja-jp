---
title: ゾーン冗長フロントエンドを使用して Load Balancer を作成する - Azure CLI
titleSuffix: Azure Load Balancer
description: Azure CLI を使用してゾーン冗長パブリック IP アドレス フロントエンドでパブリック Standard Load Balancer を作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: allensu
ms.openlocfilehash: b9e8874883b4155d38209da07590d5ce720d2335
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894538"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-cli"></a>Azure CLI でゾーン冗長フロントエンドを使用して Standard Load Balancer を作成する

この記事では、パブリック IP Standard アドレスでゾーン冗長フロントエンドを使用してパブリック [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) を作成する手順について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) の最新版をインストールしていることと、 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) で Azure アカウントにログインしていることを確認します。

> [!NOTE]
>  可用性ゾーンのサポートは、Azure リソース、リージョン、および VM サイズ ファミリを選択するために使用できます。 使用を開始する方法、および可用性ゾーンを試行する場合にどの Azure リソース、リージョン、および VM サイズ ファミリを使用できるかの詳細については、「[Overview of Availability Zones (可用性ゾーンの概要)](https://docs.microsoft.com/azure/availability-zones/az-overview)」を参照してください。 サポートについては、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) でアクセスするか、または [Azure サポート チケットを開く](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。 


## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループを作成するには、次のコマンドを使用します。

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>パブリック IP Standard の作成

パブリック IP Standard を作成するには、次のコマンドを使用します。

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>ロード バランサーの作成

前の手順で作成した Standard パブリック IP を使用してパブリック Load Balancer Standard を作成するには、次のコマンドを使用します。

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>ポート 80 で LB プローブを作成する

ロード バランサー ヘルス プローブを作成するには、次のコマンドを使用します。

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>ポート 80 の LB ルールを作成する

ロード バランサー ルールを作成するには、次のコマンドを使用します。

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>次のステップ
- [Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)を確認します。



