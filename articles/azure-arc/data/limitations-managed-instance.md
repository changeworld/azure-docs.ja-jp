---
title: Azure Arc 対応 SQL Managed Instance の制限
description: Azure Arc 対応 SQL Managed Instance の制限
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 09/07/2021
ms.topic: conceptual
ms.openlocfilehash: 8709504d93f22ee5f2704b7b1a8d71a5e66df796
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837834"
---
# <a name="limitations-of-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance の制限

この記事では、Azure Arc 対応 SQL Managed Instance の制限事項について説明します。 

現時点では、Business Critical サービス レベルはパブリック プレビューです。 General Purpose サービス レベルは一般提供されています。

## <a name="backup-and-restore"></a>バックアップと復元

### <a name="automated-backups"></a>自動バックアップ 

-  単純復旧モデルのユーザー データベースはバックアップされません。
-  データベースの作成および削除に対する干渉を防ぐために、システム データベース `model` はバックアップされません。 管理操作が実行されているときはデータベースがロックされます。

### <a name="point-in-time-restore-pitr"></a>ポイントインタイム リストア (PITR)

-  1 つの Azure Arc 対応 SQL Managed Instance から、別の Azure Arc 対応 SQL Managed Instance スへの復元は、サポートされていません。  データベースは、バックアップが作成されたのと同じ Arc 対応 SQL Managed Instance にのみ復元できます。
-  ポイントインタイム リストアの目的のためには、データベースの名前変更は現在サポートされていません。
-  現在のところ、TDE 対応データベースの復元はサポートされていません。
-  現在、削除されたデータベースは復元できません。

## <a name="other-limitations"></a>その他の制限事項 

-  トランザクション レプリケーションは現在サポートされていません。
-  ログ配布は現在ブロックされています。
-  SQL Server 認証のみがサポートされています。

## <a name="roles-and-responsibilities"></a>ロールと責任

Microsoft とそのお客様の間のロールと責任は、Azure PaaS サービス (サービスとしてのプラットフォーム) と Azure ハイブリッド (Azure Arc 対応 SQL Managed Instance など) とで異なります。 

### <a name="frequently-asked-questions"></a>よく寄せられる質問

次の表は、サポートのロールと責任に関してよく寄せられる質問とその回答をまとめたものです。

| Question                          | Azure PaaS (サービスとしてのプラットフォーム) | Azure Arc ハイブリッド サービス |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| インフラストラクチャの提供者  | Microsoft                          | Customer                  |
| ソフトウェアの提供者*       | Microsoft                          | Microsoft                 |
| 運営者          | Microsoft                          | Customer                  |
| Microsoft による SLA 提供の有無      | はい                                | いいえ                        |
| SLA の責任者          | Microsoft                          | Customer                  |

\* Azure サービス

__Microsoft が Azure Arc ハイブリッド サービスに SLA を提供しないのはなぜですか。__ Microsoft はこのインフラストラクチャを所有せず、運営していないためです。 これはお客様が行います。

## <a name="next-steps"></a>次のステップ

- **実際に使ってみてください。** Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。 

- **独自のものを作成します。** 独自の Kubernetes クラスターを作成するには、こちらの手順に従います。 
   1. [クライアント ツールをインストールする](install-client-tools.md)
   2. [Azure Arc データ コントローラーを作成する](create-data-controller.md)
   3. [Azure Arc 対応 SQL Managed Instance を作成する](create-sql-managed-instance.md) 

- **Learn**
   - [Azure Arc 対応データ サービスについて確認する](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc について確認する](https://aka.ms/azurearc)
