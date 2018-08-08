---
title: エラスティック プールのリソースをスケーリングする - Azure SQL Database | Microsoft Docs
description: このページでは、Azure SQL Database のエラスティック プールのリソースをスケーリングする方法について説明します。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 0f63739c8718ed7d6625bd18de4fdfff4df60276
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412340"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Azure SQL Database でエラスティック プールのリソースをスケーリングする

この記事では、Azure SQL Database でエラスティック プールとプールされたデータベースに使用できるコンピューティング リソースとストレージ リソースをスケーリングする方法について説明します。 

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>仮想コアベースの購入モデル: エラスティック プールのストレージ サイズの変更

- 最大サイズの上限に達するまでストレージをプロビジョニングすることができます。 
 - Standard ストレージの場合は、10 GB の増分でサイズを増減します
 - Premium のストレージでは、250 GB の増分でサイズを増減します
- エラスティック プールのストレージは、最大サイズを増減することでプロビジョニングできます。
- エラスティック プールのストレージの料金は、ストレージ量にサービス レベルのストレージ単価を掛けて計算します。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>仮想コアベースの購入モデル: エラスティック プールのコンピューティング リソース (仮想コア) の変更

[Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)、または [REST API](/rest/api/sql/elasticpools/update) を使って、リソースのニーズに基づいてエラスティック プールのパフォーマンス レベルを増減できます。

- プールの仮想コアを再スケーリングするときは、データベースの接続が短時間失われます。 これは、(プールではなく) 単一データベースの DTU を再スケーリングするときと同じ動作です。 再スケーリング操作中にデータベース接続が失われる時間とその影響について詳しくは、「[単一データベース: DTU を変更する](#single-database-change-storage-size)」をご覧ください。 
- プールの仮想コアの再スケーリングに要する時間は、プール内のすべてのデータベースで使われているストレージの総量によって異なる場合があります。 一般に、再スケーリングの待機時間の平均は 100 GB あたり 90 分以下です。 たとえば、プール内のすべてのデータベースで使用される領域の合計が 200 GB の場合、プールの再スケーリングにかかる想定待機時間は、3 時間以下になります。 Standard または Basic レベルでは場合により、使われている容量に関係なく、再スケーリングの待機時間が 5 分未満になることがあります。
- 一般に、データベースあたりの最小仮想コア数またはデータベースあたりの最大仮想コア数の変更に要する時間は、5 分以内です。
- プールの仮想コア数をダウンサイズするときは、プールで使われている領域が、ターゲットのサービス レベルとプール仮想コアで許可されている最大サイズより小さい必要があります。

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>DTU ベースの購入モデル: エラスティック プールのストレージ サイズの変更

- エラスティック プールの eDTU 価格には、追加コストなしで一定量のストレージが含まれます。 付属の容量を超える分のストレージについては、追加費用を払うことで、1 TB までは 250 GB 単位で、1 TB 以降は 256 GB 単位で、最大サイズ制限までプロビジョニングできます。 付属するストレージの量と最大サイズ制限については、「[エラスティック プール: ストレージ サイズとパフォーマンス レベル](#elastic-pool-storage-sizes-and-performance-levels)」をご覧ください。
- エラスティック プールの追加ストレージは、[Azure Portal](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)、または [REST API](/rest/api/sql/elasticpools/update) を使ってサイズを最大に増やすことでプロビジョニングできます。
- エラスティック プールの追加ストレージの料金は、追加ストレージ量にサービス レベルの追加ストレージ単価を掛けて計算します。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>DTU ベースの購入モデル: エラスティック プールのコンピューティング リソース (eDTU) の変更

[Azure Portal](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)、または [REST API](/rest/api/sql/elasticpools/update) を使って、リソースのニーズに基づいてエラスティック プールに使用可能なリソースを増減できます。

- プールの eDTU を再スケーリングするときは、データベースの接続が短時間失われます。 これは、(プールではなく) 単一データベースの DTU を再スケーリングするときと同じ動作です。 再スケーリング操作中にデータベース接続が失われる時間とその影響について詳しくは、「[単一データベース: DTU を変更する](#single-database-change-storage-size)」をご覧ください。 
- プールの eDTU の再スケーリングに要する時間は、プール内のすべてのデータベースで使われているストレージの総量よって異なる場合があります。 一般に、再スケーリングの待機時間の平均は 100 GB あたり 90 分以下です。 たとえば、プール内のすべてのデータベースで使用される領域の合計が 200 GB の場合、プールの再スケーリングにかかる想定待機時間は、3 時間以下になります。 Standard または Basic レベルでは場合により、使われている容量に関係なく、再スケーリングの待機時間が 5 分未満になることがあります。
- 一般に、データベースあたりの最小 eDTU またはデータベースあたりの最大 eDTU の変更に要する時間は、5 分以内です。
- プールの eDTU をダウンサイズするときは、プールで使われている領域が、ターゲットのサービス レベルとパフォーマンス レベルで許可されている最大サイズより小さい必要があります。
- プールの eDTU を再スケーリングするとき、(1) プールの最大ストレージ サイズがターゲット プールでサポートされていて、かつ、(2) 最大ストレージ サイズがターゲット プールに付属するストレージ量を超える場合、追加のストレージ コストが適用されます。 たとえば、最大サイズが 100 GB の 100 eDTU のStandard プールを、50 eDTU の Standard プールにダウンサイズすると、ターゲット プールは最大サイズの 100 GB をサポートしますが、付属するストレージ量は 50 GB だけなので、追加ストレージ コストがかかります。 したがって、追加ストレージ量は 100 GB – 50 GB = 50 GB になります。 追加ストレージの価格については、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 実際に使われる領域の量が付属のストレージの量より少ない場合、データベースの最大サイズを付属の量に減らすことで、この追加コストを回避できます。 

## <a name="next-steps"></a>次の手順

全体的なリソースの制限については、[SQL Database の仮想コアペースのリソース制限 - エラスティック プール](sql-database-vcore-resource-limits-elastic-pools.md)および [SQL Database の DTU ベースのリソース制限 - エラスティック プール](sql-database-dtu-resource-limits-elastic-pools.md)に関するページを参照してください。
