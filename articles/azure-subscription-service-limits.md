<properties
	pageTitle="Microsoft Azure サブスクリプションとサービスの制限、クォータ、制約"
	description="一般的な Azure サブスクリプションとサービスの制限、クォータ、制約の一覧を示します。制限を引き上げる方法と、最大値に関する情報も記載されています。"
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="jroth"/>

# Azure サブスクリプションとサービスの制限、クォータ、制約

## 概要

本書では、Microsoft Azure での最も一般的な制限について具体的に説明しています。現時点では、すべての Azure サービスを網羅していませんのでご了承ください。今後、Azure Platform 全体を広くカバーするように制限を展開し、更新していく予定です。

> [AZURE.NOTE] 「**既定の制限**」を超える制限の引き上げを希望される場合は、[オンライン カスタマー サポートに申請 (無料)](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) してください。以下の表の「**上限**」の値を超える制限の引き上げはできません。「**上限**」列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。

## 制限と Azure リソース マネージャー

複数の Azure リソースを 1 つの Azure リソース グループに結合できるようになりました。リソース グループを使用するとき、グローバルになった制限が Azure リソース マネージャーによりリージョン レベルでの管理対象になります。Azure リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](azure-portal/resource-group-portal.md)に関するページを参照してください。

次の制限では、Azure リソース マネージャーを使用する際の制限の相違点を反映するために新しい表が追加されました。たとえば、**サブスクリプションの制限**の表と、**サブスクリプションの制限 - Azure リソース マネージャー**の表などがあります。制限が 2 つのシナリオに適用される場合、制限は最初の表にのみ表示されます。特に記載のない限り、制限はすべてのリージョンに適用されます。

> [AZURE.NOTE] Azure リソース グループ内のリソースのクォータは、サブスクリプションごとではなく、サブスクリプションのリージョンごとにアクセスできることに注意してください。また、サービス管理クォータも同様です。コア クォータを例に説明します。コア対応のクォータの増量をリクエストする場合、どのリージョンでいくつのコアを使用するかを決めてから、Azure リソース グループのコア クォータの数量と目的のリージョンをリクエストする必要があります。したがって、西ヨーロッパで 30 のコアを使用してアプリケーションを実行する必要がある場合、具体的に、西ヨーロッパで 30 のコアをリクエストしてください。この場合、他のリージョンのコア クォータは増量されません -- 西ヨーロッパでのみ 30 コア クォータが増量されます。
<!-- -->
結果として、1 つのリージョンのワークロードに対して Azure リソース グループのクォータがどれくらい必要かを判断し、デプロイメントを検討している各リージョンでその量を要求するのが便利です。特定のリージョンの現在のクォータを判断する方法について詳しくは、[デプロイメントの問題のトラブルシューティング](./resource-manager-common-deployment-errors.md)に関するページを参照してください。


## サービス固有の制限

- [Active Directory](#active-directory-limits)
- [API Management](#api-management-limits)
- [App Service](#app-service-limits)
- [Application Insights](#application-insights-limits)
- [Azure Redis Cache](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [Backup](#backup-limits)
- [Batch  
](#batch-limits)
- [BizTalk Services](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [Cloud Services](#cloud-services-limits)
- [Data Factory](#data-factory-limits)
- [Data Lake Analytics](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [IoT Hub](#iot-hub-limits)
- [Key Vault](#key-vault-limits)
- [Media Services](#media-services-limits)
- [Mobile Engagement](#mobile-engagement-limits)
- [Mobile Services](#mobile-services-limits)
- [Multi-Factor Authentication](#multi-factor-authentication)
- [ネットワーク](#networking-limits)
- [Notification Hub Service](#notification-hub-service-limits)
- [Operational Insights](#operational-insights-limits)
- [リソース グループ](#resource-group-limits)
- [Scheduler](#scheduler-limits)
- [Search](#search-limits)
- [Service Bus](#service-bus-limits)
- [Site Recovery](#site-recovery-limits)
- [SQL Database](#sql-database-limits)
- [Storage](#storage-limits)
- [StorSimple System](#storsimple-system-limits)
- [Stream Analytics](#stream-analytics-limits)
- [サブスクリプション](#subscription-limits)
- [Traffic Manager](#traffic-manager-limits)
- [Virtual Machines](#virtual-machines-limits)


### サブスクリプションの制限
#### サブスクリプションの制限
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### サブスクリプションの制限 - Azure リソース マネージャー

次の制限は、Azure リソース マネージャーと Azure リソース グループの使用時に適用されます。Azure リソース マネージャーで変更されていない制限はこの一覧に含まれません。これらの制限については、前の表を参照してください。

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### リソース グループの制限

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


### Virtual Machines の制限
#### 仮想マシンの制限
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### Virtual Machines の制限 - Azure リソース マネージャー

次の制限は、Azure リソース マネージャーと Azure リソース グループの使用時に適用されます。Azure リソース マネージャーで変更されていない制限はこの一覧に含まれません。これらの制限については、前の表を参照してください。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


### ネットワークの制限

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### ネットワークの制限
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### Traffic Manager の制限

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### DNS の制限

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### ストレージの制限

ストレージ アカウントの上限の詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../articles/storage/storage-scalability-targets.md)」を参照してください。

#### ストレージ サービスの制限

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### 仮想マシン ディスクの制限

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

その他の詳細については、「[仮想マシンのサイズ](../articles/virtual-machines/virtual-machines-linux-sizes.md)」を参照してください。

**Standard Storage アカウント**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Premium Storage アカウント**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### ストレージ リソース プロバイダーの制限

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### Cloud Services の制限

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### App Service の制限
次の App Service の制限には、Web Apps、Mobile Apps、API Apps、および Logic Apps の制限が含まれます。

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### Scheduler の制限

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### Batch の制限

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###BizTalk Services の制限
次の表に Azure Biztalk Services の制限内容を示します。

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### DocumentDB の制限

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### Mobile Engagement の制限

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### Search の制限

価格レベルによって、容量と検索サービスの制限が決定されます。レベルは次のとおりです。

- **Free**: 他の Azure サブスクライバーと共有する、評価および小規模の開発プロジェクトのためのマルチ テナント サービス。
- **Basic (プレビュー)**: 小規模の運用環境のワークロード専用のコンピューティング リソースを提供します。このレベルは現在プレビュー段階であり、割引料金で提供されます。
- **Standard (S1 および S2)**: 運用環境のワークロード向けです。大容量バージョン (**S2**) は、要求すれば利用できます (azuresearch_contact@microsoft.com) に電子メールで要求します)。

[AZURE.INCLUDE [azure-search-limits-all](../includes/azure-search-limits-all.md)]

ドキュメントのサイズ、キー数、要求数、応答数など、他の制限の詳細については、「[Azure Search サービスの制限](search/search-limits-quotas-capacity.md)」をご覧ください。

### Media Services の制限

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### CDN の制限

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### Mobile Services の制限

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### Notification Hubs サービスの制限

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### Service Bus の制限

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### IoT Hub の制限

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### Data Factory の制限

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### Data Lake Analytics の制限
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### Stream Analytics の制限

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### Active Directory の制限

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### Azure RemoteApp の制限

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### StorSimple システムの制限

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Operational Insights の制限

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### Backup の制限

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### Site Recovery の制限

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Application Insights の制限

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### API Management の制限

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Azure Redis Cache の制限

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### Key Vault の制限

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### SQL Database の制限

SQL Database の制限については、「[SQL Database のリソース制限](sql-database/sql-database-resource-limits.md)」を参照してください。

## 関連項目

[Understanding Azure Limits and Increases (Azure の制限と増設について)](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure の仮想マシンおよびクラウド サービスのサイズ](virtual-machines/virtual-machines-linux-sizes.md)

[クラウド サービスのサイズ](cloud-services/cloud-services-sizes-specs.md)

<!----HONumber=AcomDC_0330_2016------>