---
title: Azure Service Fabric のイベントの一覧 | Microsoft Docs
description: クラスターの監視に役立つ、Azure Service Fabric によって提供されるイベントの包括的な一覧。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 529df0147d2563c62c4a9578e47184bd98b01761
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212961"
---
# <a name="list-of-service-fabric-events"></a>Service Fabric イベントの一覧 

Service Fabric は、クラスター イベントのプライマリ セットを公開して、クラスターの状態を [Service Fabric イベント](service-fabric-diagnostics-events.md)として通知します。 これらは、ノードとクラスター上の Service Fabric によって実行されるアクション、またはクラスターの所有者/オペレーターによって行われる管理上の決定事項に基づきます。 これらのイベントには、クラスターで [EventStore](service-fabric-diagnostics-eventstore.md) のクエリを実行することでアクセスするか、操作チャネルを通じてアクセスできます。 Windows マシンでは、操作チャネルは EventLog にもフックされるので、イベント ビューアーで Service Fabric イベントを確認できます。 

>[!NOTE]
>バージョン 6.2 より前のクラスターの Service Fabric イベントの一覧については、次のセクションをご覧ください。 

プラットフォームで利用可能なすべてのイベントの一覧を、マップ先のエンティティの順に示します。

## <a name="cluster-events"></a>クラスター イベント

**クラスター アップグレード イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | 情報 | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | 情報 | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | 情報 | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | 情報 | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | 情報 | 1 |

**クラスター正常性レポート イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | 情報 | 1 |
| 54437 | ExpiredClusterEventOperational | HM | 情報 | 1 |

**Chaos サービス イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Testability | 情報 | 1 |
| 50023 | ChaosStoppedEvent | Testability | 情報 | 1 |

## <a name="node-events"></a>ノード イベント

**ノード ライフサイクル イベント** 

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | 情報 | 1 |
| 18603 | NodeUpOperational | FM | 情報 | 1 |
| 18604 | NodeDownOperational | FM | 情報 | 1 |
| 18605 | NodeAddedOperational | FM | 情報 | 1 |
| 18606 | NodeRemovedOperational | FM | 情報 | 1 |
| 18607 | DeactivateNodeStartOperational | FM | 情報 | 1 |
| 25620 | NodeOpening | FabricNode | 情報 | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | 情報 | 1 |
| 25622 | NodeOpenedFailed | FabricNode | 情報 | 1 |
| 25623 | NodeClosing | FabricNode | 情報 | 1 |
| 25624 | NodeClosed | FabricNode | 情報 | 1 |
| 25625 | NodeAborting | FabricNode | 情報 | 1 |
| 25626 | NodeAborted | FabricNode | 情報 | 1 |

**ノード正常性レポート イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | 情報 | 1 |
| 54432 | ExpiredNodeEventOperational | HM | 情報 | 1 |

**Chaos ノード イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Testability | 情報 | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Testability | 情報 | 1 |

## <a name="application-events"></a>アプリケーション イベント

**アプリケーション ライフサイクル イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | 情報 | 1 |
| 29625 | ApplicationDeletedOperational | CM | 情報 | 1 |
| 23083 | ProcessExitedOperational | ホスティング | 情報 | 1 |

**アプリケーション アップグレード イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | 情報 | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | 情報 | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | 情報 | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | 情報 | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | 情報 | 1 |

**アプリケーション正常性レポート イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | 情報 | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | 情報 | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | 情報 | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | 情報 | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | 情報 | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | 情報 | 1 |

**Chaos アプリケーション イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Testability | 情報 | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Testability | 情報 | 1 |

## <a name="service-events"></a>サービス イベント

**サービス ライフサイクル イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | 情報 | 1 |
| 18658 | ServiceDeletedOperational | FM | 情報 | 1 |

**サービス正常性レポート イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | 情報 | 1 |
| 54433 | ExpiredServiceEventOperational | HM | 情報 | 1 |

## <a name="partition-events"></a>パーティション イベント

**パーティション移動イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | 情報 | 1 |

**パーティション正常性レポート イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | 情報 | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | 情報 | 1 |

**Chaos パーティション イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Testability | 情報 | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Testability | 情報 | 1 |

**パーティション解析イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Testability | 情報 | 1 |

## <a name="replica-events"></a>レプリカ イベント

**レプリカ正常性レポート イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | 情報 | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | 情報 | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | 情報 | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | 情報 | 1 |

**Chaos レプリカ イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Testability | 情報 | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Testability | 情報 | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Testability | 情報 | 1 |

## <a name="container-events"></a>コンテナー イベント

**コンテナー ライフサイクル イベント** 

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | ホスティング | 情報 | 1 |
| 23075 | ContainerDeactivatedOperational | ホスティング | 情報 | 1 |
| 23082 | ContainerExitedOperational | ホスティング | 情報 | 1 |

## <a name="other-events"></a>その他のイベント

**相関イベント**

| EventId | Name | ソース (タスク) | Level | バージョン |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Testability | 情報 | 1 |

## <a name="events-prior-to-version-62"></a>バージョン 6.2 より前のイベント

バージョン 6.2 より前の Service Fabric で提供されるイベントの包括的な一覧を次に示します。

| EventId | Name | ソース (タスク) | Level |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | 情報 |
| 25621 | NodeOpenedSuccess | FabricNode | 情報 |
| 25622 | NodeOpenedFailed | FabricNode | 情報 |
| 25623 | NodeClosing | FabricNode | 情報 |
| 25624 | NodeClosed | FabricNode | 情報 |
| 25625 | NodeAborting | FabricNode | 情報 |
| 25626 | NodeAborted | FabricNode | 情報 |
| 29627 | ClusterUpgradeStart | CM | 情報 |
| 29628 | ClusterUpgradeComplete | CM | 情報 |
| 29629 | ClusterUpgradeRollback | CM | 情報 |
| 29630 | ClusterUpgradeRollbackComplete | CM | 情報 |
| 29631 | ClusterUpgradeDomainComplete | CM | 情報 |
| 23074 | ContainerActivated | ホスティング | 情報 |
| 23075 | ContainerDeactivated | ホスティング | 情報 |
| 29620 | ApplicationCreated | CM | 情報 |
| 29621 | ApplicationUpgradeStart | CM | 情報 |
| 29622 | ApplicationUpgradeComplete | CM | 情報 |
| 29623 | ApplicationUpgradeRollback | CM | 情報 |
| 29624 | ApplicationUpgradeRollbackComplete | CM | 情報 |
| 29625 | ApplicationDeleted | CM | 情報 |
| 29626 | ApplicationUpgradeDomainComplete | CM | 情報 |
| 18566 | ServiceCreated | FM | 情報 |
| 18567 | ServiceDeleted | FM | 情報 |

## <a name="next-steps"></a>次の手順

* Service Fabric における[プラットフォーム レベルでのイベントの生成](service-fabric-diagnostics-event-generation-infra.md)一般に関する詳細情報
* より多くのログを収集するための [Azure 診断](service-fabric-diagnostics-event-aggregation-wad.md)の構成の変更
* 稼動チャネルのログを参照するための [Application Insights の設定](service-fabric-diagnostics-event-analysis-appinsights.md)
