---
title: メンテナンス期間
description: Azure SQL Database と Managed Instance のメンテナンス期間を構成する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/02/2021
ms.openlocfilehash: 4006cedf5f24ab2fc08e41b58f8acf90c404f668
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678656"
---
# <a name="maintenance-window-preview"></a>メンテナンス期間 (プレビュー)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

メンテナンス期間機能を使用すると、[Azure SQL Database](sql-database-paas-overview.md) と [SQL マネージド インスタンス](../managed-instance/sql-managed-instance-paas-overview.md)の予測可能なメンテナンス期間スケジュールを構成できます。 

メンテナンス イベントについて詳しくは、「[Azure SQL Database および Azure SQL Managed Instance での Azure メンテナンス イベントの計画](planned-maintenance.md)」を参照してください。

## <a name="overview"></a>概要

Azure では、Azure SQL Database と SQL Managed Instance のリソースに対して定期的に計画メンテナンス更新を実行します。多くの場合ここでは、基盤となるハードウェア、ソフトウェア (基となるオペレーティング システム (OS) を含む)、SQL エンジンに対する更新が行われます。 メンテナンスの更新中、リソースは完全に利用可能で、アクセスできますが、メンテナンス更新プログラムを適用するために Azure が少しの間 (平均で 8 秒間) インスタンスをオフラインにするため、メンテナンス更新プログラムによってはフェールオーバーが必要になることがあります。  計画的なメンテナンスの更新は、平均で 35 日に 1 回 (つまり、顧客は、Azure SQL Database または SQL マネージド インスタンスごとに 1 か月あたり約 1 件の計画メンテナンス イベントを期待できます)、および顧客が選択したメンテナンス期間スロット中に限り実行されます。   

このメンテナンス期間は、既定の期間中に計画メンテナンス イベントによって生じる可能性のある接続の中断の影響を受けるビジネス ワークロードに向けて定められます。  

メンテナンス期間は、Azure portal、PowerShell、CLI、または Azure API を使用して構成できます。 作成時に構成することも、既存の SQL データベースと SQL マネージド インスタンスに対して構成することもできます。

### <a name="gain-more-predictability-with-maintenance-window"></a>メンテナンス期間の予測可能性を高める

既定では、すべての Azure SQL Database とマネージド インスタンス データベースは、営業時間のピーク時の中断を回避するため、毎日現地時刻で午後 5 時から午前 8 時にのみ更新されます。 ローカル時間は、リソースをホストする [Azure リージョン](https://azure.microsoft.com/global-infrastructure/geographies/)によって決まります。 次の 2 つの追加メンテナンス ウィンドウ スロットから選択することにより、メンテナンスの更新をデータベースに適した時間に調整できます。

* **既定** 期間、月曜日から日曜日の現地時刻で午後 5 時から午前 8 時 
* 平日期間、月曜日から木曜日の現地時刻で午後 10 時から午前 6 時: **顧客によるオプトインが必要** 
* 週末期間、金曜日から日曜日の現地時刻で午後 10 時から午前 6 時: **顧客によるオプトインが必要**  

メンテナンス期間の選択が完了すると、すべての計画メンテナンス更新は、選択した期間中のみ実行されます。   

> [!Note]
> 計画メンテナンス更新だけでなく、まれに、計画外メンテナンス イベントによって使用できなくなることがあります。 

### <a name="cost"></a>コスト

サブスクリプション [プランの種類](https://azure.microsoft.com/support/legal/offer-details/)が従量課金制、クラウド ソリューション プロバイダー (CSP)、Microsoft Enterprise、または Microsoft 顧客契約の場合、メンテナンス期間の選択は無料です。

> [!Note]
> Azure オファーとは、ご利用の Azure サブスクリプションの種類です。 たとえば、[従量課金制料金](https://azure.microsoft.com/offers/ms-azr-0003p/)、[Azure イン オープン プラン](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)、および [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) のサブスクリプションはすべて Azure プランです。 オファーまたはプランごとに、使用条件と利点は異なります。 オファーまたはプランは、サブスクリプションの概要に表示されます。 サブスクリプションを別のオファーに変更する方法について詳しくは、「[Azure サブスクリプションを別のオファーに変更する](/azure/cost-management-billing/manage/switch-azure-offer)」を参照してください。

## <a name="advance-notifications"></a>事前通知

メンテナンス通知は、次回の計画メンテナンス イベントの 24 時間前に、メンテナンス時に、およびメンテナンス期間の終了時に顧客に通知するように構成できます。 詳しくは、「[事前通知](advance-notifications.md)」をご覧ください。

## <a name="availability"></a>可用性

### <a name="supported-service-level-objectives"></a>サポートされるサービス レベル目標

既定以外のメンテナンス期間は、すべての SLO で選択できますが、**次の場合を除きます**。
* ハイパースケール 
* レガシ Gen4 vCore
* Basic、S0、および S1 
* DC、Fsv2、M シリーズ

### <a name="azure-region-support"></a>Azure リージョンへの対応

既定以外のメンテナンス期間は、現在、次のリージョンで選択できます。

- オーストラリア東部
- オーストラリア南東部
- ブラジル南部
- カナダ中部
- 米国中部
- 米国東部
- 米国東部 2
- 東日本
- 米国中北部
- 北ヨーロッパ
- 米国中南部
- 東南アジア
- 英国南部
- 西ヨーロッパ
- 米国西部
- 米国西部 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Azure SQL Database のゲートウェイ メンテナンス

メンテナンス期間の最大のメリットを得るには、クライアント アプリケーションでリダイレクト接続ポリシーを使用していることが必要です。 リダイレクトは推奨の接続ポリシーであり、このときクライアントはデータベースをホストしているノードへの直接接続を確立します。これにより、待機時間が短縮され、スループットが向上します。  

* Azure SQL Database では、プロキシ接続ポリシーを使用した接続は、選択したメンテナンス期間とゲートウェイ ノードのメンテナンス期間の両方の影響を受ける可能性があります。 ただし、推奨のリダイレクト接続ポリシーを使用したクライアント接続であれば、ゲートウェイ ノードのメンテナンス フェールオーバーの影響を受けません。 

* Azure SQL マネージド インスタンスでは、ゲートウェイ ノードは[仮想クラスター内](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)にあり、マネージド インスタンスと同じメンテナンス期間を使用するため、プロキシ接続ポリシーを使用しても、接続が追加のメンテナンス期間の影響を受けることはおそらくありません。

Azure SQL Database でのクライアント接続ポリシーについて詳しくは、[Azure SQL Database の接続ポリシー](../database/connectivity-architecture.md#connection-policy)に関するページをご覧ください。 

Azure SQL マネージド インスタンスでのクライアント接続ポリシーについて詳しくは、[Azure SQL Managed Instance の接続ポリシー](../../azure-sql/managed-instance/connection-types-overview.md)に関するページをご覧ください。


## <a name="next-steps"></a>次のステップ

* [事前通知](advance-notifications.md)
* [メンテナンス期間の構成](maintenance-window-configure.md)

## <a name="learn-more"></a>詳細情報

* [メンテナンス期間に関する FAQ](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)
* [Azure SQL Database および Azure SQL Managed Instance での Azure メンテナンス イベントの計画](planned-maintenance.md)




