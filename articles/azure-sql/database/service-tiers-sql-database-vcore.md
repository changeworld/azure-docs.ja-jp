---
title: 仮想コア購入モデル
description: 仮想コア購入モデルでは、コンピューティング リソースとストレージ リソースを個別にスケーリングし、オンプレミスのパフォーマンスと一致させて、Azure SQL Database 用に価格を最適化することができます
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 09/10/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: fb168fad05acf84fd9b64f34c929176933a1fd2f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026034"
---
# <a name="vcore-purchase-model-overview---azure-sql-database"></a>仮想コア購入モデルの概要 - Azure SQL Database 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、[Azure SQL Database](sql-database-paas-overview.md) 用の仮想コア購入モデルを確認します。 仮想コア購入モデルか DTU 購入モデルかの選択の詳細については、[仮想コア購入モデルか DTU 購入モデルかの選択](purchasing-models.md)に関するページをご覧ください。

Azure SQL Database によって使用される仮想コア購入モデルには、DTU 購入モデルに比べて利点がいくつかあります。

- コンピューティング、メモリ、I/O、およびストレージの上限が高くなります。
- ワークロードのコンピューティング要件とメモリ要件をより満たすようにハードウェアの世代を制御します。
- [Azure ハイブリッド特典 (AHB)](../azure-hybrid-benefit.md) の料金割引があります。
- コンピューティングを強化するハードウェア詳細における透明性の向上。それが、オンプレミスのデプロイからの移行計画を容易にします。
- [予約インスタンスの価格](reserved-capacity-overview.md)は、仮想コア購入モデルでのみ使用できます。 

## <a name="service-tiers"></a>サービス階層

仮想コア購入モデルのサービス レベルのオプションには、General Purpose、Business Critical、Hyperscale があります。 サービス レベルでは一般に、ストレージ アーキテクチャ、容量と I/O の制限、および可用性とディザスター リカバリーに関連するビジネス継続性のオプションが定義されています。

|**ユース ケース**|**汎用**|**Business Critical**|**Hyperscale**|
|---|---|---|---|
|最適な用途|ほとんどのビジネス ワークロード。 予算重視で、バランスのとれた、スケーラブルなコンピューティングおよびストレージ オプションを提供します。 |複数の分離されたレプリカを使用して、障害に対する最大の回復性をビジネス アプリケーションに提供し、データベース レプリカあたりの最大の I/O パフォーマンスを実現します。|高度にスケーラブルなストレージと読み取りスケールの要件を持つほとんどのビジネス ワークロード。  複数の分離されたデータベース レプリカを構成できるようにして、障害に対するより高い回復性を提供します。 |
|ストレージ|リモート ストレージを使用します。<br/>**SQL Database がプロビジョニングされたコンピューティング**:<br/>5 GB – 4 TB<br/>**サーバーレス コンピューティング**:<br/>5 GB - 3 TB|ローカル SSD ストレージを使用します。<br/>**SQL Database がプロビジョニングされたコンピューティング**:<br/>5 GB – 4 TB|必要に応じた、ストレージの柔軟な自動拡張。 最大 100 TB のストレージをサポートします。 ローカル バッファー プール キャッシュとローカル データ ストレージにローカル SSD ストレージを使用します。 最終的な長期間のデータ ストアとして Azure リモート ストレージを使用します。 |
|IOPS とスループット (概算)|**SQL Database**:[単一データベース](resource-limits-vcore-single-databases.md)と [エラスティック プール](resource-limits-vcore-elastic-pools.md)に関するリソース制限を参照してください。|[単一データベース](resource-limits-vcore-single-databases.md)と[エラスティック プール](resource-limits-vcore-elastic-pools.md)に関するリソース制限を参照してください。|Hyperscale は、複数のレベルのキャッシュが存在する複数レベル アーキテクチャです。 有効な IOPS とスループットはワークロードによって異なります。|
|可用性|1 レプリカ、読み取りスケール レプリカなし、 <br/>ゾーン冗長高可用性 (HA) (プレビュー)|3 レプリカ、1 [読み取りスケール レプリカ](read-scale-out.md)、<br/>ゾーン冗長高可用性 (HA)|1 読み取り/書き込みレプリカ、および 0 ～ 4 [ 読み取りスケール レプリカ ](read-scale-out.md)|
|バックアップ|geo 冗長、ゾーン冗長\*、またはローカル冗長\*のバックアップ ストレージから選択、1 から 35 日の保持期間 (既定値は 7 日)|geo 冗長、ゾーン冗長\*、またはローカル冗長\*のバックアップ ストレージから選択、1 から 35 日の保持期間 (既定値は 7 日)|geo 冗長、ゾーン冗長\*\*、またはローカル冗長\*\*のバックアップ ストレージから選択、7 日の保持期間。<p>Azure リモート ストレージでのスナップショット ベースのバックアップ。 スナップショットを使用して復元することで、高速復旧が可能になります。 バックアップは瞬時であり、コンピューティング I/O パフォーマンスには影響を与えません。 復元は高速であり、データ サイズに左右される操作ではありません (数時間ではなく、数分で完了)。|
|メモリ内|サポートされていません|サポートされています|部分的なサポート。 メモリ最適化テーブル型、テーブル変数、ネイティブ コンパイル モジュールがサポートされています。|
|||

\* プレビュー段階

\*\* プレビュー段階、新しい Hyperscale データベースのみ

### <a name="choosing-a-service-tier"></a>サービス階層の選択

特定のワークロードに対してサービス レベルを選択する方法については、次の記事を参照してください。

- [General Purpose サービス レベルを選択する場合](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Business Critical サービス レベルを選択する場合](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Hyperscale サービス レベルを選択する場合](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>コンピューティング レベル

仮想コア モデルのコンピューティング レベル オプションには、プロビジョニングおよびサーバーレスのコンピューティング レベルが含まれます。


### <a name="provisioned-compute"></a>プロビジョニング済みコンピューティング

プロビジョニング済みコンピューティング レベルでは、ワークロード アクティビティとは無関係に、継続的にプロビジョニングされる特定の量のコンピューティング リソースを提供し、時間あたりの固定価格でプロビジョニングされたコンピューティング使用量に対して請求します。


### <a name="serverless-compute"></a>サーバーレス コンピューティング

[サーバーレス コンピューティング レベル](serverless-tier-overview.md)では、ワークロード アクティビティに基づいてコンピューティング リソースを自動スケールし、1 秒あたりのコンピューティング使用量に対して請求します。



## <a name="hardware-generations"></a>ハードウェアの世代

仮想コア モデルのハードウェアの世代オプションには、Gen 4/5、M シリーズ、Fsv2 シリーズ、DC シリーズがあります。 ハードウェアの世代では、一般に、ワークロードのパフォーマンスに影響を与えるコンピューティングおよびメモリの制限とその他の特性を定義します。

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5 ハードウェアでは、バランスの取れたコンピューティング リソースとメモリ リソースを提供し、Fsv2 シリーズや M シリーズで提供されるような、メモリの増加、仮想コアの増加、またはより高速な単一の仮想コア要件を持たないほとんどのデータベース ワークロードに適しています。

Gen4/Gen5 が利用可能なリージョンについては、[Gen4/Gen5 の可用性](#gen4gen5-1)に関するセクションを参照してください。

### <a name="fsv2-series"></a>Fsv2 シリーズ

- Fsv2 シリーズは、CPU を大量に要求するワークロードに対して、CPU の低待機時間と高クロック速度を実現するコンピューティング最適化のハードウェア オプションです。
- ワークロードによっては、Fsv2 シリーズは Gen5 よりも仮想コアあたりの CPU パフォーマンスを向上させることができます。72 vCore サイズでは、Gen5 の 80 仮想コアと比較して、より高い CPU パフォーマンスを実現し、コストを削減できます。 
- Fsv2 を使用すると、他のハードウェアよりも仮想コアあたりのメモリと tempdb が少なくなります。そのため、これらの制限の影響を受けるワークロードでは、Gen5 または M シリーズを代わりに検討する必要があります。  

Fsv2 シリーズは、General Purpose レベルでのみサポートされています。 Fsv2 シリーズが利用可能なリージョンについては、[Fsv2 シリーズの可用性](#fsv2-series-1)に関するセクションを参照してください。

### <a name="m-series"></a>M シリーズ

- M シリーズは、Gen5 で提供されるよりも多くのメモリと高いコンピューティング制限を要求するワークロードのためのメモリ最適化のハードウェア オプションです。
- M シリーズでは、仮想コアあたり 29 GB と最大 128 個の仮想コアを提供し、Gen5 に比べて、メモリ制限が 8 倍の 4 TB 近くまで増加します。

M シリーズは Business Critical レベルでのみサポートされており、ゾーン冗長はサポートされていません。  M シリーズが利用可能なリージョンについては、[M シリーズの可用性](#m-series-1)に関するセクションを参照してください。

#### <a name="azure-offer-types-supported-by-m-series"></a>M シリーズでサポートされている Azure オファーの種類

M シリーズにアクセスするには、サブスクリプションの種類を、従量課金制やマイクロソフト エンタープライズ契約 (EA) を含む有料のオファーにする必要があります。  M シリーズでサポートされている Azure オファーの種類の一覧については、[使用制限のない現在のオファー](https://azure.microsoft.com/support/legal/offer-details)を参照してください。

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>DC シリーズ

- DC シリーズのハードウェアでは、Software Guard Extensions (Intel SGX) テクノロジを搭載した Intel プロセッサが使用されています。
- DC シリーズは、[セキュリティで保護されたエンクレーブが設定された Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) のために必要で、これは他のハードウェア構成ではサポートされていません。
- DC シリーズは、セキュリティで保護されたエンクレーブが設定された Always Encrypted によって提供される、機密データを処理して機密クエリ処理機能を必要とするワークロード用に設計されています。
- DC シリーズのハードウェアでは、バランスの取れたコンピューティングおよびメモリ リソースが提供されます。

DC シリーズは、プロビジョニング済みコンピューティングでのみサポートされており (サーバーレスはサポートされていません)、またゾーン冗長性はサポートされていません。 DC シリーズが利用可能なリージョンについては、[DC シリーズの可用性](#dc-series-1)に関するセクションを参照してください。

#### <a name="azure-offer-types-supported-by-dc-series"></a>DC シリーズでサポートされている Azure オファーの種類

DC シリーズにアクセスするには、サブスクリプションの種類を、従量課金制やマイクロソフト エンタープライズ契約 (EA) を含む有料のオファーにする必要があります。  DC シリーズでサポートされている Azure オファーの種類の一覧については、[使用制限のない現在のオファー](https://azure.microsoft.com/support/legal/offer-details)を参照してください。

### <a name="compute-and-memory-specifications"></a>コンピューティングとメモリの仕様


|ハードウェアの世代  |Compute  |メモリ  |
|:---------|:---------|:---------|
|Gen4     |- Intel&reg; E5-2673 v3 (Haswell) 2.4 GHz プロセッサ<br>- 最大 24 個の仮想コアをプロビジョニング (1 仮想コア = 1 物理コア)  |- 仮想コアあたり 7 GB<br>- 最大 168 GB のプロビジョニング|
|第 5 世代     |**プロビジョニング済みコンピューティング**<br>- Intel&reg; E5-2673 v4 (Broadwell) 2.3-GHz プロセッサ、Intel&reg; SP-8160 (Skylake)\* プロセッサ、および Intel&reg; 8272CL (Cascade Lake) 2.5 GHz\* プロセッサ<br>- 最大 80 個の仮想コアをプロビジョニング (1 仮想コア = 1 ハイパースレッド)<br><br>**サーバーレス コンピューティング**<br>- Intel&reg; E5-2673 v4 (Broadwell) 2.3 GHz および Intel&reg; SP-8160 (Skylake)* プロセッサ<br>- 最大 40 個の仮想コアを自動スケール (1 仮想コア = 1 ハイパースレッド)|**プロビジョニング済みコンピューティング**<br>- 仮想コアあたり 5.1 GB<br>- 最大 408 GB をプロビジョニング<br><br>**サーバーレス コンピューティング**<br>- 仮想コアあたり最大 24 GB を自動スケール<br>- 最大 120 GB を自動スケール|
|Fsv2 シリーズ     |- Intel&reg; 8168 (Skylake) プロセッサ<br>- すべての主要なターボ クロック速度 (3.4 GHz) と、最大 1 コアのターボ クロック速度 (3.7 GHz) を実現します。<br>- 最大 72 個の仮想コアをプロビジョニング (1 仮想コア = 1 ハイパースレッド)|- 仮想コアあたり 1.9 GB<br>- 最大 136 GB をプロビジョニング|
|M シリーズ     |- Intel&reg; E7-8890 v3 2.5 GHz プロセッサおよび Intel&reg; 8280M 2.7 GHz (Cascade Lake) プロセッサ<br>- 最大 128 個の仮想コアをプロビジョニング (1 仮想コア = 1 ハイパースレッド)|- 仮想コアあたり 29 GB<br>- 最大 3.7 TB をプロビジョニング|
|DC シリーズ     | - Intel XEON E-2288G プロセッサ<br>- Intel Software Guard Extension (Intel SGX) を搭載<br>- 最大 8 個の仮想コアをプロビジョニング (1 仮想コア = 1 物理コア) | 仮想コアあたり 4.5 GB |

\* [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 動的管理ビューでは、Intel&reg; SP-8160 (Skylake) プロセッサを使用するデータベースのハードウェア世代は Gen6 と表示され、Intel&reg; 8272CL (Cascade Lake) を使用するデータベースのハードウェア世代は Gen7 と表示されます。 すべての Gen5 データベースのリソース制限は、プロセッサの種類 (Broadwell、Skylake、Cascade Lake) に関係なく同じです。

リソース制限の詳細については、[単一データベースに対するリソース制限 (仮想コア)](resource-limits-vcore-single-databases.md) に関するページ、または[エラスティック プールに対するリソース制限 (仮想コア)](resource-limits-vcore-elastic-pools.md) に関するページを参照してください。

### <a name="selecting-a-hardware-generation"></a>ハードウェアの世代を選択する

Azure portal では、SQL Database でデータベースまたはプールの作成時にハードウェアの世代を選択できます。また、既存のデータベースまたはプールのハードウェアの世代を変更することもできます。

**SQL Database またはプールを作成するときにハードウェアの世代を選択するには**

詳細については、[SQL Database の作成](single-database-create-quickstart.md)に関するページを参照してください。

**[基本]** タブで、 **[Compute + storage]\(コンピューティングとストレージ\)** セクションの **[データベースの構成]** リンクを選択し、 **[構成の変更]** リンクを選択します。

:::image type="content" source="./media/service-tiers-vcore/configure-sql-database.png" alt-text="SQL データベースの構成" loc-scope="azure-portal":::

目的のハードウェアの世代を選択します。

:::image type="content" source="./media/service-tiers-vcore/select-hardware.png" alt-text="SQL データベースのハードウェアの選択" loc-scope="azure-portal":::

**既存の SQL Database またはプールのハードウェアの世代を変更するには**

データベースの場合は、[概要] ページで、 **[価格レベル]** リンクを選択します。

:::image type="content" source="./media/service-tiers-vcore/change-hardware.png" alt-text="SQL Database のハードウェアの変更" loc-scope="azure-portal":::

プールの場合は、[概要] ページで **[構成]** を選択します。

手順に従って構成を変更し、前の手順で説明したようにハードウェアの世代を選択します。

### <a name="hardware-availability"></a>ハードウェアの可用性

#### <a name="gen4gen5"></a><a id="gen4gen5-1"></a>Gen4/Gen5

Gen4 ハードウェアは[段階的に廃止中](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)であり、新しいデプロイでは利用できなくなりました。 新しいデータベースはすべて、それより後のハードウェア世代に展開する必要があります。

Gen5 は、世界中のすべてのパブリック リージョンで使用できます。

#### <a name="fsv2-series"></a>Fsv2 シリーズ

Fsv2 シリーズは、次のリージョンで使用できます: オーストラリア中部、オーストラリア中部 2、オーストラリア東部、オーストラリア南東部、ブラジル南部、カナダ中部、東アジア、米国東部、フランス中部、インド中部、韓国中部、韓国南部、北ヨーロッパ、南アフリカ北部、東南アジア、英国南部、英国西部、西ヨーロッパ、米国西部 2。

#### <a name="m-series"></a>M シリーズ

M シリーズは、次のリージョンで使用できます: 米国東部、北ヨーロッパ、西ヨーロッパ、米国西部 2。
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>DC シリーズ

DC シリーズは、次のリージョンで使用できます: カナダ中部、カナダ東部、米国東部、北ヨーロッパ、英国南部、西ヨーロッパ、米国西部。

現在はサポートされていないリージョンで DC シリーズが必要な場合は、[サポート チケットを送信](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。 **[基本]** ページで、以下を設定します。

1. **[問題の種類]** で、 **[技術]** を選択します。
1. **[サービスの種類]** で、 **[SQL データベース]** を選択します。
1. **[問題の種類]** で、 **[セキュリティ、プライバシー、およびコンプライアンス]** を選択します。
1. **[問題のサブタイプ]** で **[Always Encrypted]** を選択します。

:::image type="content" source="./media/service-tiers-vcore/request-dc-series.png" alt-text="新しいリージョンでの DC シリーズをリクエストする" loc-scope="azure-portal":::

## <a name="next-steps"></a>次のステップ

- 開始するには、[Azure portal を使用した SQL Databaseの作成](single-database-create-quickstart.md)に関するページをご覧ください
- 価格設定の詳細については、[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/single/)に関するページをご覧ください
- 使用可能な特定のコンピューティング サイズおよびストレージ サイズの詳細については、以下を参照してください。
    - [Azure SQL Database の仮想コアベースのリソース制限](resource-limits-vcore-single-databases.md)
    - [プールされた Azure SQL Database の仮想コアベースのリソース制限](resource-limits-vcore-elastic-pools.md)
