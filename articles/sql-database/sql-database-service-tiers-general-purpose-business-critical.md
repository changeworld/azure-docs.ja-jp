---
title: Azure SQL Database - 汎用と Business Critical | Microsoft Docs
description: この記事では、仮想コアの購入レベルにおける汎用サービス レベルと Business Critical サービス レベルについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 15fd86a88c3025f81741d614b03d5c4c7c60262c
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351744"
---
# <a name="general-purpose-and-business-critical-service-tiers"></a>General Purpose および Business Critical サービス レベル

この記事では、仮想コアベースの購入モデルにおける汎用サービス レベルと Business Critical サービス レベルに対するストレージおよびバックアップに関する考慮事項について説明します。

> [!NOTE]
> 仮想コアベースの購入モデルにおけるハイパースケール サービス レベルの詳細については、[ハイパースケール サービス レベル](sql-database-service-tier-hyperscale.md)に関するページを参照してください。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[Azure SQL Database の購入モデルとリソース](sql-database-service-tiers.md)に関する記事をご覧ください。

## <a name="data-and-log-storage"></a>データとログのストレージ

以下、具体例に沿って説明します。

- 割り当てられたストレージは、データ ファイル (MDF) およびログ ファイル (LDF) によって使用されます。
- 各単一データベースのコンピューティング サイズで、最大データベース サイズがサポートされ、既定の最大サイズは 32 GB です。
- 必要な単一データベース サイズ (MDF のサイズ) を構成するとき、LDF をサポートするために、その 30% の追加ストレージが自動的に追加されます
- Managed Instance でのストレージ サイズは、32 GB の倍数で指定する必要があります。
- 10 GB と、サポートされている最大サイズの間で、任意の単一データベース サイズを選択できます
  - Standard Storage の場合は、10 GB の増分でサイズを増減します
  - Premium Storage の場合は、250 GB の増分でサイズを増減します
- General Purpose サービス レベルでは、`tempdb` には、接続されている SSD を使用します。このストレージ コストは、仮想コア価格に含まれます。
- Business Critical サービス レベルでは、`tempdb` は、接続されている SSD を MDF ファイルおよび LDF ファイルと共有します。tempDB ストレージ コストは、仮想コア価格に含まれます。

> [!IMPORTANT]
> MDF および LDF に割り当てられている合計ストレージに対して課金されます。

MDF および LDF の現在の合計サイズを監視するには、[sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql) を使用します。 個々の MDF ファイルおよび LDF ファイルの現在のサイズを監視するには、[sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) を使用します。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="backups-and-storage"></a>バックアップとストレージ

データベース バックアップのストレージは、SQL Database のポイントインタイム リストア (PITR) および[長期リテンション期間 (LTR)](sql-database-long-term-retention.md) 機能をサポートするために割り当てられます。 このストレージはデータベースごとに別個に割り当てられ、データベース料金ごとに 2 つが個々に課金されます。

- **PITR**: 個々のデータベース バックアップが、[RA-GRS ストレージ](../storage/common/storage-designing-ha-apps-with-ragrs.md)に自動的にコピーされます。 ストレージのサイズは、新しいバックアップが作成されるにつれ、動的に増加します。  このストレージは、毎週の完全バックアップ、毎日の差分バックアップ、5 分ごとにコピーされるトランザクション ログ バックアップによって使用されます。 ストレージの使用量は、データベースの変化率とリテンション期間によって異なります。 リテンション期間は、データベースごとに 7 ～ 35 日の範囲内で別々に構成できます。 データ サイズと同量の最小ストレージ容量が、追加料金なしで提供されます。 ほとんどのデータベースでは、この容量で十分に 7 日間のバックアップを格納できます。
- **LTR**: SQL Database には、最大 10 年の完全バックアップの長期リテンション期間を構成するオプションが用意されています。 LTR ポリシーが有効になっている場合、これらのバックアップは、RA-GRS ストレージに自動的に格納されますが、バックアップがコピーされる頻度は制御できます。 さまざまなコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対して異なったリテンション期間を選択することができます。 この構成によって、LTR バックアップに使用されるストレージ容量が定義されます。 LTR ストレージのコストは、LTR 料金計算ツールを使用して見積もることができます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

- 汎用サービス レベルと Business Critical サービスレベルの単一データベースに対して選択できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[単一データベースに対する SQL Database の仮想コアベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)に関するページを参照してください
- 汎用サービス レベルと Business Critical サービスレベルのエラスティック プールに対して選択できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[エラスティック プールに対する SQL Database の仮想コアベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)に関するページを参照してください。
