---
title: Azure SQL Database サービス - 仮想コア | Microsoft Docs
description: 仮想コア ベースの購入モデルでは、コンピューティングおよびストレージ リソースを個別にスケーリングし、オンプレミスのパフォーマンスを一致させて、コストを最適化できます。
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 68343f3fcdd2275012207d7ac5a5f3bcdc71d1b8
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414376"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>仮想コアのサービス レベル、コンピューティング、メモリ、ストレージ、および IO リソースの選択

サービス レベルは、さまざまなパフォーマンス レベル、高可用性の設計、障害の分離、ストレージの種類、および IO 範囲によって区別されます。 お客様は、バックアップ用に必要なストレージと保有期間を個別に構成する必要があります。 仮想コア モデルでは、単一データベースとエラスティック プールは、[SQL Server 向け Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)によって、最大 30% の割引対象となります。

次の表は、この 2 つのレベルの違いを把握するうえで役立ちます。

||**汎用**|**Business Critical**|
|---|---|---|
|最適な用途|ほとんどのビジネス ワークロード。 予算重視のスケーラブルでバランスの取れたコンピューティングおよびストレージ オプションを提供します。|IO 要件の高いビジネス アプリケーション。 分離された複数のレプリカを使用して、最高の耐障害性が提供されます。|
|コンピューティング|1 から 80 vCore、Gen4 と Gen5 |1 から 80 vCore、Gen4 と Gen5|
|メモリ|Gen4: コアあたり 7 GB<br>Gen5: コアあたり 5.5 GB | Gen4: コアあたり 7 GB<br>Gen5: コアあたり 5.5 GB |
|Storage|Premium リモート ストレージ、5 GB ～ 4 TB|ローカル SSD ストレージ、5 GB から 4 TB|
|IO スループット (概算)|仮想コアあたり 500 IOPS (最大 7000 IOPS)|コアあたり 5000 IOPS (最大 200000 IOPS)|
|可用性|1 レプリカ、読み取りスケールなし|3 レプリカ、1 [読み取りスケール](sql-database-read-scale-out.md)、ゾーン冗長 HA|
|バックアップ|RA-GRS、7 ～ 35 日 (既定では 7 日)|RA-GRS、7 ～ 35 日 (既定では 7 日)|
|インメモリ|該当なし|サポートされています|
|||

> [!IMPORTANT]
> 必要なコンピューティング能力が仮想コア 1 つ分を下回る場合は、DTU ベースの購入モデルを使用します。

よく寄せられる質問の回答については、「[SQL Database に関する FAQ](sql-database-faq.md)」を参照してください。 

## <a name="storage-considerations"></a>ストレージに関する考慮事項

以下、具体例に沿って説明します。
- 割り当てられたストレージは、データ ファイル (MDF) およびログ ファイル (LDF) によって使用されます。
- パフォーマンス レベルごとに最大データベース サイズがサポートされ、既定の最大サイズは 32 GB です。
- 必要なデータベース サイズ (MDF のサイズ) を構成するとき、LDF をサポートするために、その 30% の追加ストレージが自動的に追加されます
- 10 GB と、サポートされている最大サイズの間で、任意のデータベース サイズを選択できます
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

データベース バックアップのストレージは、SQL Database のポイントインタイム リストア (PITR) および長期リテンション期間 (LTR) 機能をサポートするために割り当てられます。 このストレージはデータベースごとに別個に割り当てられ、データベース料金ごとに 2 つが個々に課金されます。 

- **PITR**: 個々のデータベース バックアップが、RA-GRS ストレージに自動的にコピーされます。 ストレージのサイズは、新しいバックアップが作成されるにつれ、動的に増加します。  このストレージは、毎週の完全バックアップ、毎日の差分バックアップ、5 分ごとにコピーされるトランザクション ログ バックアップによって使用されます。 ストレージの使用量は、データベースの変化率とリテンション期間によって異なります。 リテンション期間は、データベースごとに 7 ～ 35 日の範囲内で別々に構成できます。 データ サイズと同量の最小ストレージ容量が、追加料金なしで提供されます。 ほとんどのデータベースでは、この容量で十分に 7 日間のバックアップを格納できます。
- 
  **LTR**: SQL Database には、最大 10 年の完全バックアップの長期リテンション期間を構成するオプションが用意されています。 LTR ポリシーが有効になっている場合、これらのバックアップは、RA-GRS ストレージに自動的に格納されますが、バックアップがコピーされる頻度は制御できます。 さまざまなコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対して異なったリテンション期間を選択することができます。 この構成によって、LTR バックアップに使用されるストレージ容量が定義されます。 LTR ストレージのコストは、LTR 料金計算ツールを使用して見積もることができます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。

## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

仮想コアベースの購入モデルでは、[SQL Server 向け Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)を利用して、お使いの既存のライセンスを SQL Database の割引料金のライセンスに交換できます。 この Azure 特典では、オンプレミスのソフトウェア アシュアランス付き SQL Server ライセンスを利用することで、オンプレミスの SQL Server ライセンスで Azure SQL Database の料金が最大 30% オフになります。

![価格](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>geo レプリケーション リンクを含む単一データベースの移行

DTU ベースのモデルから仮想コアベースのモデルへの移行は、Standard データベースと Premium データベースの間で geo レプリケーション関係をアップグレードまたはダウングレードするのと似ています。 geo レプリケーションを終了する必要はありませんが、ユーザーは、シーケンス処理ルールに従う必要があります。 アップグレードの場合は、最初にセカンダリ データベースをアップグレードしてから、プライマリをアップグレードする必要があります。 ダウングレードは逆の順序で行います。つまり、最初にプライマリ データベースをダウングレードしてから、セカンダリをダウングレードします。 

2 つのエラスティック プールの間で geo レプリケーションを使用している場合は、一方のプールをプライマリとして指定し、もう一方をセカンダリとして指定することをお勧めします。 その場合、エラスティック プールの移行でも同じガイダンスが使用されます。  ただし、技術的には、1 つのエラスティック プールに、プライマリ データベースとセカンダリ データベースの両方を含めることができます。 この場合、正しく移行するには、使用率の高い方のプールを "プライマリ" と見なし、適宜シーケンス処理ルールに従います。  

次の表に、具体的な移行シナリオのガイダンスを示します。 

|現在のサービス レベル|移行先のサービス レベル|移行の種類|ユーザー操作|
|---|---|---|---|
|標準|汎用|ラテラル|任意の順序で移行できますが、適切な仮想コア サイズを確保する必要があります*|
|Premium|Business Critical|ラテラル|任意の順序で移行できますが、適切な仮想コア サイズを確保する必要があります*|
|標準|Business Critical|アップグレード|セカンダリを最初に移行する必要があります|
|Business Critical|標準|ダウングレード|プライマリを最初に移行する必要があります|
|Premium|汎用|ダウングレード|プライマリを最初に移行する必要があります|
|汎用|Premium|アップグレード|セカンダリを最初に移行する必要があります|
|Business Critical|汎用|ダウングレード|プライマリを最初に移行する必要があります|
|汎用|Business Critical|アップグレード|セカンダリを最初に移行する必要があります|
||||

\* Standard レベルでは 100 DTU ごとに 1 つ以上の仮想コア、Premium レベルでは 125 DTU ごとに 1 つ以上の仮想コアが必要です

## <a name="migration-of-failover-groups"></a>フェールオーバー グループの移行 

複数のデータベースが含まれるフェールオーバー グループの移行では、プライマリ データベースとセカンダリ データベースを個別に移行する必要があります。 その処理中は、同じ考慮事項とシーケンス処理ルールが適用されます。 データベースが仮想コアベースのモデルに変換された後も、フェールオーバー グループには、同じポリシー設定が引き続き有効になります。 

## <a name="creation-of-a-geo-replication-secondary"></a>geo レプリケーション セカンダリの作成

プライマリと同じサービス レベルを使用して、geo セカンダリのみを作成できます。 ログ生成率が高いデータベースの場合、プライマリと同じパフォーマンス レベルでセカンダリを作成することをお勧めします。 単一プライマリ データベースに対してエラスティック プールに geo セカンダリを作成する場合、そのプールの `maxVCore` 設定を、プライマリ データベースのパフォーマンス レベルと一致させることをお勧めします。 geo セカンダリの作成先エラスティック プールと、そのプライマリがあるエラスティック プールが異なる場合、その 2 つのプールの `maxVCore` 設定を同じにすることをお勧めします

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>データベースのコピーを使用した、DTU ベースのデータベースから仮想コアベースのデータベースへの変換

DTU ベース パフォーマンス レベルのデータベースを、仮想コアベース パフォーマンス レベルのデータベースにコピーする場合、コピー先のパフォーマンス レベルが、コピー元データベースの最大データベース サイズをサポートしている限り、制限や特別なシーケンス処理は伴いません。 これは、データベースのコピーによって、コピー操作を開始した時点のデータ スナップショットが作成されるので、コピー元とコピー先の間でデータ同期が行われないためです。 

## <a name="next-steps"></a>次の手順

- 単一データベースに対して選択できる具体的なパフォーマンス レベルとストレージ サイズの詳細については、[単一データベースに対する SQL Database の仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)に関するページをご覧ください。
- エラスティック プールに対して選択できる具体的なパフォーマンス レベルとストレージ サイズの詳細については、[エラスティック プールに対する SQL Database 仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels)に関するページをご覧ください。
