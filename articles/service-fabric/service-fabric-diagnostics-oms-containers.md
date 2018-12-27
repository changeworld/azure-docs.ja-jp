---
title: Log Analytics を使用した Azure Service Fabric のコンテナーの監視 | Microsoft Docs
description: Azure Service Fabric クラスターで実行しているコンテナーを監視するために、Log Analytics を使用します。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: d5fd55ec93ce07e30e4c6f123f9be8492581053c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972254"
---
# <a name="monitor-containers-with-log-analytics"></a>Log Analytics でコンテナーを監視
 
この記事では、コンテナーのイベントを表示するための Azure Log Analytics コンテナー監視ソリューションを設定するために必要な手順について説明します。 コンテナー イベントを収集するクラスターを設定するには、こちらの[ステップ バイ ステップ チュートリアル](service-fabric-tutorial-monitoring-wincontainers.md)を参照してください。 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

## <a name="set-up-the-container-monitoring-solution"></a>コンテナー監視ソリューションの設定

> [!NOTE]
> お使いのクラスターに対して Log Analytics を設定して、ノード上に Log Analytics エージェントを展開させる必要があります。 実行していない場合は、[Log Analytics の設定](service-fabric-diagnostics-oms-setup.md)と [Log Analytics エージェントのクラスターへの追加](service-fabric-diagnostics-oms-agent.md)の手順をまず実行します。

1. お使いのクラスターを Log Analytics と Log Analytics エージェントを使って設定した後、コンテナーを展開します。 コンテナーが展開されるのを待ってから次の手順に進みます。

2. Azure Marketplace で、「*コンテナー監視ソリューション*」を検索し、監視 + 管理 カテゴリの下に表示される **コンテナー監視ソリューション** リソースをクリックします。

    ![コンテナー ソリューションの追加](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 既にクラスターに対して作成したのと同じワークスペース内でソリューションを作成します。 この変更により、コンテナーの Docker データの収集を開始するエージェントが自動的にトリガーされます。 下の図に示すように、約 15 分位でソリューションが点灯し、ログの受信と統計が表示されるのを確認できます。

    ![Log Analytics の基本のダッシュボード](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

エージェントはいくつかのコンテナー固有ログの収集を有効にします。これらは、Log Analytics 内で照会したり、視覚化されたパフォーマンス インジケーターに使用したりできます。 収集されるログの種類は次のとおりです。

* ContainerInventory: コンテナーの場所、名前、およびイメージに関する情報を表示
* ContainerImageInventory: ID やサイズなど、デプロイ済みのイメージに関する情報
* ContainerLog: 特定のエラー ログ、Docker ログ (stdout など)、およびその他のエントリ
* ContainerServiceLog: 実行されている Docker デーモン コマンド
* Perf: コンテナーの CPU、メモリ、ネットワーク トラフィック、ディスク I/O、およびホスト コンピューターからのカスタム メトリックなどのパフォーマンス カウンター



## <a name="next-steps"></a>次の手順
* [Log Analytics のコンテナー ソリューション](../azure-monitor/insights/containers.md)について説明します。
* Service Fabric のコンテナー オーケストレーションについての詳細 - [Service Fabric とコンテナー](service-fabric-containers-overview.md)
* Log Analytic の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる
* Log Analytics を構成して、検出と診断に役立つ[自動アラート](../log-analytics/log-analytics-alerts.md)規則を設定する