---
title: 仮想コア購入モデル
description: 仮想コア購入モデルでは、コンピューティングとストレージの各リソースを個別にスケーリングし、オンプレミスのパフォーマンスと一致させて、Azure SQL Managed Instance 用に価格を最適化することができます。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 05/18/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d5a67a12d6dbdb72625ab0459767eb8be92a8241
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131035986"
---
# <a name="azure-sql-managed-instance---compute-hardware-in-the-vcore-service-tier"></a>Azure SQL Managed Instance - 仮想コア サービス レベルのコンピューティング ハードウェア
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) 用の仮想コア購入モデルを確認します。 仮想コア購入モデルか DTU 購入モデルかの選択の詳細については、[仮想コア購入モデルか DTU 購入モデルかの選択](../database/purchasing-models.md)に関するページをご覧ください。

Azure SQL Managed Instance で使用される仮想コア (vCore) 購入モデルには、次の特性があります。

- ワークロードのコンピューティング要件とメモリ要件をより満たすようにハードウェアの世代を制御します。
- [Azure ハイブリッド特典 (AHB)](../azure-hybrid-benefit.md) および[予約インスタンス (RI)](../database/reserved-capacity-overview.md) に対する料金割引。
- コンピューティングを強化するハードウェア詳細における透明性の向上。それが、オンプレミスのデプロイからの移行計画を容易にします。
- [予約インスタンスの価格](../database/reserved-capacity-overview.md)は、仮想コア購入モデルでのみ使用できます。 

## <a name="service-tiers"></a><a id="compute-tiers"></a>サービス階層

仮想コア購入モデルのサービス レベルのオプションには、General Purpose および Business Critical があります。 サービス レベルでは一般に、ストレージ アーキテクチャ、容量と I/O の制限、および可用性とディザスター リカバリーに関連するビジネス継続性のオプションが定義されています。

|**ユース ケース**|**汎用**|**Business Critical**|
|---|---|---|
|最適な用途|ほとんどのビジネス ワークロード。 予算重視で、バランスのとれた、スケーラブルなコンピューティングおよびストレージ オプションを提供します。 |複数の分離されたレプリカを使用して、障害に対する最大の回復性をビジネス アプリケーションに提供し、最大の I/O パフォーマンスを実現します。|
|ストレージ|リモート ストレージを使用します。 コアの数に応じて 32 GB から 16 TB |ローカル SSD ストレージを使用します。 <BR>- **Standard シリーズ (Gen5):** 32 GB から 4 TB <BR>- **Premium シリーズ:** 32 GB から 5.5 TB <BR>- **メモリ最適化 Premium シリーズ:** 32 GB から 16 TB |
|IOPS とスループット (概算)|[Azure SQL Managed Instance のリソース制限の概要](../managed-instance/resource-limits.md#service-tier-characteristics)に関するページを参照してください。|[Azure SQL Managed Instance のリソース制限の概要](../managed-instance/resource-limits.md#service-tier-characteristics)に関するページを参照してください。|
|可用性|1 レプリカ、読み取りスケール レプリカなし|合計 4 レプリカ、1 つの[読み取りスケール レプリカ](../database/read-scale-out.md)、<br/> 2 つの高可用性レプリカ (HA)|
|バックアップ|[読み取りアクセス geo 冗長ストレージ (RA-GRS)](../../storage/common/geo-redundant-design.md)、1 ～ 35 日 (既定では 7 日)|[RA-GRS](../../storage/common/geo-redundant-design.md)、1 ～ 35 日 (既定では 7 日)|
|メモリ内|サポートされていません|サポートされています|
||||

### <a name="choosing-a-service-tier"></a>サービス階層の選択

特定のワークロードに対してサービス レベルを選択する方法については、次の記事を参照してください。

- [General Purpose サービス レベルを選択する場合](../database/service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Business Critical サービス レベルを選択する場合](../database/service-tier-business-critical.md#when-to-choose-this-service-tier)

## <a name="compute"></a>コンピューティング

SQL Managed Instance コンピューティングでは、ワークロード アクティビティとは無関係に、継続的にプロビジョニングされる特定の量のコンピューティング リソースを提供し、時間あたりの固定価格でプロビジョニングされたコンピューティング使用量に対して請求します。

## <a name="hardware-generations"></a>ハードウェアの世代

仮想コアモデルのハードウェア世代オプションには、Standard シリーズ (Gen5)、Premium シリーズ、メモリ最適化 Premium シリーズの各ハードウェア世代が含まれます。 ハードウェアの世代では、一般に、ワークロードのパフォーマンスに影響を与えるコンピューティングおよびメモリの制限とその他の特性を定義します。

ハードウェアの世代の詳細と制限事項について詳しくは、「[ハードウェアの世代の特性](resource-limits.md#hardware-generation-characteristics)」をご覧ください。

 [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 動的管理ビューでは、Intel&reg; SP-8160 (Skylake) プロセッサを使用するインスタンスのハードウェア世代は Gen6 と表示され、Intel&reg; 8272CL (Cascade Lake) を使用するインスタンスのハードウェア世代は Gen7 と表示されます。 Premium シリーズとメモリ最適化 Premium シリーズのハードウェア世代で使われる Intel&reg; 8370C (Ice Lake) CPU は、Gen8 として示されます。 Standard シリーズ (Gen5) のすべてのインスタンスに対するリソース制限は、プロセッサの種類 (Broadwell、Skylake、Cascade Lake) に関係なく同じです。

### <a name="selecting-a-hardware-generation"></a>ハードウェアの世代を選択する

Azure portal では、作成時にハードウェア世代を選択できます。また、既存の SQL Managed Instance のハードウェア世代を変更することもできます。

**SQL Managed Instance の作成時にハードウェア世代を選択するには**

詳細については、[SQL Managed Instance の作成](../managed-instance/instance-create-quickstart.md)に関するページを参照してください。

**[基本]** タブで、 **[Compute + storage]** セクションの **[データベースの構成]** リンクを選択し、使用するハードウェア世代を選択します：

:::image type="content" source="../database/media/service-tiers-vcore/configure-managed-instance.png" alt-text="SQL Managed Instance を構成する"  loc-scope="azure-portal":::
  
**既存の SQL Managed Instance のハードウェア世代を変更するには**

#### <a name="the-azure-portal"></a>[Azure ポータル](#tab/azure-portal)

SQL Managed Instance のページで、[設定] セクションの下にある **[価格レベル]** リンクを選択します

:::image type="content" source="../database/media/service-tiers-vcore/change-managed-instance-hardware.png" alt-text="SQL Managed Instance のハードウェアを変更する"  loc-scope="azure-portal":::

[価格レベル] ページで、前の手順で説明したようにハードウェア世代を変更できます。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次の PowerShell スクリプトを使用します：

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

詳細については [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) コマンドを調べてください。

#### <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の CLI コマンドを使用します。

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

詳細については [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update) コマンドを調べてください。

---

### <a name="hardware-availability"></a>ハードウェアの可用性

#### <a name="gen4"></a><a id="gen4gen5-1"></a> Gen4

Gen4 ハードウェアは[段階的に廃止中](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)であり、新しいデプロイでは利用できなくなりました。 新しいインスタンスはすべて、それより後のハードウェア世代に展開する必要があります。

#### <a name="standard-series-gen5-and-premium-series"></a>Standard シリーズ (Gen5) と Premium シリーズ

Standard シリーズ (Gen5) のハードウェアは、世界中のすべてのパブリック リージョンで利用できます。
  
Premium シリーズとメモリ最適化 Premium シリーズのハードウェアはプレビュー段階であり、利用できるリージョンは限られています。 詳しくは、[Azure SQL Managed Instance のリソース制限](../managed-instance/resource-limits.md#hardware-generation-characteristics)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

- 開始するには、[Azure portal を使用して SQL Managed Instance を作成する](instance-create-quickstart.md)に関するページを参照してください
- 価格の詳細については、以下を参照してください 
    - [Azure SQL マネージド単一インスタンスの価格に関するページ](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Azure SQL マネージド単一インスタンス プールの価格に関するページ](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
- General Purpose と Business Critical サービス レベルで利用できる具体的なコンピューティングおよびストレージのサイズの詳細については、[Azure SQL Managed Instance の仮想コアベースのリソース制限](resource-limits.md)に関するページを参照してください。
