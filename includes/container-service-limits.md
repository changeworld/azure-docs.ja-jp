---
title: インクルード ファイル
description: インクルード ファイル
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 1c2dec106ae72ddead7bda54792fa74e38eb6660
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081061"
---
| リソース                                                                                                           | 制限                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| サブスクリプションあたりの最大クラスター数                                                                                  | 1000                                                                                                                                                                                                        |
| 仮想マシン可用性セットと Basic Load Balancer SKU を使用したクラスターあたりの最大ノード                       | 100                                                                                                                                                                                                         |
| Virtual Machine Scale Sets と [Standard Load Balancer SKU][standard-load-balancer] を使用したクラスターあたりの最大ノード数 | 1000 ([ノード プール][node-pool]あたり 100 ノード)                                                                                                                                                                 |
| ノードごとの最大ポッド数: Kubenet を使用した[基本的なネットワーク][basic-networking]                                           | 110                                                                                                                                                                                                         |
| ノードごとの最大ポッド数: Azure Container Networking Interface を使った[高度なネットワーク][advanced-networking]        | Azure CLI でのデプロイ: 30<sup>1</sup><br />Azure Resource Manager テンプレート: 30<sup>1</sup><br />ポータルのデプロイ: 30                                                                                        |
| Open Service Mesh (OSM) AKS アドオン プレビュー                                                                          | Kubernetes クラスターのバージョン: 1.19 以上<sup>2</sup><br />クラスターあたりの OSM コントローラー数: 1<sup>2</sup><br />OSM コントローラーあたりのポッド数: 500<sup>2</sup><br />OSM によって管理される Kubernetes サービス アカウント数: 50<sup>2</sup> |

<sup>1</sup>Azure CLI または Resource Manager テンプレートを使用して Azure Kubernetes Service (AKS) クラスターをデプロイする場合、この値は最大でノードあたり 250 ポッドまで構成できます。 AKS クラスターを既にデプロイしている場合、または Azure portal を使用してクラスターをデプロイする場合は、ノードあたりの最大ポッド数を構成できません。<br />

<sup>2</sup>AKS 用 OSM アドオンはプレビュー段階であり、今後、さらなる改良を経て一般提供 (GA) となる予定です。 プレビュー フェーズ中は、記載した制限の範囲内で使用することをお勧めします。<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
