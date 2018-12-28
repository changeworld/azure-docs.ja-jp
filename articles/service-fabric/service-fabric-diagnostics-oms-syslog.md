---
title: Azure Service Fabric で Linux クラスター イベントを監視する | Microsoft Docs
description: Syslog から Linux クラスター イベントを監視する方法について説明します
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: ec2b623650818877930ac6b95a17ee264f07efdf
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959539"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Syslog 内の Service Fabric Linux クラスター イベント

Service Fabric では、クラスター内の重要なアクティビティを通知するための一連のプラットフォーム イベントを公開しています。 公開されているすべてのイベントの一覧については、[こちら](service-fabric-diagnostics-event-generation-operational.md)を参照してください。 これらのイベントは、さまざまな方法で使用できます。 この記事では、このようなイベントを Syslog に書き込むように Service Fabric を構成する方法について説明します。

## <a name="introduction"></a>はじめに

6.4 リリースでは、Service Fabric プラットフォーム イベントを Linux クラスター用の Syslog に送信するための SyslogConsumer が導入されました。 有効にすると、イベントは自動的に Syslog に送信されます。これは、Log Analytics エージェントで収集および送信できます。

各 Syslog イベントには 4 つのコンポーネントがあります
* Facility
* ID
* Message
* severity

SyslogConsumer では、Facility `Local0` を使用すると、すべてのプラットフォーム イベントが書き込まれます。 構成を変更することで任意の有効な Facility に更新できます。使用される ID は `ServicFabric` です。 Message フィールドには JSON でシリアル化されたイベント全体が含まれているため、さまざまなツールからクエリを実行したり使用したりすることができます。 

## <a name="enable-syslogconsumer"></a>SyslogConsumer を有効にする

SyslogConsumer を有効にするには、クラスターのアップグレードを実行する必要があります。 `fabricSettings` セクションは、次のコードで更新する必要があります。 このコードには SyslogConsumer に関連するセクションのみが含まれていることに注意してください

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

これは、呼び出すための変更です
1. Common セクションには、`LinuxStructuredTracesEnabled` という新しいパラメーターがあります。 **これは、Syslog への送信時に Linux イベントを構造化およびシリアル化するために必要です。**
2. Diagnostics セクションに、新しい ConsumerInstance であるSyslogConsumer が追加されました。 これで、イベントの他のコンシューマーが存在することをプラットフォームに伝えます。 
3. 新しいセクション SyslogConsumer では `IsEnabled` を `true` にする必要があります。 これは Local0 Facility を自動的に使用するように構成されています。 他のパラメーターを追加することで、これを上書きすることができます。

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="log-analytics-integration"></a>Log Analytics の統合
Log Analytics などの監視ツールでこれらの Syslog イベントを読み取ることができます。 Azure Marketplace を使用して Log Analytics ワークスペースを作成するには、こちらの [手順].(../azure-monitor/learn/quick-create-workspace.md) を参照してください。また、このデータを収集してワークスペースに送信するには、Log Analytics エージェントをクラスターに追加する必要があります。 これは、パフォーマンス カウンターの収集に使用されるものと同じエージェントです。 

1. `Advanced Settings` ブレードに移動します

    ![ワークスペースの設定](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. [`Data`] をクリックします。
3. [`Syslog`] をクリックします。
4. Local0 を追跡する Facility として構成します。fabricSettings で変更した場合は、別の Facility を追加できます。

    ![Configure Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. ワークスペース リソースのメニューの `Logs` をクリックしてクエリ エクスプローラーに進み、クエリを開始します。

    ![ワークスペースのログ](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. ProcessName としての `ServiceFabric` を探して、`Syslog` テーブルに対してクエリを実行できます。 次のクエリは、イベント内の JSON を解析してその内容を表示する方法の例です。

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog クエリ](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

上の例は、NodeDown イベントの例です。 すべてのイベント一覧については、[こちら](service-fabric-diagnostics-event-generation-operational.md)を参照してください。

## <a name="next-steps"></a>次の手順
* お使いのノードに [Log Analytics エージェントをデプロイ](service-fabric-diagnostics-oms-agent.md)してパフォーマンス カウンターを収集し、Docker の統計とコンテナーのログを収集する
* Log Analytic の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる
* [Log Analytics のビュー デザイナーを使用してカスタム ビューを作成する](../log-analytics/log-analytics-view-designer.md)
* [Log Analytics と Syslog との統合](../log-analytics/log-analytics-data-sources-syslog.md)方法のリファレンス
