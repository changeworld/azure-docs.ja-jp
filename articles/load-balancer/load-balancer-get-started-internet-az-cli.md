---
title: Azure CLI でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Load Balancer Standard を作成する | Microsoft Docs
description: Azure CLI でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Load Balancer Standard を作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 1a430f5c6349741e5d04626158dc89d42169a15b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Azure CLI でゾーン冗長フロントエンドを使用してパブリック Load Balancer Standard を作成する

この記事では、パブリック IP Standard アドレスでゾーン冗長フロントエンドを使用してパブリック [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) を作成する手順について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="register-for-availability-zones-preview"></a>可用性ゾーン (プレビュー) の登録

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.17 以降を実行していることが要件です。  バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

> [!NOTE]
> 可用性ゾーンはプレビュー段階にあり、開発とテストのシナリオのために準備されています。 選択された Azure リソース、リージョン、および VM サイズ ファミリに対するサポートが使用できます。 使用を開始する方法、および可用性ゾーンを試行する場合にどの Azure リソース、リージョン、および VM サイズ ファミリを使用できるかの詳細については、「[Overview of Availability Zones (可用性ゾーンの概要)](https://docs.microsoft.com/azure/availability-zones/az-overview)」を参照してください。 サポートについては、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) でアクセスするか、または [Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。  

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) の最新版がインストールされていること、および [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login) で Azure アカウントにログインしていることを確認します。

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループを作成するには、次のコマンドを使用します。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-public-ip-standard"></a>パブリック IP Standard の作成

パブリック IP Standard を作成するには、次のコマンドを使用します。

```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>ロード バランサーの作成

前の手順で作成した Standard パブリック IP を使用してパブリック Load Balancer Standard を作成するには、次のコマンドを使用します。

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>ポート 80 で LB プローブを作成する

ロード バランサー ヘルス プローブを作成するには、次のコマンドを使用します。

```azurecli-interactive
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>ポート 80 の LB ルールを作成する

ロード バランサー ルールを作成するには、次のコマンドを使用します。

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>次の手順
- [可用性ゾーン内にパブリック IP を作成する](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)方法を確認する



