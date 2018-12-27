---
title: Azure Service Fabric のイベントの一覧 | Microsoft Docs
description: クラスターの監視に役立つ、Azure Service Fabric によって提供されるイベントの包括的な一覧。
services: service-fabric
documentationcenter: .net
author: srrengar
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
ms.openlocfilehash: 03dac03405588ba00a0f8ca5b127956c40853e36
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868515"
---
# <a name="list-of-service-fabric-events"></a>Service Fabric イベントの一覧 

Service Fabric は、クラスター イベントのプライマリ セットを公開して、クラスターの状態を [Service Fabric イベント](service-fabric-diagnostics-events.md)として通知します。 これらは、ノードとクラスター上の Service Fabric によって実行されるアクション、またはクラスターの所有者/オペレーターによって行われる管理上の決定事項に基づきます。 これらのイベントには、クラスターで [EventStore](service-fabric-diagnostics-eventstore.md) のクエリを実行することでアクセスするか、操作チャネルを通じてアクセスできます。 Windows マシンでは、操作チャネルは EventLog にもフックされるので、イベント ビューアーで Service Fabric イベントを確認できます。 

>[!NOTE]
>バージョン 6.2 より前のクラスターの Service Fabric イベントの一覧については、次のセクションをご覧ください。 

プラットフォームで利用可能なすべてのイベントの一覧を、マップ先のエンティティの順に示します。

## <a name="cluster-events"></a>クラスター イベント

**クラスター アップグレード イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStarted | クラスターのアップグレードが開始されました | CM | 情報 | 1 |
| 29628 | ClusterUpgradeCompleted | クラスターのアップグレードが完了しました| CM | 情報 | 1 |
| 29629 | ClusterUpgradeRollbackStarted | クラスターのアップグレードのロールバックが開始されました | CM | 情報 | 1 |
| 29630 | ClusterUpgradeRollbackCompleted | クラスターのアップグレードのロールバックが完了しました | CM | 情報 | 1 |
| 29631 | ClusterUpgradeDomainCompleted | クラスターのアップグレード中にドメインのアップグレードが完了しました | CM | 情報 | 1 |

## <a name="node-events"></a>ノード イベント

**ノード ライフサイクル イベント** 

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 18602 | NodeDeactivateCompleted | ノードの非アクティブ化が完了しました | FM | 情報 | 1 |
| 18603 | NodeUp | クラスターでノードが起動したことが検出されました | FM | 情報 | 1 |
| 18604 | NodeDown | クラスターでノードがシャットダウンしたことが検出されました |  FM | 情報 | 1 |
| 18605 | NodeAddedToCluster | クラスターに新しいノードが追加されました | FM | 情報 | 1 |
| 18606 | NodeRemovedFromCluster | クラスターからノードが削除されました | FM | 情報 | 1 |
| 18607 | NodeDeactivateStarted | ノードの非アクティブ化が開始されました | FM | 情報 | 1 |
| 25620 | NodeOpening | ノードを起動しています  (ノード ライフサイクルの第 1 段階) | FabricNode | 情報 | 1 |
| 25621 | NodeOpenSucceeded | ノードが正常に起動しました | FabricNode | 情報 | 1 |
| 25622 | NodeOpenFailed | ノードの起動に失敗しました | FabricNode | 情報 | 1 |
| 25623 | NodeClosing | ノードをシャットダウンしています  (ノード ライフサイクルの最終段階の開始) | FabricNode | 情報 | 1 |
| 25624 | NodeClosed | ノードが正常にシャットダウンしました | FabricNode | 情報 | 1 |
| 25625 | NodeAborting | 正常ではない方法でノードがシャットダウンを開始しています | FabricNode | 情報 | 1 |
| 25626 | NodeAborted | 正常ではない方法でノードがシャットダウンしました | FabricNode | 情報 | 1 |

## <a name="application-events"></a>アプリケーション イベント

**アプリケーション ライフサイクル イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 29620 | ApplicationCreated | 新しいアプリケーションが作成されました | CM | 情報 | 1 |
| 29625 | ApplicationDeleted | 既存のアプリケーションが削除されました | CM | 情報 | 1 |
| 23083 | ApplicationProcessExited | アプリケーション内のプロセスが終了しました | ホスティング | 情報 | 1 |

**アプリケーション アップグレード イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 29621 | ApplicationUpgradeStarted | アプリケーションのアップグレードが開始されました | CM | 情報 | 1 |
| 29622 | ApplicationUpgradeCompleted | アプリケーションのアップグレードが完了しました | CM | 情報 | 1 |
| 29623 | ApplicationUpgradeRollbackStarted | アプリケーションのアップグレードのロールバックが開始されました |CM | 情報 | 1 |
| 29624 | ApplicationUpgradeRollbackCompleted | アプリケーションのアップグレードのロールバックが完了しました | CM | 情報 | 1 |
| 29626 | ApplicationUpgradeDomainCompleted | アプリケーションのアップグレード中にドメインのアップグレードが完了しました | CM | 情報 | 1 |

## <a name="service-events"></a>サービス イベント

**サービス ライフサイクル イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | 新しいサービスが作成されました | FM | 情報 | 1 |
| 18658 | ServiceDeleted | 既存のサービスが削除されました | FM | 情報 | 1 |

## <a name="partition-events"></a>パーティション イベント

**パーティション移動イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | パーティションの再構成が完了しました | RA | 情報 | 1 |

## <a name="container-events"></a>コンテナー イベント

**コンテナー ライフサイクル イベント** 

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | コンテナーが起動しました | ホスティング | 情報 | 1 |
| 23075 | ContainerDeactivated | コンテナーが停止しました | ホスティング | 情報 | 1 |
| 23082 | ContainerExited | コンテナーが終了しました。UnexpectedTermination フラグを確認してください | ホスティング | 情報 | 1 |

## <a name="health-reports"></a>正常性レポート

**クラスター正常性レポート イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | クラスターの新しい正常性レポートが使用可能です | HM | 情報 | 1 |
| 54437 | ClusterHealthReportExpired | クラスターの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

**ノード正常性レポート イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | ノードの新しい正常性レポートが使用可能です | HM | 情報 | 1 |
| 54432 | NodeHealthReportExpired | ノードの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

**アプリケーション正常性レポート イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | アプリケーションの新しい正常性レポートが作成されました。 これは展開解除されたアプリケーションのレポートです。 | HM | 情報 | 1 |
| 54426 | DeployedApplicationNewHealthReport | 展開済みアプリケーションの新しい正常性レポートが作成されました | HM | 情報 | 1 |
| 54427 | DeployedServicePackageNewHealthReport | 展開済みサービスの新しい正常性レポートが作成されました | HM | 情報 | 1 |
| 54434 | ApplicationHealthReportExpired | アプリケーションの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |
| 54435 | DeployedApplicationHealthReportExpired | 展開済みアプリケーションの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | 展開済みサービスの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

**サービス正常性レポート イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | サービスの新しい正常性レポートが作成されました | HM | 情報 | 1 |
| 54433 | ServiceHealthReportExpired | サービスの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

**パーティション正常性レポート イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | パーティションの新しい正常性レポートが作成されました | HM | 情報 | 1 |
| 54431 | PartitionHealthReportExpired | パーティションの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

**レプリカ正常性レポート イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | ステートフル レプリカの正常性レポートが作成されました | HM | 情報 | 1 |
| 54430 | StatelessInstanceNewHealthReport | ステートレス インスタンスの新しい正常性レポートが作成されました | HM | 情報 | 1 |
| 54438 | StatefulReplicaHealthReportExpired | ステートフル レプリカの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |
| 54439 | StatelessInstanceHealthReportExpired | ステートレス インスタンスの既存の正常性レポートの有効期限が切れました | HM | 情報 | 1 |

## <a name="chaos-testing-events"></a>カオス テスト イベント 

**カオス セッション イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | カオス テスト セッションが開始されました | Testability | 情報 | 1 |
| 50023 | ChaosStopped | カオス テスト セッションが中止されました | Testability | 情報 | 1 |

**Chaos ノード イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | カオス テスト セッションの一環として、ノードの再起動がスケジュールされました | Testability | 情報 | 1 |
| 50087 | ChaosNodeRestartCompleted | カオス テスト セッションの一環として、ノードの再起動が完了しました | Testability | 情報 | 1 |

**Chaos アプリケーション イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | カオス テスト セッション中に、コード パッケージの再起動がスケジュールされました | Testability | 情報 | 1 |
| 50101 | ChaosCodePackageRestartCompleted | カオス テスト セッション中に、コード パッケージの再起動が完了しました | Testability | 情報 | 1 |

**Chaos パーティション イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | カオス テスト セッションの一環として、プライマリ パーティションの移動がスケジュールされました | Testability | 情報 | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | カオス テスト セッションの一環として、セカンダリ パーティションの移動がスケジュールされました | Testability | 情報 | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | プライマリ パーティションの移動の詳細な分析が使用可能です | Testability | 情報 | 1 |

**Chaos レプリカ イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | カオス テスト セッションの一環として、レプリカの再起動がスケジュールされました | Testability | 情報 | 1 |
| 50051 | ChaosReplicaRemovalScheduled | カオス テスト セッションの一環として、レプリカの削除がスケジュールされました | Testability | 情報 | 1 |
| 50093 | ChaosReplicaRemovalCompleted | カオス テスト セッションの一環として、レプリカの削除が完了しました | Testability | 情報 | 1 |

## <a name="other-events"></a>その他のイベント

**相関イベント**

| EventId | Name | 説明 |ソース (タスク) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | 相関関係が検出されました | Testability | 情報 | 1 |

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
