---
title: 仮想コア モデルの概要
description: 仮想コア購入モデルでは、コンピューティング リソースとストレージ リソースを個別にスケーリングし、オンプレミスのパフォーマンスと一致させて、価格を最適化することができます。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 78e01c854201e3c5253cd86aebcd85b62bf5568d
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629302"
---
# <a name="vcore-model-overview"></a>仮想コア モデルの概要

仮想コア (vCore) モデルには、いくつかの利点があります。

- コンピューティング、メモリ、IO、およびストレージの上限が高くなります。
- ワークロードのコンピューティング要件とメモリ要件をより満たすようにハードウェアの世代を制御します。
- [Azure ハイブリッド特典 (AHB)](sql-database-azure-hybrid-benefit.md) および[予約インスタンス (RI)](sql-database-reserved-capacity.md) に対する料金割引。
- コンピューティングを強化するハードウェア詳細における透明性の向上。オンプレミスのデプロイからの移行計画を容易にします。

## <a name="service-tiers"></a>サービス階層

仮想コア モデルのサービス レベルのオプションには、General Purpose、Business Critical、および Hyperscale があります。 サービス レベルでは、一般に、可用性とディザスター リカバリーに関連するストレージ アーキテクチャ、容量と IO の制限、およびビジネス継続性のオプションが定義されています。

||**汎用**|**Business Critical**|**Hyperscale**|
|---|---|---|---|
|最適な用途|ほとんどのビジネス ワークロード。 予算重視で、バランスのとれた、スケーラブルなコンピューティングおよびストレージ オプションを提供します。 |複数の分離されたレプリカを使用して、障害に対する最大の回復性をビジネス アプリケーションに提供し、データベース レプリカあたりの最大の I/O パフォーマンスを実現します。|高度にスケーラブルなストレージと読み取りスケールの要件を持つほとんどのビジネス ワークロード。  複数の分離されたデータベース レプリカを構成できるようにして、障害に対するより高い回復性を提供します。 |
|ストレージ|リモート ストレージを使用します。<br/>**単一データベースとエラスティック プールにプロビジョニングされるコンピューティング**:<br/>5 GB – 4 TB<br/>**サーバーレス コンピューティング**:<br/>5 GB - 3 TB<br/>**Managed Instance**:32 GB ～ 8 TB |ローカル SSD ストレージを使用します。<br/>**単一データベースとエラスティック プールにプロビジョニングされるコンピューティング**:<br/>5 GB – 4 TB<br/>**Managed Instance**:<br/>32 GB ～ 4 TB |必要に応じた、ストレージの柔軟な自動拡張。 最大 100 TB のストレージをサポートします。 ローカル バッファー プール キャッシュとローカル データ ストレージにローカル SSD ストレージを使用します。 最終的な長期間のデータ ストアとして Azure リモート ストレージを使用します。 |
|IOPS とスループット (概算)|**単一データベースとエラスティック プール**:[単一データベース](../sql-database/sql-database-vcore-resource-limits-single-databases.md)と[エラスティック プール](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)に関するリソース制限を参照してください。<br/>**Managed Instance**:「[Azure SQL Database マネージド インスタンスのリソース制限の概要](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics)」を参照してください。|[単一データベース](../sql-database/sql-database-vcore-resource-limits-single-databases.md)と[エラスティック プール](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)に関するリソース制限を参照してください。|Hyperscale は、複数のレベルのキャッシュが存在する複数レベル アーキテクチャです。 有効な IOPS とスループットはワークロードによって異なります。|
|可用性|1 レプリカ、読み取りスケール レプリカなし|3 レプリカ、1 [読み取りスケール レプリカ](sql-database-read-scale-out.md)、<br/>ゾーン冗長高可用性 (HA)|1 読み取り/書き込みレプリカ、および 0 ～ 4 [ 読み取りスケール レプリカ ](sql-database-read-scale-out.md)|
|バックアップ|[ 読み取りアクセス geo 冗長ストレージ (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7 ～ 35 日 (既定では 7 日)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7 ～ 35 日 (既定では 7 日)|Azure リモート ストレージでのスナップショット ベースのバックアップ。 このようなスナップショットを使用して復元することで、高速復元が可能になります。 バックアップは瞬時であり、コンピューティング I/O パフォーマンスには影響を与えません。 復元は高速であり、データ サイズに左右される操作ではありません (数時間または数日ではなく、数分で完了)。|
|メモリ内|サポートされていません|サポートされています|サポートされていません|
|||


### <a name="choosing-a-service-tier"></a>サービス階層の選択

特定のワークロードに対してサービス レベルを選択する方法については、次の記事を参照してください。

- [General Purpose サービス レベルを選択する場合](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Business Critical サービス レベルを選択する場合](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Hyperscale サービス レベルを選択する場合](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>コンピューティング レベル

仮想コア モデルのコンピューティング レベル オプションには、プロビジョニングおよびサーバーレスのコンピューティング レベルが含まれます。


### <a name="provisioned-compute"></a>プロビジョニング済みコンピューティング

プロビジョニング済みコンピューティング レベルでは、ワークロード アクティビティとは無関係に、継続的にプロビジョニングされる特定の量のコンピューティング リソースを提供し、時間あたりの固定価格でプロビジョニングされたコンピューティング使用量に対して請求します。


### <a name="serverless-compute"></a>サーバーレス コンピューティング

[サーバーレス コンピューティング レベル](sql-database-serverless.md)では、ワークロード アクティビティに基づいてコンピューティング リソースを自動スケールし、1 秒あたりのコンピューティング使用量に対して請求します。



## <a name="hardware-generations"></a>ハードウェアの世代

仮想コア モデルのハードウェアの世代オプションには、Gen 4/5、M シリーズ (プレビュー)、Fsv2 シリーズ (プレビュー) があります。 ハードウェアの世代では、一般に、ワークロードのパフォーマンスに影響を与えるコンピューティングおよびメモリの制限とその他の特性を定義します。

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5 ハードウェアでは、バランスの取れたコンピューティング リソースとメモリ リソースを提供し、Fsv2 シリーズや M シリーズで提供されるような、メモリの増加、仮想コアの増加、またはより高速な単一の仮想コア要件を持たないほとんどのデータベース ワークロードに適しています。

Gen4/Gen5 が利用可能なリージョンについては、[Gen4/Gen5 の可用性](#gen4gen5-1)に関するセクションを参照してください。

### <a name="fsv2-seriespreview"></a>Fsv2 シリーズ (プレビュー)

- Fsv2 シリーズは、CPU を大量に要求するワークロードに対して、CPU の低待機時間と高クロック速度を実現するコンピューティング最適化のハードウェア オプションです。
- ワークロードによっては、Fsv2 シリーズは Gen5 よりも仮想コアあたりの CPU パフォーマンスを向上させることができます。72 vCore サイズでは、Gen5 の 80 仮想コアと比較して、より高い CPU パフォーマンスを実現し、コストを削減できます。 
- Fsv2 を使用すると、他のハードウェアよりも仮想コアあたりのメモリと tempdb が少なくなります。そのため、これらの制限の影響を受けるワークロードでは、Gen5 または M シリーズを代わりに検討する必要があります。  

Fsv2 シリーズは、General Purpose レベルでのみサポートされています。  Fsv2 シリーズが利用可能なリージョンについては、[Fsv2 シリーズの可用性](#fsv2-series)に関するセクションを参照してください。


### <a name="m-seriespreview"></a>M シリーズ (プレビュー)

- M シリーズは、Gen5 で提供されるよりも多くのメモリと高いコンピューティング制限を要求するワークロードのためのメモリ最適化のハードウェア オプションです。
- M シリーズでは、仮想コアあたり 29 GB と 128 個の仮想コアを提供し、Gen5 に比べて、メモリ制限が 8 倍の 4 TB 近くまで増加します。

M シリーズは Business Critical レベルでのみサポートされており、ゾーン冗長はサポートされていません。

サブスクリプションとリージョンで M シリーズ ハードウェアを有効にするには、サポート リクエストが開かれている必要があります。 サブスクリプションの種類は、従量課金制やマイクロソフト エンタープライズ契約 (EA) を含む有料のオファーである必要があります。  サポート リクエストが承認されると、M シリーズの選択とプロビジョニングのエクスペリエンスは、他のハードウェアの世代と同じパターンに従います。 M シリーズが利用可能なリージョンについては、[M シリーズの可用性](#m-series)に関するセクションを参照してください。


### <a name="compute-and-memory-specifications"></a>コンピューティングとメモリの仕様


|ハードウェアの世代  |Compute  |メモリ  |
|:---------|:---------|:---------|
|Gen4     |- Intel E5-2673 v3 (Haswell) 2.4 GHz プロセッサ<br>- 最大 24 個の仮想コアをプロビジョニング (1 仮想コア = 1 物理コア)  |- 仮想コアあたり 7 GB<br>- 最大 168 GB のプロビジョニング|
|第 5 世代     |**プロビジョニング済みコンピューティング**<br>- Intel E5-2673 v4 (Broadwell) 2.3 GHz および Intel SP-8160 (Skylake)* プロセッサ<br>- 最大 80 個の仮想コアをプロビジョニング (1 仮想コア = 1 ハイパースレッド)<br><br>**サーバーレス コンピューティング**<br>- Intel E5-2673 v4 (Broadwell) 2.3 GHz および Intel SP-8160 (Skylake)* プロセッサ<br>- 最大 16 個の仮想コアを自動スケール (1 仮想コア = 1 ハイパースレッド)|**プロビジョニング済みコンピューティング**<br>- 仮想コアあたり 5.1 GB<br>- 最大 408 GB をプロビジョニング<br><br>**サーバーレス コンピューティング**<br>- 仮想コアあたり最大 24 GB を自動スケール<br>- 最大 48 GB を自動スケール|
|Fsv2 シリーズ     |- Intel Xeon Platinum 8168 (SkyLake) プロセッサ<br>- すべての主要なターボ クロック速度 (3.4 GHz) と、最大 1 コアのターボ クロック速度 (3.7 GHz) を実現します。<br>- 最大 72 個の仮想コアをプロビジョニング (1 仮想コア = 1 ハイパースレッド)|- 仮想コアあたり 1.9 GB<br>- 136 GB をプロビジョニング|
|M シリーズ     |- Intel Xeon E7-8890 v3 2.5 GHz および Intel Xeon Platinum 8280M 2.7 GHz (Cascade Lake) プロセッサ<br>- 128 個の仮想コアをプロビジョニング (1 仮想コア = 1 ハイパースレッド)|- 仮想コアあたり 29 GB<br>- 3.7 TB をプロビジョニング|

\* [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 動的管理ビューでは、Intel SP-8160 (Skylake) プロセッサを使用する Gen5 データベースのハードウェアの世代が Gen6 として表示されます。 すべての Gen5 データベースのリソース制限は、プロセッサの種類 (Broadwell または Skylake) に関係なく同じです。

リソース制限の詳細については、[単一データベースに対するリソース制限 (仮想コア)](sql-database-vcore-resource-limits-single-databases.md) に関するページ、または[エラスティック プールに対するリソース制限 (仮想コア)](sql-database-vcore-resource-limits-elastic-pools.md) に関するページを参照してください。

### <a name="selecting-a-hardware-generation"></a>ハードウェアの世代を選択する

Azure portal では、作成時に SQL データベースまたはプールのハードウェアの世代を選択できます。また、既存の SQL データベースまたはプールのハードウェアの世代を変更することもできます。

**SQL データベースまたはプールを作成するときにハードウェアの世代を選択するには**

詳細については、[SQL データベースの作成](sql-database-single-database-get-started.md)に関するページを参照してください。

**[基本]** タブで、 **[Compute + storage]\(コンピューティングとストレージ\)** セクションの **[データベースの構成]** リンクを選択し、 **[構成の変更]** リンクを選択します。

  ![configure database](media/sql-database-service-tiers-vcore/configure-sql-database.png)

目的のハードウェアの世代を選択します。

  ![ハードウェアの選択](media/sql-database-service-tiers-vcore/select-hardware.png)


**既存の SQL データベースまたはプールのハードウェアの世代を変更するには**

データベースの場合は、[概要] ページで、 **[価格レベル]** リンクを選択します。

  ![ハードウェアの変更](media/sql-database-service-tiers-vcore/change-hardware.png)

プールの場合は、[概要] ページで **[構成]** を選択します。

手順に従って構成を変更し、前の手順で説明したようにハードウェアの世代を選択します。

**マネージドインスタンスの作成時にハードウェア世代を選択するには**

詳細については、[マネージドインスタンスを作成する](sql-database-managed-instance-get-started.md) に関するページを参照してください。

**[基本]** タブで、 **[Compute + storage]** セクションの **[データベースの構成]** リンクを選択し、使用するハードウェア世代を選択します：

  ![マネージドインスタンスを構成する](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**既存のマネージドインスタンスのハードウェア世代を変更するには**

# <a name="portal"></a>[ポータル](#tab/azure-portal)

マネージド インスタンスのページで、[設定] セクションの下にある **[価格レベル]** リンクを選択します

![マネージド インスタンスのハードウェアを変更する](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

**[価格レベル]** ページで、前の手順で説明したようにハードウェア世代を変更できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次の PowerShell スクリプトを使用します：

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

詳細については [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) コマンドを調べてください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の CLI コマンドを使用します。

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

詳細については [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) コマンドを調べてください。

---

### <a name="hardware-availability"></a>ハードウェアの可用性

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

Gen4 ハードウェアは[段階的に廃止中](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)であり、新しいデプロイでは利用できなくなりました。 すべての新しいデータベースを Gen5 ハードウェアにデプロイする必要があります。

Gen5 は、世界中のほとんどのリージョンで使用できます。

#### <a name="fsv2-series"></a>Fsv2 シリーズ

Fsv2 シリーズは、次のリージョンで使用できます: オーストラリア中部、オーストラリア中部 2、オーストラリア東部、オーストラリア南東部、ブラジル南部、カナダ中部、東アジア、米国東部、フランス中部、インド中部、インド西部、韓国中部、韓国南部、北ヨーロッパ、南アフリカ北部、東南アジア、英国南部、英国西部、西ヨーロッパ、米国西部 2。


#### <a name="m-series"></a>M シリーズ

M シリーズは、次のリージョンで使用できます: 米国東部、北ヨーロッパ、西ヨーロッパ、米国西部 2。
また、M シリーズは、追加のリージョンでの使用が制限される場合があります。 ここに記載されているものとは別のリージョンを要求できますが、別のリージョンでのフルフィルメントができない場合があります。

サブスクリプションで M シリーズの可用性を有効にするには、[新しいサポート リクエストをファイリング](#create-a-support-request-to-enable-m-series)してアクセスを要求する必要があります。


##### <a name="create-a-support-request-to-enable-m-series"></a>M シリーズを有効にするサポート リクエストを作成します。 

1. ポータルで **[ヘルプとサポート]** を選択します。
2. **[新しいサポート リクエスト]** を選択します。

**[基本]** ページで、以下を設定します。

1. **[問題の種類]** で、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
2. **[サブスクリプション]** で、M シリーズを有効にするサブスクリプションを選択します。
3. **[クォータの種類]** で、 **[SQL データベース]** を選択します。
4. **[次へ]** を選択して、 **[詳細]** ページに移動します。

**[詳細]** ページで、以下を設定します。

1. **[問題の詳細]** セクションで、 **[詳細の指定]** リンクを選択します。 
2. **[SQL Database のクォータの種類]** で、 **[M シリーズ]** を選択します。
3. **[リージョン]** で、M シリーズを有効にするリージョンを選択します。
    M シリーズが利用可能なリージョンについては、[M シリーズの可用性](#m-series)に関するセクションを参照してください。

承認されたサポート リクエストは、通常、5 営業日以内に完了します。


## <a name="next-steps"></a>次のステップ

- SQL データベースを作成するには、[Azure portal を使用して SQL データベースを作成する方法](sql-database-single-database-get-started.md)に関するページを参照してください。
- 単一データベースに対して使用できる特定のコンピューティング サイズとストレージ サイズの選択については、[単一データベースに対する SQL Database の仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md)に関するページを参照してください。
- エラスティック プールに対して使用できる特定のコンピューティング サイズとストレージ サイズの選択については、[エラスティック プールに対する SQL Database の仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md)に関するページを参照してください。
- 価格設定の詳細については、[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/single/)に関するページを参照してください。
