---
title: "OMS を使用した Azure Service Fabric イベント分析 | Microsoft Docs"
description: "Azure Service Fabric クラスターの監視と診断に OMS を使用したイベントの視覚化と分析について説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 977c5d64a32157b39aa6b618196dde20c4c3cc8e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>OMS を使用したイベントの分析と視覚化

Operations Management Suite (OMS) は、クラウドでホストされるアプリケーションとサービスの監視と診断に役立つ管理サービスのコレクションです。 OMS および OMS が提供するサービスの詳細については、[OMS とは](../operations-management-suite/operations-management-suite-overview.md)を参照してください

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics と OMS ワークスペース

Log Analytics は、Azure ストレージ テーブルまたはエージェントなどの管理対象リソースからデータを収集し、中央レポジトリ内で保守します。 それらのデータは分析、アラート、および視覚化、さらにエクスポートに使用できます。 Log Analytics は、イベント、パフォーマンス データ、またはその他のカスタム データをサポートします。

OMS が構成されているときは特定の *OMS ワークスペース*にアクセスして、ダッシュボード内でデータを照会したり視覚化したりできます。

Log Analytics がデータを受け取った後、OMS にはいくつかの*管理ソリューション*が用意されています。これらは受け取るデータを監視するために事前にパッケージ化されたソリューションで、いくつかのシナリオに合わせてカスタマイズされています。 *Service Fabric Analytics* ソリューションと*コンテナー* ソリューションが含まれており、これらは Service Fabric クラスターを使用する際の診断と監視に最も関連性の高いソリューションです。 調査してみる価値のあるソリューションが他にもいくつかあり、OMS ではカスタム ソリューションを作成することもできます。詳細については[こちら](../operations-management-suite/operations-management-suite-solutions.md)をご覧ください。 クラスターに使用するために選択する各ソリューションは、同じ OMS ワークスペース内に Log Analytics と一緒に構成できます。 ワークスペースでは、カスタム ダッシュボードを作成でき、データを視覚化したり、収集、処理、および分析するデータに変更を加えたりできます。

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>Service Fabric Analytics ソリューションによる OMS ワークスペースの設定
OMS ワークスペースに Service Fabric ソリューションを含めることをお勧めします。プラットフォーム/アプリケーション レベルから入ってくるさまざまなログのチャネルを表示するダッシュボードが用意されており、Service Fabric 固有のログを照会することもできます。 ここで、比較的単純な Service Fabric ソリューションを示します。クラスター上に単一アプリケーションが展開されます。

![OMS SF ソリューション](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

お使いのクラスターでこの作業を始める場合は、[OMS Log Analytics の設定](service-fabric-diagnostics-oms-setup.md)に関する記事をご覧ください。

## <a name="using-the-oms-agent"></a>OMS エージェントの使用

EventFlow と WAD を集計ソリューションとして使用することをお勧めします。診断と監視をモジュール方式で使用できます。 たとえば、EventFlow からの出力を変更する場合は、実際のインストルメンテーションを変更する必要はなく、構成ファイルを変更するだけで済みます。 ただし、OMS Log Analytics を使用することに投資すると決めた場合は、[OMS エージェント](../log-analytics/log-analytics-windows-agent.md)を設定する必要があります。 クラスターにコンテナーをデプロイするときは、次の説明のように OMS エージェントも使用する必要があります。 

この手順については、「[Add the OMS Agent to a cluster (OMS エージェントのクラスターへの追加)](service-fabric-diagnostics-oms-agent.md)」をご覧ください。

これの利点は次のとおりです。

* パフォーマンス カウンターおよびメトリック面に関するデータが豊富
* クラスターから収集されるメトリックを簡単に構成できます。クラスターの構成を更新する必要がありません。 エージェントの設定は OMS ポータルから変更できます。また、必要な構成に合わせるためにエージェントが自動的に再起動します。 特定のパフォーマンス カウンターを選択するように OMS エージェントを構成するには、ワークスペースの **[Home] \(ホーム)、[Settings] \(設定)、[Data] \(データ)、[Windows Performance Counters] \(Windows パフォーマンス カウンター)** の順に移動し、収集するデータを選択します
* データが速く表示される (データは OMS / Log Analytics によって選択される前に保存される必要があるが、それより速く表示される)
* コンテナーの監視はもっと簡単です。Docker ログ(stdout、stderr) と統計 (コンテナーおよびノード レベルに関するパフォーマンス メトリック) を選択できます。

ここでの主な考慮事項は、エージェントはすべてのアプリケーションと共にクラスターに展開されるため、クラスター上のアプリケーションのパフォーマンスにいくらかの影響が出るということです。

## <a name="monitoring-containers"></a>コンテナーの監視

コンテナーを Service Fabric クラスターに展開するときは、クラスターが OMS エージェントで設定され、監視と診断を有効にするためにコンテナー ソリューションが OMS ワークスペースに追加されていることが推奨されます。 ここで、ワークスペース内のコンテナー ソリューションを示します。

![基本的な OMS のダッシュボード](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

エージェントはいくつかのコンテナー固有ログの収集を有効にします。これらは、OMS 内で照会したり、視覚化されたパフォーマンス インジケーターに使用したりできます。 収集されるログの種類は次のとおりです。

* ContainerInventory: コンテナーの場所、名前、およびイメージに関する情報を表示
* ContainerImageInventory: ID やサイズなど、デプロイ済みのイメージに関する情報
* ContainerLog: 特定のエラー ログ、Docker ログ (stdout など)、およびその他のエントリ
* ContainerServiceLog: 実行されている Docker デーモン コマンド
* Perf: コンテナーの CPU、メモリ、ネットワーク トラフィック、ディスク I/O、およびホスト コンピューターからのカスタム メトリックなどのパフォーマンス カウンター

「[Monitor containers with OMS Log Analytics (OMS Log Analytics でのコンテナーの監視)](service-fabric-diagnostics-oms-containers.md)」では、クラスターを監視するようにコンテナーを設定するために必要な手順について説明しています。 OMS のコンテナー ソリューションの詳細については、ソリューションの[ドキュメント](../log-analytics/log-analytics-containers.md)を参照してください。

## <a name="next-steps"></a>次のステップ

必要に応じてワークスペースをカスタマイズするには、次の OMS ツールとオプションを調べてください。

* オンプレミス クラスター用に、OMS はデータを OMS に送信するために使用できるゲートウェイ (HTTP 転送プロキシ) を提供します。 詳細については、[インターネットにアクセスできないコンピューターを OMS ゲートウェイを使って OMS に接続する](../log-analytics/log-analytics-oms-gateway.md)を参照してください
* OMS を構成して、検出と診断に役立つ[自動アラート](../log-analytics/log-analytics-alerts.md)を設定する
* Log Analytic の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる