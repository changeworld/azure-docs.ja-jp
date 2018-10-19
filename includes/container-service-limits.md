---
title: インクルード ファイル
description: インクルード ファイル
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 71294824bd3dd5215c388cfcd44382c7eee123ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874065"
---
| リソース | 既定の制限 |
| --- | :--- |
| サブスクリプションあたりの最大クラスター数 | 100 |
| クラスターごとの最大ノード数 | 100 |
| ノードごとの最大ポッド数 (Kubenet を使用した[基本的なネットワーク][basic-networking]) | 110 |
| ノードごとの最大ポッド数 (Azure CNI を使用した[高度なネットワーク][advanced-networking]) | Azure CLI でのデプロイ: 30<sup>1</sup><br />Resource Manager テンプレート: 30<sup>1</sup><br />ポータルのデプロイ: 30 |

<sup>1</sup> この値は、クラスターのデプロイで (Azure CLI または Resource Manager テンプレートで AKS クラスターをデプロイするときに) 構成できます。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
