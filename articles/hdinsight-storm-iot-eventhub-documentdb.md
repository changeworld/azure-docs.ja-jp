<properties
 pageTitle="Azure Event Hub および HDInsight 上の Apache Storm を使用して車両センサー データを処理する"
 description="HDInsight 上の Apache Storm を使用して Azure Event Hub からの車両センサー データを処理する方法について説明します。処理の際、DocumentDB からの車両モデル情報を検索してデータが強化されます。最終的にデータが Azure Storage に書き込まれます。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="04/28/2015"
ms.author="larryfr"/>

# HDInsight 上の Apache Storm を使用して Azure Event Hub からのセンサー データを処理する

HDInsight 上の Apache Storm を使用して Azure Event Hub からの車両センサー データを処理する方法について説明します。この例では Azure Event Hub からセンサー データを読み取り、Azure DocumentDB に格納されたデータを参照してデータを強化し、最後に Hadoop ファイル システム (HDFS) を使用して Azure Storage にデータを格納します。

![アーキテクチャ ダイアグラム](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## 概要

車両にセンサーを追加すると、履歴データの傾向に基づいて機器の問題を予測できるだけでなく、使用状況のパターン分析に基づいてその後のバージョンを改良できます。この分析には従来の MapReduce バッチ処理を使用できますが、MapReduce 処理を行うためには、その前にすべての車両のデータを Hadoop に迅速かつ効率的に読み込めなければなりません。また、重大なエラー パス (エンジン温度、ブレーキなど) に関してリアルタイムで分析を行いたい場合もあります。

Azure Event Hub はセンサーによって生成された大量のデータを処理するためにビルドされていて、HDInsight 上で Apache Storm を使用してそれらのデータを読み込んで処理してから、(Azure Storage によって戻された) データを HDFS に格納してさらに MapReduce 処理を行えます。

## 解決策

エンジン温度、大気温度、車両速度のテレメトリ データがセンサーによって記録され、車両識別番号 (VIN) とタイムスタンプとともに Event Hub に送信されます。そこから、HDInsight クラスター上の Apache Storm で実行されている Storm トポロジによってデータが読み取られ、処理され、HDFS に格納されます。

処理の際、VIN を使用して Azure DocumentDB からモデル情報が取得されます。この情報がデータに追加されてから、格納されます。

Storm トポロジでは、次のコンポーネントが使用されます。

* **EventHubSpout** - Azure Event Hub からデータを読み取ります。

* **TypeConversionBolt** - Event Hub の JSON 文字列を、エンジン温度、大気温度、速度、VIN、タイムスタンプそれぞれのデータ値が含まれるの個々のデータ値を含むタプルに変換します。

* **DataReferencBolt** - VIN を使用して DocumentDB から車両モデルを検索します。

* **WasbStoreBolt** - HDFS (Azure Storage) にデータを格納します。

このソリューションのダイアグラムを次に示します。

![Storm トポロジ](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE]これは簡略化したダイアグラムで、ソリューション内の各コンポーネントには複数のインスタンスを含めることができます。たとえば、トポロジ内にある各コンポーネントの複数のインスタンスを、HDInsight クラスター上の Storm にある複数のノードに分散できます。

## 実装

このシナリオの完全に自動化されたソリューションは、GitHub の <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight-Storm-Examples</a> リポジトリの一部として利用できます。この例を使用するには、[IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md) に記されている手順に従ってください。

## 次のステップ

Storm トポロジ例をさらにご覧になる場合、「[HDInsight での Storm トポロジの例](hdinsight-storm-example-topology.md)」を参照してください。

<!--HONumber=52-->
