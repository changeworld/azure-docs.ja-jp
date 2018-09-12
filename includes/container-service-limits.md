---
title: インクルード ファイル
description: インクルード ファイル
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666992"
---
| リソース | 既定の制限 |
| --- | :--- |
| サブスクリプションあたりの最大クラスター数 | 100 |
| クラスターごとの最大ノード数 | 100 |
| ノードごとの最大ポッド数 (Kubenet を使用した[基本的なネットワーク][basic-networking]) | 110 |
| ノードごとの最大ポッド数 (Azure CNI を使用した[高度なネットワーク][advanced-networking]) | Azure CLI でのデプロイ: 110<sup>1</sup><br />Resource Manager テンプレート: 110<sup>1</sup><br />ポータルのデプロイ: 30 |

<sup>1</sup> この値は、クラスターのデプロイで (Azure CLI または Resource Manager テンプレートで AKS クラスターをデプロイするときに) 構成できます。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
