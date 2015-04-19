<properties 
	pageTitle="HDInsight (Hadoop) での Apache Storm の概要" 
	description="HDInsight (Hadoop) での Apache Storm の使用方法について説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# HDInsight での Apache Storm の概要

## Storm とは

[Apache Storm][apachestorm] は、リアルタイムでデータを処理できる、フォールト トレランスに優れたオープン ソースの分散型計算システムです。また、Storm のソリューションは、最初に正常に処理されなかったデータを再生する機能を備え、保証されたデータ処理を実現します。

## HDInsight での Storm とは

Storm は、管理されるクラスターとして、Azure 環境に統合されて提供されます。この環境内で、より大きな Azure のソリューションの一部として使用されることもあります。たとえば、Storm は、Service Bus Queues や Event Hub などのサービスのデータを利用して、Websites や Cloud Services を使用してデータの視覚化を可能にします。Storm クラスターは、Azure Virtual Network 上に構成されることもあります。これにより、同じ Virtual Network にある他のリソースとの通信の遅延が減少し、プライベート データセンター内にあるリソースとのセキュリティで保護された通信も可能になります。

Storm の使用方法については、「[HDInsight (Hadoop) での Storm の使用][gettingstarted]」を参照してください。

## HDInsight での Storm のデータの処理方法

Storm クラスターは、HDInsight や Hadoop から使用することが一般的な MapReduce ジョブではなく、**トポロジ**を処理します。Storm クラスターには、**Nimbus** を実行するヘッド ノードと **Supervisor** を実行するワーカー ノードの 2 種類のノードが含まれます。

* **Nimbus** - Hadoop の JobTracker 同様、クラスター全体へのコードの配布、マシンへのタスクの割り当て、障害の監視を処理します。HDInsight は 2 つの Nimbus ノードを提供するため、Storm クラスターに単一障害点が生じません。

* **Supervisor** - 各ワーカー ノードの supervisor は、ノードの**ワーカー プロセス**の開始と停止を処理します。

* **ワーカー プロセス** - ワーカー ノードは、**トポロジ**のサブセットを実行します。実行中のトポロジは、クラスター内の多くのワーカー プロセスに分散されます。

* **トポロジ** - データ **ストリーム**を処理する計算処理のグラフを定義します。MapReduce ジョブとは異なり、トポロジは停止されるまで実行されます。

* **ストリーム** - **タプル**のバインドされていないコレクション。ストリームは、**スパウト**と**ボルト**により生成され、**ボルト**によって利用されます。

* **タプル** - 動的に型指定される値の名前付きのリスト

* **スパウト** - データ ソースのデータを利用し、1 つ以上の**ストリーム**を発します。

	> [AZURE.NOTE] 多くの場合、データは、Kafka、Azure Service Bus Queues、Event Hubs などのキューから読み取られます。システムが停止した場合、キューが確実にデータを保持します。

* **ボルト** - **ストリーム**を利用し、**タプル**に対して処理を実行します。**ストリーム**を発する場合もあります。ボルトは、キュー、HDInsight HBase、BLOB、その他のデータ ストアなどの、外部ストレージへのデータの書き込みも処理します。

* **Thrift** - Apache Thrift は、拡張性のある、多言語のサービス開発のためのソフトウェア フレームワークです。これを使用すると、C++、Java、Python、PHP、Ruby、Erlang、Perl、Haskell、C#、Cocoa、JavaScript、Node.js、Smalltalk、その他の言語の間で動作するサービスを構築できます。

	* **Nimbus** は Thrift サービスであり、**トポロジ**は Thrift の定義であるため、さまざまなプログラミング言語を使用してトポロジを開発できます。 

Storm コンポーネントの詳細については、apache.org の「[Storm tutorial (Storm チュートリアル)][apachetutorial]」を参照してください。

## シナリオ:Storm の使用事例

以下に、Apache Storm を使用する一般的なシナリオをいくつか示します。実際のシナリオの詳細については、「[how companies are using Storm (企業での Storm の使用事例)][poweredby]」をお読みください。

### リアルタイム分析

Storm はリアルタイムにデータ ストリームを処理するため、データ ストリームが到着するとその中から特定のイベントやパターンを探して反応する必要があるデータ分析に最適です。たとえば、Storm トポロジはセンサー データを監視してシステムの状態を判断し、特定のパターンが発生したときにアラートのために SMS メッセージを生成することがあります。

### Extract Transform Load (ETL)

ETL は、Storm の処理の副次効果とほぼ見なすことができます。たとえば、リアルタイム分析を使用して不正の検出を実行している場合には、既にデータを取り込んで変換しています。将来の分析で使用するために、ボルトで HBase、Hive、またはその他のデータ ストアにデータを格納することも想定できます。

### 分散 RPC

分散 RPC は、Storm を使用して作成できるパターンです。要求が Storm に伝わり、次に計算処理が複数のノードに分散され、最終的に結果のストリームが待機中のクライアントに返されます。

分散 RPC および Storm に付属する DRPCClient の詳細については、「[Distributed RPC (分散 RPC)](https://storm.incubator.apache.org/documentation/Distributed-RPC.html)」を参照してください。

### オンライン機械学習

Storm は、Mahout に基づくソリューションなど、あらかじめバッチ プロセスでトレーニングされた機械学習ソリューションと一緒に使用できます。ただし、その汎用的な分散型の計算処理モデルは、ストリームベースの機械学習ソリューションにも門戸を開きます。たとえば、「[Scalable Advanced Massive Online Analysis (SAMOA) project (Scalable Advanced Massive Online Analysis (SAMOA) プロジェクト)][samoa]」は機械学習ライブラリであり、ストリーム処理を使用し、Storm と連携して動作することができます。

## 使用できるプログラミング言語

HDInsight Storm クラスターは、.NET、Java、Python を既定でサポートします。Storm は[他の言語をサポート](https://storm.incubator.apache.org/about/multi-language.html)しますが、その多くが、HDInsight クラスターへの追加のプログラミング言語のインストールおよびその他の構成の変更を必要とします。 

### .NET

SCP は、.NET 開発者がトポロジ (スパウトとボルトを含む) を設計し実装できるプロジェクトです。SCP は、Storm クラスターによって既定でサポートされています。

SCP を使用した開発の詳細については、「[HDInsight の Storm で SCP.NET と C# を使用したストリーミング データ処理アプリケーションの開発](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)」を参照してください。

### Java

目にすることがあるほとんどの Java の例は、プレーンな Java か Trident です。Trident は、結合、集計、グループ化、フィルタリングなどの実行を容易にするための、高レベルの抽象概念です。ただし、Trident は、タプルのバッチに作用し、ここでは未加工の Java ソリューションが 1 回に 1 タプルずつストリームを処理します。

Trident の詳細については、apache.org の「[Trident tutorial (Trident のチュートリアル)](https://storm.incubator.apache.org/documentation/Trident-tutorial.html)」を参照してください。

未加工の Java および Trident トポロジの両方の例については、HDInsight Storm クラスターの **%storm_home%\contrib\storm-starter** ディレクトリを参照してください。

## 一般的な開発パターンの例

### メッセージの処理の保証

Storm は、さまざまなレベルでメッセージの処理を保証します。たとえば、基本的な Storm アプリケーションは "最低 1 回" の処理を保証できますが、Trident は "厳密に 1 回" の処理を保証できます。

詳細については、apache.org の「[Guarantees on data processing (データ処理の保証)](https://storm.apache.org/about/guarantees-data-processing.html)」を参照してください。

### BasicBolt

入力タプルを読み込み、0 個以上のタプルを発行し、実行メソッドの終了直後に入力タプルを確認するというパターンは一般的であるため、Storm は、[IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) インターフェイスを提供してこのパターンを自動化しています。

### 結合

2 つのデータ ストリームの結合は、アプリケーションによって異なります。たとえば、複数のストリームの各タプルを 1 つの新しいストリームに結合したり、特定のウィンドウに対してのみタプルのバッチを結合したりする場合があります。いずれの場合も、結合は [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) を使用して実行されます。これは、ボルトへのタプルのルーティングの定義方法の 1 つです。

次の Java の例では、fieldsGrouping は、**MyJoiner** ボルトへの、コンポーネント "1"、"2"、"3" から発生したタプルのルーティングに使用されています。

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2")); 

### バッチ処理

バッチ処理はいくつかの方法で実行できます。基本的な Storm Java トポロジでは、単純なカウンターを使用して、X 個のタプルをバッチ処理してから発したり、tick タプルと呼ばれる内部の時間調整メカニズムを使用して X 秒ごとにバッチを発したりできます。

tick タプルを使用する例については、「[Storm と HDInsight を使用したセンサー データの分析](hdinsight-storm-sensor-data-analysis.md.md)」を参照してください。

Trident を使用している場合、これはタプルのバッチ処理を基盤とします。

### Caching)

メモリ内キャッシュは、頻繁に使用されるアセットをメモリ内に保持することで、処理速度を上げるためのメカニズムとしてよく使用されます。トポロジは、複数のノードにわたって、また各ノード内の複数のプロセスにわたって分散されるため、キャッシュの検索に使用されるフィールドを含むタプルが常に同じプロセスにルーティングされるように、[fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) の使用を検討する必要があります。これにより、プロセス間でのキャッシュ エントリの重複が回避されます。

### トップ N のストリーミング

トポロジが、Twitter のトレンドのトップ 5 など、 'top' N の値の計算に依存する場合、トップ N の値を同時に計算し、計算の出力をグローバル値に結合する必要があります。これには、[fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) を使用し、並列のボルトにフィールド別にルーティングし、データをフィールド値で分割して、最終的に、トップ N の値を一括で決定するボルトにルーティングします。

この詳細については、「[RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java)」の例を参照してください。

## 次のステップ

* [HDInsight での Storm の使用][gettingstarted]

* [Storm と HDInsight を使用したセンサー データの分析](hdinsight-storm-sensor-data-analysis.md)

* [HDInsight の Storm で SCP.NET と C# を使用したストリーミング データ処理アプリケーションの開発](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)

[apachestorm]: https://storm.incubator.apache.org
[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[poweredby]: https://storm.incubator.apache.org/documentation/Powered-By.html
[gettingstarted]: /ja-jp/documentation/articles/hdinsight-storm-getting-started
<!--HONumber=42-->
