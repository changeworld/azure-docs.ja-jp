---
title: インクルード ファイル
description: インクルード ファイル
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: include
origin.date: 10/11/2018
ms.date: 05/13/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072882"
---
| Resource | 既定の制限 |
| --- | :--- |
| サブスクリプションあたりの最大クラスター数 | 100 |
| クラスターあたりの最大ノード数 | 100 |
| ノードあたりの最大ポッド数:Kubenet を使用した[基本的なネットワーク][basic-networking] | 110 |
| ノードあたりの最大ポッド数:Azure Container Networking Interface を使った[高度なネットワーク][advanced-networking] | Azure CLI でのデプロイ: 30<sup>1</sup><br />Azure Resource Manager テンプレート:30<sup>1</sup><br />ポータルのデプロイ: 30 |

<!--Pending verify-->
<!--Not Available on Portal deployment: 30-->

<sup>1</sup>Azure CLI または Resource Manager テンプレートを使用して Azure Kubernetes Service (AKS) クラスターをデプロイする場合、この値は最大でノードあたり 250 ポッドまで構成できます。 AKS クラスターを既にデプロイしている場合、または Azure portal を使用してクラスターをデプロイする場合は、ノードあたりの最大ポッド数を構成できません。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://support.azure.cn/zh-cn/support/support-azure/