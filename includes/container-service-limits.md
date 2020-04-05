---
title: インクルード ファイル
description: インクルード ファイル
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334699"
---
| リソース | 制限 |
| --- | :--- |
| サブスクリプションあたりの最大クラスター数 | 100 |
| 仮想マシン可用性セットと Basic Load Balancer SKU を使用したクラスターあたりの最大ノード  | 100 |
| Virtual Machine Scale Sets と [Standard Load Balancer SKU][standard-load-balancer] を使用したクラスターあたりの最大ノード数 | 1000 ([ノード プール][node-pool]あたり 100 ノード) |
| ノードあたりの最大ポッド数:Kubenet を使用した[基本的なネットワーク][basic-networking] | 110 |
| ノードあたりの最大ポッド数:Azure Container Networking Interface を使った[高度なネットワーク][advanced-networking] | Azure CLI でのデプロイ: 30<sup>1</sup><br />Azure Resource Manager テンプレート:30<sup>1</sup><br />ポータルのデプロイ: 30 |

<sup>1</sup>Azure CLI または Resource Manager テンプレートを使用して Azure Kubernetes Service (AKS) クラスターをデプロイする場合、この値は最大でノードあたり 250 ポッドまで構成できます。 AKS クラスターを既にデプロイしている場合、または Azure portal を使用してクラスターをデプロイする場合は、ノードあたりの最大ポッド数を構成できません。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
