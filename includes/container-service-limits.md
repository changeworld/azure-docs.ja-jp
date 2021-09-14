---
title: インクルード ファイル
description: インクルード ファイル
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 928c4669ec7b1a04874f760cc14234cfa874e99a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437898"
---
| リソース                                                                                                           | 制限                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| サブスクリプションあたりの最大クラスター数                                                                                  | 5000                                                                                                                                                                                                        |
| 仮想マシン可用性セットと Basic Load Balancer SKU を使用したクラスターあたりの最大ノード                       | 100                                                                                                                                                                                                         |
| Virtual Machine Scale Sets と [Standard Load Balancer SKU][standard-load-balancer] を使用したクラスターあたりの最大ノード数 | 1000 (すべての[ノード プール][node-pool]対象)                                            |
| クラスターあたりの最大ノード プール数                                                                                     | 100                                                                                  |
| ノードごとの最大ポッド数: Kubenet を使用した[基本的なネットワーク][basic-networking]                                           | 最大値: 250 <br /> Azure CLI の既定値: 110 <br /> Azure Resource Manager テンプレートの既定値: 110 <br /> Azure portal デプロイの既定値: 30          |
| ノードごとの最大ポッド数: Azure Container Networking Interface を使った[高度なネットワーク][advanced-networking]        | 最大値: 250 <br /> 既定値: 30                                                      |
| Open Service Mesh (OSM) AKS アドオン プレビュー                                                                          | Kubernetes クラスターのバージョン: 1.19 以上<sup>1</sup><br />クラスターあたりの OSM コントローラー数: 1<sup>1</sup><br />OSM コントローラーあたりのポッド数: 500<sup>1</sup><br />OSM によって管理される Kubernetes サービス アカウント数: 50<sup>1</sup> |

<sup>1</sup>AKS 用 OSM アドオンはプレビュー段階であり、一般提供 (GA) 前にさらに改良される予定です。 プレビュー段階では、記載されている制限の範囲内で使用することをお勧めします。<br />

<br />

| Kubernetes コントロール プレーンの制限                                                                                                                                                                                                     |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 有料レベル                                                                                  | 負荷に基づいた自動スケールアウト                                                                                                                                                                                                        |
| Free レベル                       | 50 個の変更と 100 個の読み取り専用呼び出しの[配信要求制限](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/)付きの制限されたリソース                                                                                                                                                                                                         |

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
