---
title: Azure Standard Load Balancer と Virtual Machine Scale Sets
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: このラーニング パスを使用して、Azure Standard Load Balancer と Virtual Machine Scale Sets の使用を開始します。
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: f5a8453f84854108facb4da4616a7d362e0fb38c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528304"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets と Azure Load Balancer

仮想マシン スケール セットとロード バランサーを操作する場合は、次の項目を考慮する必要があります。

## <a name="multiple-virtual-machine-scale-sets-cant-use-the-same-load-balancer"></a>複数の仮想マシン スケール セットで同じロード バランサーを使用することはできません
## <a name="port-forwarding-and-inbound-nat-rules"></a>ポート フォワーディングとインバウンド NAT 規則:
  * スケール セットが作成された後、ロード バランサーの正常性プローブに使用される負荷分散ルールのバックエンド ポートを変更することはできません。 ポートを変更するには、Azure 仮想マシン スケール セットを更新し、ポートを更新してから正常性プローブを再度構成することで、正常性プローブを削除できます。
  * ロード バランサーのバックエンド プールで仮想マシン スケール セットを使用している場合は、既定のインバウンド NAT 規則が自動的に作成されます。
## <a name="inbound-nat-pool"></a>インバウンド NAT プール:
  * 各仮想マシン スケール セットには、少なくとも 1 つのインバウンド NAT プールが必要です。 
  * インバウンド NAT プールはインバウンド NAT 規則のコレクションです。 1 つのインバウンド NAT プールで複数の仮想マシン スケール セットをサポートすることはできません。
  
## <a name="load-balancing-rules"></a>負荷分散ルール:
  * ロード バランサーのバックエンド プールで仮想マシン スケール セットを使用している場合は、既定の負荷分散規則が自動的に作成されます。
## <a name="outbound-rules"></a>アウトバウンド規則:
  *  既に負荷分散規則によって参照されているバックエンド プールのアウトバウンド規則を作成するには、まずインバウンド負荷分散規則が作成されたポータルで **[暗黙的なアウトバウンド規則の作成]** を **[いいえ]** としてマークする必要があります。

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="負荷分散規則の作成" border="true":::

既存の Azure Load Balancer で仮想マシン スケール セットをデプロイするには、次の方法を使用できます。

* [Azure portal を使用して、既存の Azure Load Balancer で仮想マシン スケール セットを構成する](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal)。
* [Azure PowerShell を使用して、既存の Azure Load Balancer で仮想マシン スケール セットを構成する](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell)。
* [Azure CLI を使用して、既存の Azure Load Balancer で仮想マシン スケール セットを構成する](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli)。
