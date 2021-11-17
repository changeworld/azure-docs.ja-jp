---
title: Azure Arc 対応 PostgreSQL Hyperscale の制限事項
description: Azure Arc 対応 PostgreSQL Hyperscale の制限事項
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: a2a63e62598b291388375e0eb520d390dbdc4278
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562829"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale の制限事項

この記事では、Azure Arc 対応 PostgreSQL Hyperscale の制限事項について説明します。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>バックアップと復元
バックアップと復元の機能は、設計とエクスペリエンスの最終調整のため、一時的に削除されています。

## <a name="high-availability"></a>高可用性
高可用性の構成や、インフラで障害が発生した際のフェールオーバーの確保はまだ利用できません。

## <a name="databases"></a>データベース
複数のワーカー ノードにデプロイをスケールアウトしている場合、1 つのサーバー グループで複数のデータベースをホストすることはできません。

## <a name="roles-and-responsibilities"></a>ロールと責任

Microsoft とそのお客様の間のロールと責任は、Azure マネージド サービス (サービスとしてのプラットフォーム (PaaS)) と Azure ハイブリッド (Azure Arc 対応 PostgreSQL Hyperscale など) とで異なります。 

### <a name="frequently-asked-questions"></a>よく寄せられる質問
次の表は、サポートのロールと責任に関してよく寄せられる質問とその回答をまとめたものです。

| Question                      | Azure PaaS (サービスとしてのプラットフォーム) | Azure Arc ハイブリッド サービス |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| インフラストラクチャの提供者  | Microsoft                          | Customer                  |
| ソフトウェアの提供者*       | Microsoft                          | Microsoft                 |
| 運営者 | Microsoft                          | Customer                  |
| Microsoft による SLA 提供の有無      | はい                                | いいえ                        |
| SLA の責任者 | Microsoft                          | Customer                  |

\* Azure サービス

__Microsoft が Azure Arc ハイブリッド サービスに SLA を提供しないのはなぜですか。__ Microsoft はこのインフラストラクチャを所有せず、運営していないためです。 これはお客様が行います。

## <a name="next-steps"></a>次のステップ

- **実際に使ってみてください。** Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) をすぐに開始できます。 

- **独自のものを作成します。** 独自の Kubernetes クラスターを作成するには、こちらの手順に従います。 
   1. [クライアント ツールをインストールする](install-client-tools.md)
   2. [Azure Arc データ コントローラーを作成する](create-data-controller.md)
   3. [Azure Arc で Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc 対応データ サービスについて確認する](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc について確認する](https://aka.ms/azurearc)
