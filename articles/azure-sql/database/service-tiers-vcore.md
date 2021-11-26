---
title: 仮想コア購入モデル
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 仮想コア購入モデルでは、Azure SQL Database と Azure SQL Managed Instance のコンピューティング リソースとストレージ リソースを個別にスケーリングし、オンプレミスのパフォーマンスと一致させて、価格を最適化することができます。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 05/18/2021
ROBOTS: NOINDEX
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 67e79d8b307de6327f3bacf2bfd57f95080b50d1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553194"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>仮想コア モデルの概要 - Azure SQL Database および Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と Azure SQL Managed Instance で使用される仮想コア (vCore) 購入モデルには、いくつかの利点があります。

- ワークロードのコンピューティング要件とメモリ要件をより満たすようにハードウェアの世代を制御します。
- [Azure ハイブリッド特典 (AHB)](../azure-hybrid-benefit.md) および[予約インスタンス (RI)](reserved-capacity-overview.md) に対する料金割引。
- コンピューティングを強化するハードウェア詳細における透明性の向上。それが、オンプレミスのデプロイからの移行計画を容易にします。
- Azure SQL Database の場合、仮想コア購入モデルは、DTU モデルよりも高いコンピューティング、メモリ、I/O、ストレージの制限が提供されます。

仮想コア購入モデルか DTU 購入モデルかの選択の詳細については、[仮想コア購入モデルか DTU 購入モデルかの選択](purchasing-models.md)に関するページをご覧ください。

> [!div class="nextstepaction"]
> [Azure SQL を改善するためのアンケート](https://aka.ms/AzureSQLSurveyNov2021)

## <a name="service-tiers"></a>サービス階層

次の記事では、各製品の仮想コア購入モデルに関する具体的な情報を提供します。

- 仮想コアモデルの Azure SQL Database の詳細については、「[仮想コアモデルの概要 - Azure SQL Database](service-tiers-sql-database-vcore.md)」を参照してください。
- 仮想コア モデルの Azure SQL Managed Instance サービス レベルの詳細については、「[仮想コア モデルの概要 - Azure SQL Managed Instance](../managed-instance/service-tiers-managed-instance-vcore.md)」 を参照してください。

## <a name="next-steps"></a>次のステップ

作業を開始するには、次のトピックをご覧ください。 
- [Azure portal を使用して SQL Database を作成する](single-database-create-quickstart.md)
- [Azure portal を使用して SQL Managed Instance を作成する](../managed-instance/instance-create-quickstart.md)

- 価格の詳細については、以下を参照してください 
    - [Azure SQL Database の価格ページ](https://azure.microsoft.com/pricing/details/sql-database/single/)
    - [Azure SQL マネージド単一インスタンスの価格に関するページ](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Azure SQL マネージド単一インスタンス プールの価格に関するページ](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
    
General Purpose サービス レベルおよび Business Critical サービス レベルで使用できる特定のコンピューティングおよびストレージ サイズの詳細については、以下を参照してください。

- [Azure SQL Database の仮想コアベースのリソース制限](resource-limits-vcore-single-databases.md)
- [プールされた Azure SQL Database の仮想コアベースのリソース制限](resource-limits-vcore-elastic-pools.md)
- [Azure SQL Managed Instance の仮想コアベースのリソース制限](../managed-instance/resource-limits.md)
