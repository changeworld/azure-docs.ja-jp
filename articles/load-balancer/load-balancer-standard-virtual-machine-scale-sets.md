---
title: Azure Standard Load Balancer と仮想マシン スケール セットの規則の追加
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: このラーニング パスを使用して、Azure Standard Load Balancer と仮想マシン スケール セットの使用を開始します。
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
ms.openlocfilehash: 2ba2d00d1cbb025b665c2c9c6cb929f8b46a1f33
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723667"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Azure Load Balancer と仮想マシン スケール セットの規則の追加

仮想マシン スケール セットと Azure Load Balancer を操作する場合、次のガイドラインを考慮してください。

## <a name="port-forwarding-and-inbound-nat-rules"></a>ポート フォワーディングとインバウンド NAT 規則

スケール セットが作成された後、ロード バランサーの正常性プローブに使用される負荷分散規則のバックエンド ポートを変更することはできません。 ポートを変更するには、仮想マシン スケール セットを更新し、ポートを更新して、正常性プローブを削除します。 次に、正常性プローブを再度構成します。

ロード バランサーのバックエンド プールで仮想マシン スケール セットを使用している場合、既定のインバウンド NAT 規則が自動的に作成されます。
  
## <a name="inbound-nat-pool"></a>インバウンド NAT プール

各仮想マシン スケール セットには、少なくとも 1 つのインバウンド NAT プールが必要です。 インバウンド NAT プールはインバウンド NAT 規則のコレクションです。 1 つのインバウンド NAT プールで複数の仮想マシン スケール セットをサポートすることはできません。

## <a name="load-balancing-rules"></a>負荷分散規則

ロード バランサーのバックエンド プールで仮想マシン スケール セットを使用している場合は、既定の負荷分散規則が自動的に作成されます。

## <a name="virtual-machine-scale-set-instance-level-ips"></a>仮想マシン スケール セットのインスタンス レベル IP

[インスタンスごとに複数のパブリック IP](../virtual-network/ip-services/public-ip-address-prefix.md) を持つ仮想マシン スケール セットが、前にロード バランサーを使用して作成される場合、インスタンス IP の SKU は Load Balancer (Basic または Standard など) の SKU によって決定されます。  Standard Load Balancer を使用する場合、個々のインスタンスの IP の種類はすべて標準の "ゾーンなし" になります (ただし、Load Balancer フロントエンドはゾーン ベースまたはゾーン冗長になる可能性があります)。

## <a name="outbound-rules"></a>アウトバウンド規則

既に負荷分散規則によって参照されているバックエンド プールのアウトバウンド規則を作成するには、インバウンド負荷分散規則を作成するときに、Azure portal の **[暗黙的なアウトバウンド規則の作成]** で **[いいえ]** を選択します。

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="負荷分散規則の作成を示すスクリーンショット。" border="true":::

既存の Load Balancer のインスタンスで仮想マシン スケール セットをデプロイするには、次の方法を使用します。

* [Azure portal を使用して、既存の Azure Load Balancer のインスタンスで仮想マシン スケール セットを構成する](./configure-vm-scale-set-portal.md)
* [Azure PowerShell を使用して、既存の Azure Load Balancer のインスタンスで仮想マシン スケール セットを構成する](./configure-vm-scale-set-powershell.md)
* [Azure CLI を使用して、既存の Azure Load Balancer のインスタンスで仮想マシン スケール セットを構成する](./configure-vm-scale-set-cli.md)
* [仮想マシン スケール セットで使用される既存の Azure Load Balancer のインスタンスを更新または削除する](./update-load-balancer-with-vm-scale-set.md)
