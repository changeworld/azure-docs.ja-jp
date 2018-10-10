---
title: Azure SQL Database サービス - 仮想コア | Microsoft Docs
description: 仮想コア ベースの購入モデルでは、コンピューティングおよびストレージ リソースを個別にスケーリングし、オンプレミスのパフォーマンスを一致させて、コストを最適化できます。
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
ms.date: 09/26/2018
ms.openlocfilehash: 3fb1357b7a70579fa527a896d5bd359749b10ad6
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407657"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>仮想コアのサービス レベル、コンピューティング、メモリ、ストレージ、および IO リソースの選択

仮想コア ベースの購入モデルでは、コンピューティングおよびストレージ リソースを個別にスケーリングし、オンプレミスのパフォーマンスを一致させて、コストを最適化できます。 ハードウェアの世代を選択することもできます。
- Gen 4 - Intel E5-2673 v3 (Haswell) 2.4 GHz プロセッサに基づく最大 24 個の論理 CPU、仮想コア = 1 PP (物理コア)、7 GB/コア、接続されている SSD
- Gen 5 - Intel E5-2673 v4 (Broadwell) 2.3 GHz プロセッサに基づく最大 80 個の論理 CPU、仮想コア = 1 LP (ハイパースレッド)、5.5  GB/コア、高速 eNVM SSD

また、仮想コア モデルでは、[SQL Server 向けの Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)を使用して、コストを削減することもできます。

## <a name="service-tier-characteristics"></a>サービス レベルの特性

仮想コア モデルでは、General Purpose と Business Critical の 2 つのサービス レベルが提供されます。 サービス レベルは、さまざまなコンピューティング サイズ、高可用性の設計、障害の分離、ストレージの種類、および IO 範囲によって区別されます。 お客様は、バックアップ用に必要なストレージと保有期間を個別に構成する必要があります。

次の表は、この 2 つのレベルの違いを把握するうえで役立ちます。

||**汎用**|**Business Critical**|**ハイパースケール (プレビュー)**|
|---|---|---|---|
|最適な用途|ほとんどのビジネス ワークロード。 予算重視のスケーラブルでバランスの取れたコンピューティングおよびストレージ オプションを提供します。|IO 要件の高いビジネス アプリケーション。 分離された複数のレプリカを使用して、最高の耐障害性が提供されます。|高度にスケーラブルなストレージと読み取りスケールの要件を持つほとんどのビジネス ワークロード|
|コンピューティング|Gen4: 1 ～ 24 仮想コア<br/>Gen5: 1 ～ 80 仮想コア|Gen4: 1 ～ 24 仮想コア<br/>Gen5: 1 ～ 80 仮想コア|Gen4: 1 ～ 24 仮想コア<br/>Gen5: 1 ～ 80 仮想コア|
|メモリ|Gen4: コアあたり 7 GB<br>Gen5: コアあたり 5.5 GB | Gen4: コアあたり 7 GB<br>Gen5: コアあたり 5.5 GB |Gen4: コアあたり 7 GB<br>Gen5: コアあたり 5.5 GB|
|Storage|[Premium リモート ストレージ](../virtual-machines/windows/premium-storage.md)<br/>単一データベース: 5 GB から 4 TB<br/>Managed Instance: 32 GB ～ 8 TB |ローカル SSD ストレージ<br/>単一データベース: 5 GB から 4 TB<br/>Managed Instance: 32 GB ～ 4 TB |柔軟性が高く、必要に応じて自動拡張されるストレージ。 最大 100 TB のストレージなどをサポートします。 ローカル バッファー プール キャッシュとローカル データ ストレージ用のローカル SSD ストレージ。 最終的な長期データ ストアとしての Azure リモート ストレージ。 |
|IO スループット (概算)|単一データベース: 仮想コアあたり 500 IOPS (最大 7,000 IOPS)</br>Managed Instance: [ファイルのサイズ](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)に依存|コアあたり 5000 IOPS (最大 200,000 IOPS)|TBD|
|可用性|1 レプリカ、読み取りスケールなし|3 レプリカ、1 [読み取りスケール レプリカ](sql-database-read-scale-out.md)、<br/>ゾーン冗長 HA|?|
|バックアップ|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7 ～ 35 日 (既定では 7 日)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7 ～ 35 日 (既定では 7 日)|Azure リモート ストレージおよび復元のスナップショットベースのバックアップは、高速復旧のためにこのようなスナップショットを使用します。 バックアップは瞬時に行われ、Compute の IO パフォーマンスには影響しません。 復元はとても高速で、データ操作の規模ではありません (数時間/日ではなく分単位)。|
|インメモリ|サポートされていません|サポートされています|サポートされていません|
|||

詳細については、[単一データベースでの仮想コア リソースの制限](sql-database-vcore-resource-limits-single-databases.md)および [Managed Instance での仮想コア リソースの制限](sql-database-managed-instance.md#vcore-based-purchasing-model)に関するページを参照してください。 

> [!IMPORTANT]
> 必要なコンピューティング能力が仮想コア 1 つ分を下回る場合は、DTU ベースの購入モデルを使用します。

よく寄せられる質問の回答については、「[SQL Database に関する FAQ](sql-database-faq.md)」を参照してください。 

## <a name="storage-considerations"></a>ストレージに関する考慮事項

### <a name="general-purpose-and-business-critical-service-tiers"></a>General Purpose および Business Critical サービス レベル
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

### <a name="hyperscale-service-tier-preview"></a>ハイパースケール サービス レベル (プレビュー)

ストレージは、ハイパースケール データベースに合わせて自動管理されます。 必要に応じてストレージは大きくなります。 高速な Azure Premium Storage SSD 上の "無限のログ" ストレージです。頻繁なログの切り捨ては不要です。

## <a name="backups-and-storage"></a>バックアップとストレージ

### <a name="general-purpose-and-business-critical-service-tiers"></a>General Purpose および Business Critical サービス レベル

データベース バックアップのストレージは、SQL Database のポイントインタイム リストア (PITR) および[長期リテンション期間 (LTR)](sql-database-long-term-retention.md) 機能をサポートするために割り当てられます。 このストレージはデータベースごとに別個に割り当てられ、データベース料金ごとに 2 つが個々に課金されます。 

- **PITR**: 個々のデータベース バックアップが、[RA-GRS ストレージ](../storage/common/storage-designing-ha-apps-with-ragrs.md)に自動的にコピーされます。 ストレージのサイズは、新しいバックアップが作成されるにつれ、動的に増加します。  このストレージは、毎週の完全バックアップ、毎日の差分バックアップ、5 分ごとにコピーされるトランザクション ログ バックアップによって使用されます。 ストレージの使用量は、データベースの変化率とリテンション期間によって異なります。 リテンション期間は、データベースごとに 7 ～ 35 日の範囲内で別々に構成できます。 データ サイズと同量の最小ストレージ容量が、追加料金なしで提供されます。 ほとんどのデータベースでは、この容量で十分に 7 日間のバックアップを格納できます。
- **LTR**: SQL Database には、最大 10 年の完全バックアップの長期リテンション期間を構成するオプションが用意されています。 LTR ポリシーが有効になっている場合、これらのバックアップは、RA-GRS ストレージに自動的に格納されますが、バックアップがコピーされる頻度は制御できます。 さまざまなコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対して異なったリテンション期間を選択することができます。 この構成によって、LTR バックアップに使用されるストレージ容量が定義されます。 LTR ストレージのコストは、LTR 料金計算ツールを使用して見積もることができます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。

### <a name="hyperscale-service-tier-preview"></a>ハイパースケール サービス レベル (プレビュー)

ハイパースケール サービス レベルでは、バックアップはスナップショットベースであり、Azure リモート ストレージに格納されます。 このようなスナップショットを使用して復元することで、高速復元が可能になります。 バックアップは瞬時に行われ、Compute の IO パフォーマンスには影響しません。 復元はとても高速で、データ操作の規模ではありません (数時間/日ではなく分単位)。

## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

仮想コアベースの購入モデルでは、[SQL Server 向け Azure ハイブリッド使用特典](../virtual-machines/windows/hybrid-use-benefit-licensing.md)を利用して、お使いの既存のライセンスを SQL Database の割引料金のライセンスに交換できます。 この Azure 特典では、オンプレミスのソフトウェア アシュアランス付き SQL Server ライセンスを利用することで、オンプレミスの SQL Server ライセンスで Azure SQL Database の料金が最大 30% オフになります。

![価格](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-from-dtu-model-to-vcore-model"></a>DTU モデルから仮想コア モデルへの移行

### <a name="migration-of-single-databases-with-geo-replication-links"></a>geo レプリケーション リンクを含む単一データベースの移行

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

### <a name="migration-of-failover-groups"></a>フェールオーバー グループの移行 

複数のデータベースが含まれるフェールオーバー グループの移行では、プライマリ データベースとセカンダリ データベースを個別に移行する必要があります。 その処理中は、同じ考慮事項とシーケンス処理ルールが適用されます。 データベースが仮想コアベースのモデルに変換された後も、フェールオーバー グループには、同じポリシー設定が引き続き有効になります。 

### <a name="creation-of-a-geo-replication-secondary"></a>geo レプリケーション セカンダリの作成

プライマリと同じサービス レベルを使用して、geo セカンダリのみを作成できます。 ログ生成率が高いデータベースの場合、プライマリと同じコンピューティング サイズでセカンダリを作成することをお勧めします。 単一プライマリ データベースに対してエラスティック プールに geo セカンダリを作成する場合、そのプールの `maxVCore` 設定を、プライマリ データベースのコンピューティング サイズと一致させることをお勧めします。 geo セカンダリの作成先エラスティック プールと、そのプライマリがあるエラスティック プールが異なる場合、その 2 つのプールの `maxVCore` 設定を同じにすることをお勧めします

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>データベースのコピーを使用した、DTU ベースのデータベースから仮想コアベースのデータベースへの変換

DTU ベース コンピューティング サイズのデータベースを、仮想コアベース コンピューティング サイズのデータベースにコピーする場合、コピー先のコンピューティング サイズが、コピー元データベースの最大データベース サイズをサポートしている限り、制限や特別なシーケンス処理は伴いません。 これは、データベースのコピーによって、コピー操作を開始した時点のデータ スナップショットが作成されるので、コピー元とコピー先の間でデータ同期が行われないためです。 

## <a name="next-steps"></a>次の手順

- 単一データベースに対して選択できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[単一データベースに対する SQL Database の仮想コアベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)に関するページを参照してください。
- エラスティック プールに対して選択できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[エラスティック プールに対する SQL Database 仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)に関するページを参照してください。
