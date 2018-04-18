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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 7a775b6d23c144c81650bb3608ee6a117475a9ba
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-containers-with-log-analytics"></a>Log Analytics でコンテナーを監視
 
この記事では、クラスターを監視するようにコンテナーを設定するために必要な手順について説明します。 詳細については、[Service Fabric でのコンテナーの監視](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers)に関するセクションをご覧ください。 この手順の詳細なチュートリアルについては、[Service Fabric で Windows コンテナーを監視する](service-fabric-tutorial-monitoring-wincontainers.md)方法に関するページを参照してください。

## <a name="set-up-the-container-monitoring-solution"></a>コンテナー監視ソリューションの設定

> [!NOTE]
> お使いのクラスターに対して Log Analytics を設定して、ノード上に OMS エージェントを展開させる必要があります。 実行していない場合は、[Log Analytics の設定](service-fabric-diagnostics-oms-setup.md)と[エージェントのクラスターへの追加](service-fabric-diagnostics-oms-agent.md)の手順をまず実行します。

1. お使いのクラスターを Log Analytics と OMS エージェントを使って設定した後、コンテナーを展開します。 コンテナーが展開されるのを待ってから次の手順に進みます。

2. Azure Marketplace で、「*コンテナー監視ソリューション*」を検索し、監視 + 管理 カテゴリの下に表示される **コンテナー監視ソリューション** リソースをクリックします。

    ![コンテナー ソリューションの追加](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 既にクラスターに対して作成したのと同じワークスペース内でソリューションを作成します。 この変更により、コンテナーの Docker データの収集を開始するエージェントが自動的にトリガーされます。 約 15 分位でソリューションが点灯し、ログの受信と統計が表示されるのを確認できます。

## <a name="next-steps"></a>次の手順
* Service Fabric のコンテナー オーケストレーションについての詳細 - [Service Fabric とコンテナー](service-fabric-containers-overview.md)
* Log Analytic の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる
* Log Analytics を構成して、検出と診断に役立つ[自動アラート](../log-analytics/log-analytics-alerts.md)規則を設定する