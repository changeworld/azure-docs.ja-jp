---
title: インクルード ファイル
description: インクルード ファイル
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4251f379c517d5ccfd0430987e3d5280208590ff
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400218"
---
| リソース | 既定の制限 |
| --- | :--- |
| サブスクリプションあたりの最大クラスター数 | 100 |
| クラスターごとの最大ノード数 | 100 |
| ノードごとの最大ポッド数 (Kubenet を使用した[基本的なネットワーク][basic-networking]) | 110 |
| ノードごとの最大ポッド数 (Azure CNI を使用した[高度なネットワーク][advanced-networking]) | Azure CLI でのデプロイ: 30<sup>1</sup><br />Resource Manager テンプレート: 30<sup>1</sup><br />ポータルのデプロイ: 30 |

<sup>1</sup> Azure CLI または Resource Manager テンプレートを使用して AKS クラスターをデプロイする場合、この値は最大で**ノードあたり 110 ポッド**まで構成できます。 AKS クラスターを既にデプロイした場合、または Azure portal を使用してクラスターをデプロイする場合は、ノードあたりの最大ポッド数を構成できません。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
