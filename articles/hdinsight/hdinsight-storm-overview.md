---
title: "HDInsight での Apache Storm の概要 | Microsoft Docs"
description: "HDInsight の Apache Storm について紹介しています。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/31/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 73e524bf9e7d51cee841a3c5599ab33aa31cbc37
ms.lasthandoff: 04/06/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>HDInsight での Apache Storm の概要: Hadoop のリアルタイム分析

Azure HDInsight の Apache Storm を使って分散型のリアルタイム分析ソリューションを作成できます。

Storm は、フォールト トレランスに優れたオープン ソースの分散型計算システムです。 Storm を Hadoop で使用することにより、リアルタイムでデータを処理することができます。 また、Storm のソリューションは、最初に正常に処理されなかったデータを再生する機能を備え、保証されたデータ処理を実現します。

## <a name="how-does-storm-work"></a>Storm のしくみ

Storm は、HDInsight や Hadoop から使用することが一般的な MapReduce ジョブではなく、トポロジを処理します。 トポロジは、有向非巡回グラフ (DAG) に配置されている複数のコンポーネントで構成されます。 次の図は、基本的なワード カウント トポロジ内のコンポーネント間でデータがどのように流れるかを示しています。

![Storm トポロジでのコンポーネントの配置の例](./media/hdinsight-storm-overview/wordcount-topology.png)

* スパウト コンポーネントは、データをトポロジに取り込みます。 1 つ以上のストリームをこのトポロジに出力します。

* ボルト コンポーネントは、スパウトまたはその他のボルトから出力されたストリームを使用します。 ボルトは、必要に応じて、新しいストリームをトポロジに出力できます。 また、HDFS や HBase などの永続的ストレージへのデータの書き込みも処理します。

## <a name="why-use-storm-on-hdinsight"></a>HDInsight で Apache Storm を使用する理由

HDInsight における Storm の主なメリットは次のとおりです。

* 稼働時間 99.9% の SLA を提供する、管理されたサービスとして実行されます。

* 作成中または作成後にクラスターに対してスクリプトを実行することで、簡単にカスタマイズできます。 詳細については、[スクリプト アクションを使った HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関する記事を参照してください。

* 各種の言語に対応しています。 Storm コンポーネントは、Java、C#、Python など、さまざまな言語で作成することができます。

    * C# トポロジの開発、管理、監視を目的として、Visual Studio と HDInsight を連携させることができます。 詳細については、[HDInsight Tools for Visual Studio を使用した C# Storm トポロジの開発](hdinsight-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

    * Trident Java インターフェイスをサポートします。 メッセージの厳密に 1 回の処理、トランザクションのデータストア永続化、一般的なストリーム分析操作のセットをサポートする Storm トポロジの作成が可能になります。

*  クラスターのスケールアップとスケールダウンが容易です。 Storm トポロジの実行に影響を与えることなく、worker ノードを追加または削除することができます。

* 次の Azure サービスと連携します。

    * Azure Event Hubs

    * Azure Virtual Network

    * Azure SQL Database

    * Azure Storage (Azure Storage)

    * Azure DocumentDB

* Virtual Network を使用して、複数の HDInsight クラスターの機能を安全に組み合わせることができます。 HDInsight、HBase、Hadoop クラスターを使用する分析パイプラインを作成できます。

リアルタイムの分析ソリューション用の Storm を使用している企業の一覧については、[Apache Storm を使用している企業](https://storm.apache.org/documentation/Powered-By.html)に関するページを参照してください。

Storm の使用方法については、[HDInsight での Storm の使用][gettingstarted]に関する記事を参照してください。

## <a name="ease-of-creation"></a>作成のしやすさ

HDInsight 上に新しい Storm クラスターを数分でプロビジョニングできます。 Storm クラスターの作成の詳細については、[HDInsight での Storm の使用](hdinsight-apache-storm-tutorial-get-started-linux.md)に関する記事を参照してください。

## <a name="ease-of-use"></a>使いやすさ

* __Secure Shell (SSH) 接続__: SSH を使用してインターネット経由で HDInsight クラスターのヘッド ノードにアクセスすることができます。 クラスターには、SSH を使用して直接コマンドを実行できます。

  詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* __Web 接続__: HDInsight クラスターでは Ambari Web UI が提供されます。 Ambari Web UI を使用すれば、クラスター上のサービスの監視、構成、管理を簡単に行うことができます。 HDInsight における Storm では Storm UI も提供されます。 Storm UI を使用すれば、お使いのブラウザーから Storm トポロジの実行を監視および管理できます。

  詳細については、[Ambari Web UI を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari.md)に関する記事と、「[Storm UI を使用して監視および管理する](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui)」を参照してください。

* __Azure PowerShell と Azure CLI__: PowerShell と CLI はいずれも、HDInsight やその他の Azure サービスを操作するためにクライアント システムから使用できるコマンドライン ユーティリティです。

* __Visual Studio 統合__: Azure Data Lake Tools for Visual Studio には、SCP.NET フレームワークを使用して C# Storm トポロジを作成するためのプロジェクト テンプレートが含まれています。 また、Data Lake Tools には、HDInsight の Storm によるソリューションのデプロイ、監視、管理のためのツールも用意されています。

  詳細については、[HDInsight Tools for Visual Studio を使用した C# Storm トポロジの開発](hdinsight-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

## <a name="integration-with-other-azure-services"></a>その他の Azure サービスとの統合

* __Azure Data Lake Store__: Storm で Data Lake Store を使用する例については、[HDInsight の Apache Storm による Azure Data Lake Store の使用](hdinsight-storm-write-data-lake-store.md)に関する記事を参照してください。

* __Event Hubs__: Storm で Event Hubs を使用する例については、次のドキュメントを参照してください。

    * [HDInsight で Storm の Java ベースのトポロジを開発する](hdinsight-storm-develop-java-topology.md)

    * [HDInsight で Storm を使用して Azure Event Hubs のイベントを処理する (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__、__DocumentDB__、__Event Hubs__、__HBase__: Data Lake Tools for Visual Studio にテンプレートの例が含まれています。 詳細については、[HDInsight の Storm 向けの C# トポロジの開発](hdinsight-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

## <a name="reliability"></a>信頼性

Storm は、データ分析が多数のノードにまたがる場合でも、各受信メッセージを完全に処理することを常に保証します。

Nimbus ノードは Hadoop JobTracker に同様の機能を提供し、Zookeeper を介して、タスクをクラスターの他のノードに割り当てます。 Zookeeper ノードは、クラスターに調整を提供し、Nimbus と worker ノードの Supervisor 処理間の通信を容易にします。 処理中のノードの 1 つがダウンした場合、Nimbus ノードに通知され、タスクと関連付けられているデータが別のノードに割り当てられます。

Storm の既定の構成では、Nimbus ノードは 1 つだけです。 HDInsight の Storm では、2 つの Nimbus ノードを実行します。 プライマリ ノードが失敗すると、プライマリ ノードが復旧中は、HDInsight クラスターはセカンダリ ノードに切り替わります。 次の図は、HDInsight 上の Storm に使用されるタスク フローの構成を示しています。

![Nimbus、Zookeeper、スーパーバイザのダイアグラム](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>スケール

作成中にクラスター内のノード数を指定できますが、ワークロードに一致するようにクラスターを拡大、縮小できます。 すべての HDInsight クラスターは、データの処理中であっても、そのノード数を変更することができます。

> [!NOTE]
> スケーリングによって追加された新しいノードを利用するには、クラスター サイズを増やす前に開始されていたトポロジを再調整する必要があります。

## <a name="support"></a>サポート

HDInsight の Storm には、完全なエンタープライズレベルの継続性サポートが付属します。 HDInsight の Storm では、99.9% の SLA も保証されています。 つまり、クラスターは、99.9% 以上の時間において外部に接続されることを保証します。

詳細については、「[Azure のサポート](https://azure.microsoft.com/support/options/)」を参照してください。

## <a name="common-use-cases"></a>一般的なユース ケース

以下に、HDInsight で Storm を使用する一般的なシナリオをいくつか示します。 

* モノのインターネット(IoT)
* 不正行為の検出
* ソーシャル分析
* 抽出、変換、読み込み (ETL: Extraction、Transformation、Loading)
* ネットワーク監視
* Search
* Mobile Engagement


実際のシナリオの詳細については、[企業での Storm の使用事例](https://storm.apache.org/documentation/Powered-By.html)に関するページを参照してください。

## <a name="development"></a>開発

.NET 開発者は、Data Lake Tools for Visual Studio を使い、トポロジの設計と実装を C# で行うことができます。 Java と C# のコンポーネントを使用するハイブリッド トポロジを作成することもできます。

詳細については、[Visual Studio を使用した HDInsight での Storm の C# トポロジの開発](hdinsight-storm-develop-csharp-visual-studio-topology.md)に関する記事を参照してください。

任意の IDE を使用して、Java ソリューションを開発することもできます。 詳細については、[HDInsight での Storm の C# トポロジの開発](hdinsight-storm-develop-java-topology.md)に関する記事を参照してください。

Storm コンポーネントの開発には、Python も使用できます。 詳細については、[HDInsight での Python を使用した Storm トポロジの開発](hdinsight-storm-develop-python-topology.md)に関する記事を参照してください。

## <a name="common-development-patterns"></a>一般的な開発パターン

### <a name="guaranteed-message-processing"></a>メッセージの処理の保証

Storm は、さまざまなレベルでメッセージの処理を保証します。 たとえば、基本的な Storm アプリケーションは "最低 1 回" の処理を保証できますが、Trident は "厳密に 1 回" の処理を保証できます。

詳細については、apache.org の「 [Guarantees on data processing (データ処理の保証)](https://storm.apache.org/about/guarantees-data-processing.html) 」をご覧ください。

### <a name="ibasicbolt"></a>IBasicBolt

入力タプルを読み込み、0 個以上のタプルを発行し、実行メソッドの終了直後に入力タプルを確認するというパターンが一般的です。 Storm には、このパターンを自動化する [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) インターフェイスが用意されています。

### <a name="joins"></a>結合

データ ストリームの結合方法は、アプリケーションによって異なります。 たとえば、複数のストリームの各タプルを 1 つの新しいストリームに結合したり、特定のウィンドウに対してのみタプルのバッチを結合したりする場合があります。 いずれの場合も、結合は [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) を使用して実行されます。これは、ボルトへのタプルのルーティングの定義方法の 1 つです。

次の Java の例では、fieldsGrouping は、MyJoiner ボルトへの、コンポーネント "1"、"2"、"3" から発生したタプルのルーティングに使用されています。

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>バッチ

Storm には "tick タプル" と呼ばれる内部の時間調整メカニズムが備わっており、これを使って X 秒ごとにバッチを発することができます。

C# コンポーネントからの tick タプルの使用例については、[PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java) を参照してください。

Trident はタプルのバッチ処理を基盤とします。

### <a name="caches"></a>キャッシュ

メモリ内キャッシュは、頻繁に使用されるアセットをメモリ内に保持することで、処理速度を上げるためのメカニズムとしてよく使用されます。 トポロジは複数のノード (また各ノード内の複数のプロセス) にまたがって分散されるため、[fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) の使用を検討してください。 `fieldsGrouping` を使用すると、キャッシュの検索に使用されるフィールドを含んだタプルが常に同じプロセスにルーティングされます。 このグループ化の機能により、プロセス間でのキャッシュ エントリの重複が回避されます。

### <a name="stream-top-n"></a>"トップ N" のストリーミング

トポロジが "トップ N" の値の計算に依存する場合、トップ N の値を並行して計算する必要があります。 その後、これらの計算の出力をグローバル値に結合してください。 この操作を実行するには、[fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) を使用して、並列処理の対象となるフィールド別にルーティングし、トップ N の値を一括で決定するボルトにルーティングします。

"トップ N" の値の計算例については、[RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) の例を参照してください。

## <a name="logging"></a>ログの記録

Storm では、Apache Log4j を使用して情報をログに記録します。 既定では大量のデータがログに記録されるため、情報を調べるのが困難になる可能性があります。 Storm トポロジの一部にログの構成ファイルを含めることにより、ログ記録の動作を制御することができます。

ログ記録を構成する方法を示すトポロジの例については、HDInsight で Storm を使用した [Java ベースの WordCount](hdinsight-storm-develop-java-topology.md) の例を参照してください。

## <a name="next-steps"></a>次のステップ

HDInsight 上の Storm を使用したリアルタイム分析ソリューションの詳細について学習します。

* [HDInsight での Storm の使用][gettingstarted]
* [HDInsight 上の Storm に関するトポロジ例](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

