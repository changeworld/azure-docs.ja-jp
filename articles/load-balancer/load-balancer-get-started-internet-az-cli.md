---
title: "Azure CLI でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Load Balancer Standard を作成する | Microsoft Docs"
description: "Azure CLI でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Load Balancer Standard を作成する方法について説明します。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 769eb86af3e0506ddf03d1ec616d5a17b7e5f714
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Azure CLI でゾーン冗長フロントエンドを使用してパブリック Load Balancer Standard を作成する

この記事では、パブリック IP Standard アドレスでゾーン冗長フロントエンドを使用してパブリック [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) を作成する手順について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>可用性ゾーン、Load Balancer Standard、パブリック IP Standard プレビューの登録

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.17 以降を実行していることが要件です。  バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

>[!NOTE]
[Load Balancer Standard SKU](https://aka.ms/azureloadbalancerstandard) は現在プレビュー段階です。 プレビュー期間は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 運用サービスには、一般公開の [Load Balancer Basic SKU](load-balancer-overview.md) を使用してください。 

> [!NOTE]
> 可用性ゾーンはプレビュー段階であり、開発とテストのシナリオで使用できます。 一部の Azure リソース、リージョン、VM サイズ ファミリについては、サポートを利用できます。 利用を開始する方法のほか、可用性ゾーンを試すことができる Azure リソース、リージョン、VM サイズ ファミリについては、[可用性ゾーンの概要](https://docs.microsoft.com/azure/availability-zones/az-overview)に関するページを参照してください。 サポートが必要な場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) を参照するか、[Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。  

Load Balancer のフロントエンド パブリック IP アドレスのゾーンまたはゾーン冗長オプションを選択する前に、まず[可用性ゾーン プレビューの登録に関するページ](https://docs.microsoft.com/azure/availability-zones/az-overview)の手順を完了する必要があります。

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) の最新版がインストールされていること、および [az login](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest#login) で Azure アカウントにログインしていることを確認します。

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

## <a name="next-steps"></a>次のステップ
- [可用性ゾーン内にパブリック IP を作成する](../virtual-network/create-public-ip-availability-zone-cli.md)方法について学びます




