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
ms.openlocfilehash: 23c25953d2f493d2dd799bfd11dbbb69db002d1b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55735987"
---
| リソース | 既定の制限 |
| --- | :--- |
| サブスクリプションあたりの最大クラスター数 | 100 |
| クラスターごとの最大ノード数 | 100 |
| ノードごとの最大ポッド数: Kubenet を使用した[基本的なネットワーク][basic-networking] | 110 |
| ノードごとの最大ポッド数: Azure CNI を使用した[高度なネットワーク][advanced-networking] | Azure CLI でのデプロイ: 30<sup>1</sup><br />Resource Manager テンプレート: 30<sup>1</sup><br />ポータルのデプロイ: 30 |

<sup>1</sup> Azure CLI または Resource Manager テンプレートを使用して AKS クラスターをデプロイする場合、この値は最大で**ノードあたり 110 ポッド**まで構成できます。 AKS クラスターを既にデプロイした場合、または Azure portal を使用してクラスターをデプロイする場合は、ノードあたりの最大ポッド数を構成できません。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
