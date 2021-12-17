---
title: メンテナンス期間
description: Azure SQL Database とマネージド インスタンスのメンテナンス期間を構成する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.custom: references_regions
ms.date: 11/12/2021
ms.openlocfilehash: cea06c5731fd17b05987e2c070264588f5428345
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491376"
---
# <a name="maintenance-window-preview"></a>メンテナンス期間 (プレビュー)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

メンテナンス期間機能を使用すると、[Azure SQL Database](sql-database-paas-overview.md) と [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) リソースのメンテナンス スケジュールを構成して、影響のあるメンテナンス イベントを予測可能にし、ワークロードの中断を減らすことができます。 

> [!Note]
> メンテナンス期間機能では、アップグレードや予定メンテナンスから見込まれる予定される影響のみを防ぎます。 フェールオーバーのあらゆる原因を防ぐことはありません。メンテナンス期間の外で短い接続中断を引き起こしうる例外には、ハードウェアの故障、クラスターの負荷分散、データベースの Service Level Objective の変更などのイベントに起因するデータベース再構成などがあります。 

## <a name="overview"></a>概要

Azure では、SQL Database と SQL マネージド インスタンス リソースの[計画メンテナンス](planned-maintenance.md)を定期的に実行します。 Azure SQL メンテナンス イベントの間、データベースは完全に利用可能ですが、[SQL Database](https://azure.microsoft.com/support/legal/sla/azure-sql-database) と [SQL マネージド インスタンス](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance)のそれぞれの可用性 SLA 内で短時間の再構成が行われる可能性があります。

メンテナンス期間は、データベースまたはインスタンスの再構成に対して回復性がなく、計画メンテナンス イベントによる短時間の接続中断を許容できない運用環境のワークロードを対象としています。 希望するメンテナンス期間を選択することで、計画メンテナンスの影響を最小限に抑えることができます。それが営業時間のピーク時以外に行われるためです。 回復性があるワークロードと非運用環境ワークロードは、Azure SQL の既定のメンテナンス ポリシーに依存できます。

メンテナンス期間は、作成時に構成することも、既存の Azure SQL リソースに対して構成することもできます。 これは、Azure portal、PowerShell、CLI、または Azure API を使用して構成できます。

> [!Important]
> メンテナンス期間の構成は、Azure SQL リソースのサービス レベルの変更と同様に、時間のかかる非同期操作です。 操作の終了時に発生する短い再構成を除いて、操作中にリソースを使用できます。これは、長時間のトランザクションが中断された場合でも、通常は最大 8 秒です。 再構成の影響を最小限に抑えるには、ピーク時以外に操作を実行する必要があります。

### <a name="gain-more-predictability-with-maintenance-window"></a>メンテナンス期間の予測可能性を高める

既定では、Azure SQL のメンテナンス ポリシーは、一般的な営業時間のピーク時の中断を回避するために、**毎日現地時刻で午前 8 時から午後 5 時まで** の間、最も影響のある更新をブロックします。 現地時刻は、リソースをホストする [Azure リージョン](https://azure.microsoft.com/global-infrastructure/geographies/)の場所によって決定され、ローカル タイム ゾーンの定義に従って夏時間が適用される場合があります。 

次の 2 つの追加メンテナンス期間スロットから選択することにより、メンテナンスの更新を Azure SQL リソースに適した時間に調整できます。
 
* **平日** のウィンドウ: 現地時間の午後 10:00 から午前 6:00、月曜日から木曜日
* **週末** のウィンドウ: 現地時間の午後 10:00 から午前 6:00、金曜日から日曜日

一覧リストされているメンテナンスウィンドウの日数は、各 8 時間のメンテナンス時間の開始日を示します。 たとえば、"10:00 PM to 6:00 AM local time, Monday – Thursday" は、メンテナンス期間が毎日 (月曜日から木曜日) の現地時刻の午後 10:00 に開始され、次の日 (火曜日から金曜日) の現地時刻の午前 6 時に完了します。

メンテナンス期間を選択し、サービスの構成が完了すると、計画メンテナンスは、選択した期間中のみ行われます。 通常、メンテナンス イベントは 1 つの期間内で完了しますが、一部のイベントは 2 つ以上の隣接する期間にまたがる場合があります。   

> [!Important]
> 重要なセキュリティ パッチの適用など、アクションの延期によって重大な影響が生じる可能性がある非常にまれな状況では、構成されたメンテナンス期間が一時的にオーバーライドされることがあります。 

### <a name="cost-and-eligibility"></a>コストと資格

メンテナンス期間の構成と使用は、対象となるすべての[プランの種類](https://azure.microsoft.com/support/legal/offer-details/) (従量課金制、クラウド ソリューション プロバイダー (CSP)、Microsoft Enterprise Agreement、または Microsoft 顧客契約) で無料です。

> [!Note]
> Azure オファーとは、ご利用の Azure サブスクリプションの種類です。 たとえば、[従量課金制料金](https://azure.microsoft.com/offers/ms-azr-0003p/)、[Azure イン オープン プラン](https://azure.microsoft.com/offers/ms-azr-0111p/)、および [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) のサブスクリプションはすべて Azure プランです。 オファーまたはプランごとに、使用条件と利点は異なります。 オファーまたはプランは、サブスクリプションの概要に表示されます。 サブスクリプションを別のオファーに変更する方法について詳しくは、「[Azure サブスクリプションを別のオファーに変更する](../../cost-management-billing/manage/switch-azure-offer.md)」を参照してください。

## <a name="advance-notifications"></a>事前通知

メンテナンス通知は、Azure SQL Database の次回の計画メンテナンス イベントの 24 時間前に、メンテナンス時に、およびメンテナンスの終了時に通知するように構成できます。 詳しくは、「[事前通知](advance-notifications.md)」をご覧ください。

## <a name="availability"></a>可用性

### <a name="supported-service-level-objectives"></a>サポートされるサービス レベル目標

既定以外のメンテナンス期間は、すべての SLO で選択できますが、**次の場合を除きます**。
* インスタンス プール
* レガシ Gen4 vCore
* Basic、S0、および S1 
* DC、Fsv2、M シリーズ

### <a name="azure-region-support"></a>Azure リージョンへの対応

既定以外のメンテナンス期間は、現在、次のリージョンで選択できます。

| Azure リージョン | SQL Managed Instance | SQL Database | [Azure 可用性ゾーン](high-availability-sla.md)内の SQL Database | 
|:---|:---|:---|:---|
| オーストラリア中部 1 | はい | | |
| オーストラリア中部 2 | はい | | |
| オーストラリア東部 | はい | はい | はい |
| オーストラリア南東部 | はい | はい | |
| ブラジル南部 | はい | はい |  |
| カナダ中部 | はい | はい | はい |
| カナダ東部 | はい | Yes | |
| インド中部 | Yes | はい | |
| 米国中部 | はい | はい | はい |
| 中国東部 2 |はい | はい ||
| 中国北部 2 |はい|はい ||
| East US | はい | はい | はい |
| 米国東部 2 | はい | はい | はい |
| 東アジア | はい | はい | |
| フランス中部 | はい | はい | |
| フランス南部 | はい | はい | |
| ドイツ中西部 | はい | はい |  |
| ドイツ北部 | はい |  |  |
| 東日本 | はい | はい | はい |
| 西日本 | はい | はい | |
| 韓国中部 | はい | | |
| 韓国南部 | はい | | |
| 米国中北部 | はい | はい | |
| 北ヨーロッパ | はい | はい | はい |
| 南アフリカ北部 | はい | | | 
| 南アフリカ西部 | はい | | | 
| 米国中南部 | はい | はい | はい |
| インド南部 | はい | はい | |
| 東南アジア | はい | はい | はい |
| スイス北部 | はい | はい | |
| スイス西部 | はい | | |
| アラブ首長国連邦中部 | はい | | |
| アラブ首長国連邦北部 | はい | | |
| 英国南部 | はい | はい | はい |
| 英国西部 | はい | Yes | |
| 米国中西部 | はい | はい | |
| 西ヨーロッパ | はい | はい | はい |
| インド西部 | はい | | |
| 米国西部 | はい | はい |  |
| 米国西部 2 | はい | はい | はい |
| | | | | 

## <a name="gateway-maintenance-for-azure-sql-database"></a>Azure SQL Database のゲートウェイ メンテナンス

メンテナンス期間の最大のメリットを得るには、クライアント アプリケーションでリダイレクト接続ポリシーを使用していることが必要です。 リダイレクトは推奨の接続ポリシーであり、このときクライアントはデータベースをホストしているノードへの直接接続を確立します。これにより、待機時間が短縮され、スループットが向上します。  

* Azure SQL Database では、プロキシ接続ポリシーを使用した接続は、選択したメンテナンス期間とゲートウェイ ノードのメンテナンス期間の両方の影響を受ける可能性があります。 ただし、推奨のリダイレクト接続ポリシーを使用したクライアント接続であれば、ゲートウェイ ノードのメンテナンス再構成の影響を受けません。 

* Azure SQL Managed Instance では、ゲートウェイ ノードは[仮想クラスター内](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)でホストされ、マネージド インスタンスと同じメンテナンス期間を使用しますが、メンテナンス イベント中の中断の回数を最小限に抑えるために、リダイレクト接続ポリシーを使用することをお勧めします。

Azure SQL Database でのクライアント接続ポリシーについて詳しくは、[Azure SQL Database の接続ポリシー](../database/connectivity-architecture.md#connection-policy)に関するページをご覧ください。 

<<<<<<< HEAD
Azure SQL Managed Instance でのクライアント接続ポリシーについて詳しくは、[Azure SQL Managed Instance の接続の種類](../../azure-sql/managed-instance/connection-types-overview.md)に関するページをご覧ください。

## <a name="considerations-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance に関する考慮事項

Azure SQL Managed Instance は、お客様の仮想ネットワーク サブネット内で実行される、隔離された一連の専用仮想マシンでホストされたサービス コンポーネントで構成されます。 これらの仮想マシンは、複数のマネージド インスタンスをホストできる[仮想クラスター](../managed-instance/connectivity-architecture-overview.md#high-level-connectivity-architecture)を形成します。 1 つのサブネットのインスタンスに対して構成されたメンテナンス期間は、サブネット内の仮想クラスターの数と、仮想クラスター間でのインスタンスの分散に影響を与える可能性があります。 これには、いくつかの影響を考慮する必要がある場合があります。
=======
Azure SQL Managed Instance でのクライアント接続ポリシーについて詳しくは、「[Azure SQL Managed Instance の接続の種類](../../azure-sql/managed-instance/connection-types-overview.md)」をご覧ください。

## <a name="considerations-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance に関する考慮事項

Azure SQL Managed Instance は、お客様の仮想ネットワーク サブネット内で実行される、隔離された一連の専用仮想マシンでホストされたサービス コンポーネントで構成されます。 これらの仮想マシンは、複数のマネージド インスタンスをホストできる[仮想クラスター](../managed-instance/connectivity-architecture-overview.md#high-level-connectivity-architecture)を形成します。 1 つのサブネットのインスタンスに対して構成されたメンテナンス期間は、サブネット内の仮想クラスターの数、仮想クラスター間でのインスタンスの分散、および仮想クラスター管理操作に影響を与える可能性があります。 これには、いくつかの影響を考慮する必要がある場合があります。
>>>>>>> repo_sync_working_branch

### <a name="maintenance-window-configuration-is-long-running-operation"></a>メンテナンス期間の構成は実行時間の長い操作です 
仮想クラスターでホストされているすべてのインスタンスは、メンテナンス期間を共有します。 既定では、すべてのマネージド インスタンスは、既定のメンテナンス期間を使用して仮想クラスターでホストされます。 その作成時または作成後にマネージド インスタンスに対して別のメンテナンス期間を指定すると、それは、対応するメンテナンス期間を持つ仮想クラスターに配置する必要があります。 そのような仮想クラスターがサブネット内に存在しない場合は、そのインスタンスを格納するために、最初に新しいものを作成する必要があります。 既存の仮想クラスターに追加のインスタンスを格納するには、クラスターのサイズ変更が必要になる場合があります。 どちらの操作も、マネージド インスタンスのメンテナンス期間の構成にかかる時間に影響します。
マネージド インスタンスでのメンテナンス期間の構成に予想される時間は、[インスタンス管理操作の予想時間](../managed-instance/management-operations-overview.md#duration)を使用して計算できます。

> [!Important]
> メンテナンス操作の終了時に短い再構成が発生します。これは、実行時間の長いトランザクションが中断された場合でも、通常は最大で 8 秒です。 再構成の影響を最小限に抑えるには、ピーク時以外に操作をスケジュールする必要があります。

### <a name="ip-address-space-requirements"></a>IP アドレス空間の要件
サブネット内の新しい各仮想クラスターには、[仮想クラスターの IP アドレスの割り当て](../managed-instance/vnet-subnet-determine-size.md#determine-subnet-size)に従って、追加の IP アドレスが必要です。 既存のマネージド インスタンスのメンテナンス期間を変更する場合も、対応するサービス レベルの仮想コアのスケーリング シナリオと同様に、[一時的な追加の IP 容量](../managed-instance/vnet-subnet-determine-size.md#update-scenarios)が必要です。

### <a name="ip-address-change"></a>IP アドレスの変更
メンテナンス期間の構成および変更を行うと、サブネットの IP アドレス範囲内で、インスタンスの IP アドレスが変更されます。

> [!Important]
>  IP アドレスを変更した後、NSG とファイアウォール規則によってデータ トラフィックがブロックされないようにしてください。 

### <a name="serialization-of-virtual-cluster-management-operations"></a>仮想クラスター管理操作のシリアル化

サービスのアップグレードや仮想クラスターのサイズ変更 (新しいコンピューティング ノードの追加や不要なコンピューティング ノードの削除) のような、仮想クラスターに影響を与える操作はシリアル化されます。 つまり、前の操作が完了しないと、新しい仮想クラスター管理操作を開始できません。 進行中のサービスのアップグレードまたはメンテナンス操作が完了する前にメンテナンス期間が終了した場合、その間に送信された他の仮想クラスター管理操作は、次のメンテナンス期間が開始され、サービスのアップグレードまたはメンテナンス操作が完了するまで保留されます。 一般的に、仮想クラスターあたりのメンテナンス操作が 1 つの期間より長くかかることはありませんが、メンテナンス操作が非常に複雑な場合には発生する可能性があります。

仮想クラスター管理操作のシリアル化は、既定のメンテナンス ポリシーにも適用される一般的な動作です。 メンテナンス期間のスケジュールが構成されている場合、隣接する 2 つの期間の間が数日になることもあります。 メンテナンス操作が 2 つの期間にまたがっている場合は、送信された操作が数日保留される可能性があります。 これは非常にまれなケースですが、新しいインスタンスの作成や既存のインスタンスのサイズ変更 (追加のコンピューティング ノードが必要な場合) は、この期間中にはブロックされる可能性があります。

## <a name="next-steps"></a>次のステップ

* [メンテナンス期間の構成](maintenance-window-configure.md)
* [事前通知](advance-notifications.md)

## <a name="learn-more"></a>詳細情報

* [メンテナンス期間に関する FAQ](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
<<<<<<< HEAD
* [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)
=======
* [SQL マネージド インスタンス](../managed-instance/sql-managed-instance-paas-overview.md)
>>>>>>> repo_sync_working_branch
* [Azure SQL Database および Azure SQL Managed Instance での Azure メンテナンス イベントの計画](planned-maintenance.md)
