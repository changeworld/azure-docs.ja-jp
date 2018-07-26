---
title: Azure での管理ソリューションのデータ収集の詳細 | Microsoft Docs
description: Azure の管理ソリューションには、特定の問題点に関するメトリックを提供するロジックや視覚化、データ取得規則が集約されています。  この記事では、Microsoft で利用可能な管理ソリューションの一覧を示し、データ収集の手法および頻度に関する詳細について説明します。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 40b8f51c66ebe98cd1c312002b7bd5e96e5032bd
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112707"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Azure での管理ソリューションのデータ収集の詳細
この記事には、Microsoft で利用可能な[管理ソリューションの](monitoring-solutions.md)一覧を、詳細なドキュメントへのリンクと共に示します。  また、Log Analytics へのデータ収集の手法および頻度に関する情報についても提供します。  この記事の情報を利用して、使用可能なさまざまなソリューションを識別し、異なる管理ソリューションのデータ フローと接続要件を理解できます。 

## <a name="list-of-management-solutions"></a>管理ソリューションの一覧

次の表に、Microsoft が提供する Azure の[管理ソリューション](monitoring-solutions.md)を示します。 列のエントリは、該当のソリューションではその手法を使用して Log Analytics にデータを収集することを意味します。  ソリューションのどの列にも印がない場合は、別の Azure サービスから直接 Log Analytics に書き込みが行われます。 詳細については、各ソリューションの詳しいドキュメントへのリンクを確認してください。

列の説明を次に示します。

- **Microsoft Monitoring Agent** - Windows および Linux 上で使用される、SCOM から管理パックを、また、Azure から管理ソリューションを実行するエージェント。 この構成では、エージェントは Operations Manager 管理グループに接続されずに、直接 Log Analytics に接続されます。 
- **Operations Manager** - Microsoft Monitoring Agent と同一のエージェント。 この構成では、Log Analytics に接続されている [Operations Manager 管理グループに接続](../log-analytics/log-analytics-om-agents.md)されます。 
-  **Azure Storage** - ソリューションは、Azure ストレージ アカウントからデータを収集します。 
- **Operations Manager が必要か** - 接続された Operations Manager 管理グループが、該当の管理ソリューションによるデータ収集を必要とします。 
- **管理グループ経由で送信される Operations Manager エージェント データ** - エージェントが[ SCOM 管理グループに接続されている](../log-analytics/log-analytics-om-agents.md)場合、データは管理サーバーから Log Analytics へ送信されます。 この場合、エージェントが直接、Log Analytics に接続される必要はありません。 このボックスに印がない場合、エージェントが SCOM 管理グループに接続されている場合でも、データはエージェントから直接 Log Analytics に送信されます。 また、エージェントは、[OMS ゲートウェイ](../log-analytics/log-analytics-oms-gateway.md)経由で Log Analytics と通信できる必要があります。
- **収集の頻度** - 管理ソリューションによってデータが収集される頻度を示します。 



| **管理ソリューション** | **プラットフォーム** | **Microsoft Monitoring Agent** | **Operations Manager エージェント** | **Azure Storage** | **Operations Manager が必要か** | **管理グループ経由で送信される Operations Manager エージェント データ** | **収集の頻度** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [アクティビティ ログ分析](../log-analytics/log-analytics-activity.md) | Azure | | | | | | 通知時 |
| [AD 評価](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 日 |
| [AD レプリケーションの状態](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 日 |
| [Agent Health](../operations-management-suite/oms-solution-agenthealth.md) | Windows および Linux | &#8226; | &#8226; | | | &#8226; | 1 分 |
| [アラート管理](../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |着信時 |
| [アラート管理](../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 分 |
| [アラート管理](../log-analytics/log-analytics-solution-alert-management.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 分 |
| [Azure Site Recovery](../site-recovery/site-recovery-overview.md) | Azure | | | | | | 該当なし |
| [Application Insights Connector (プレビュー)](../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | 通知時 |
| [Automation Hybrid Worker](../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | 該当なし |
| [Azure Application Gateway 分析](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | 通知時 |
| **管理ソリューション** | **プラットフォーム** | **Microsoft Monitoring Agent** | **Operations Manager エージェント** | **Azure Storage** | **Operations Manager が必要か** | **管理グループ経由で送信される Operations Manager エージェント データ** | **収集の頻度** |
| [Azure ネットワーク セキュリティ グループ分析 (非推奨)](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | 通知時 |
| [Azure SQL Analytics (プレビュー)](../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 1 分 |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | 通知時 |
| [Capacity and Performance (プレビュー)](../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |着信時 |
| [変更の追跡](../log-analytics/log-analytics-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |1 時間に 1 回 |
| [変更の追跡](../log-analytics/log-analytics-change-tracking.md) |Linux |&#8226; | | | | |1 時間に 1 回 |
| [Containers](../log-analytics/log-analytics-containers.md) | Windows および Linux | &#8226; | &#8226; |  |  |  | 3 分 |
| [Key Vault Analytics](../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |通知時 |
| [マルウェアの評価](../log-analytics/log-analytics-malware.md) |Windows |&#8226; |&#8226; | | |&#8226; |1 時間に 1 回 |
| [ネットワーク パフォーマンス監視](../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP ハンドシェイク (5 秒ごと)、データ送信 (3 分ごと) |
| [Office 365 Analytics (プレビュー)](../operations-management-suite/oms-solution-office-365.md) |Windows | | | | | |通知時 |
| **管理ソリューション** | **プラットフォーム** | **Microsoft Monitoring Agent** | **Operations Manager エージェント** | **Azure Storage** | **Operations Manager が必要か** | **管理グループ経由で送信される Operations Manager エージェント データ** | **収集の頻度** |
| [Service Fabric Analytics (プレビュー)](../log-analytics/log-analytics-service-fabric.md) |Windows | | |&#8226; | | |5 分 |
| [サービス マップ](../operations-management-suite/operations-management-suite-service-map.md) | Windows および Linux | &#8226; | &#8226; |  |  |  | 15 秒 |
| [SQL の評価](../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 日 |
| [SurfaceHub](../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |着信時 |
| [System Center Operations Manager Assessment (プレビュー)](../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 日 |
| [更新管理](../operations-management-suite/oms-solution-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |1 日に少なくとも 2 回、更新プログラムのインストールの 15 分後 |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 日 |
| [VMware Monitoring (非推奨)](../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 分 |
| [Wire Data 2.0 (プレビュー)](../log-analytics/log-analytics-wire-data.md) |Windows (2012 R2/8.1 以降) |&#8226; |&#8226; | | | | 1 分 |




## <a name="next-steps"></a>次の手順
* [クエリを作成](../log-analytics/log-analytics-log-searches.md)して、管理ソリューションで収集したデータを分析する方法を確認します。
