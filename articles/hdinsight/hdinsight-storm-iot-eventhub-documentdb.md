---
title: "HDInsight 上の Apache Storm を使用した車両センサー データの処理 | Microsoft Docs"
description: "HDInsight 上の Apache Storm を使用して Event Hubs からの車両センサー データを処理する方法について説明します。 Azure Cosmos DB からモデルのデータを追加し、ストレージに出力を格納します。"
services: hdinsight,documentdb,notification-hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 78980635-8bef-4c33-96c3-fae50e932e31
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8e8ebc724e1c70e8fcd56312adef5da2342373ea
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>HDInsight 上の Apache Storm を使用した Azure Event Hubs からの車両センサー データの処理

HDInsight 上の Apache Storm を使用して Azure Event Hubs からの車両センサー データを処理する方法について説明します。 この例では、Azure Event Hubs からセンサー データを読み取り、Azure Cosmos DB に格納されたデータを参照することでデータを強化します。 データは Hadoop ファイル システム (HDFS) を使用して Azure Storage に格納されます。

![HDInsight and the Internet of Things (IoT) architecture diagram](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>概要

車両にセンサーを追加すると、履歴データの傾向に基づいて機器の問題を予測できます。 また、使用状況パターンの分析に基づいて将来のバージョンを改善することもできます。 MapReduce 処理を行うためには、その前にすべての車両のデータを Hadoop に迅速かつ効率的に読み込めなければなりません。 また、重大なエラー パス (エンジン温度、ブレーキなど) に関してリアルタイムで分析を行いたい場合もあります。

Azure Event Hubs はセンサーによって生成された大量のデータを処理するために構築されています。 Apache Storm は、データを HDFS に格納する前に読み込んで処理するのに使用できます。

## <a name="solution"></a>解決策

エンジン温度、大気温度、車両速度のテレメトリ データがセンサーによって記録されます。 データは車両識別番号 (VIN) とタイム スタンプとともに Event Hubs に送信されます。 そこから、HDInsight クラスター上の Apache Storm で実行されている Storm トポロジによってデータが読み取られ、処理され、HDFS に格納されます。

処理の際、VIN を使用して Cosmos DB からモデル情報が取得されます。 このデータは格納される前にデータ ストリームに追加されます。

Storm トポロジでは、次のコンポーネントが使用されます。

* **EventHubSpout** - Azure Event Hubs からデータを読み取ります。
* **TypeConversionBolt** - JSON 文字列を Event Hubs から次のセンサー データを含むタプルに変換します。
    * Engine temperature
    * 大気温度
    * 速度
    * VIN
    * タイムスタンプ
* **DataReferencBolt** - VIN を使用して Cosmos DB から車両モデルを検索します。
* **WasbStoreBolt** - HDFS (Azure Storage) にデータを格納します。

このソリューションのダイアグラムを次の図に示します。

![Storm トポロジ](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

## <a name="implementation"></a>実装

このシナリオの完全に自動化されたソリューションは、GitHub の [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) リポジトリの一部として入手可能です。 この例を使用するには、 [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md)に記されている手順に従ってください。

## <a name="next-steps"></a>次のステップ

Storm トポロジ例をさらにご覧になる場合、「 [HDInsight での Storm トポロジの例](hdinsight-storm-example-topology.md)」を参照してください。


