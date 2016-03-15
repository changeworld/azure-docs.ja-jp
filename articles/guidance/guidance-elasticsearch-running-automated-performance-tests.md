
<properties
   pageTitle="自動化された Elasticsearch パフォーマンス テストの実行 | Microsoft Azure"
   description="独自の環境でパフォーマンス テストを実行する方法の説明"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# 自動化された Elasticsearch パフォーマンス テストの実行

これは[一連の記事の一部](guidance-elasticsearch.md)です。

「[Tuning Data Ingestion Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの取り込みパフォーマンスを調整する)]」と「[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの集計とクエリのパフォーマンスを調整する)]」というドキュメントでは、サンプル Elasticsearch クラスターに対して実行されたさまざまなパフォーマンス テストについて説明します。

自動化で実行できるように、これらのテストはスクリプト化されました。このドキュメントでは、ご利用の環境でこのテストを再現する方法について説明しています。

## 前提条件

自動化されたテストには、次の項目が必要です。

-  Elasticsearch クラスター。

- 「[Creating a Performance Testing Environment for Elasticsearch on Azure (Azure で Elasticsearch のパフォーマンス テスト環境を構築する)]」というドキュメントに説明がある JMeter 環境設定。

- JMeter マスター VM だけにインストールされている次のソフトウェア。

    [Python 3.5.1](https://www.python.org/downloads/release/python-351/)


## テストの動作
このテストは JMeter を利用して実行されます。JMeter マスター サーバーはテスト計画を読み込み、実際にテストを実行する一連の JMeter 下位サーバーにそれを渡します。JMeter マスター サーバーは JMeter 下位サーバーを調整し、結果を累積します。

次のテスト計画が提供されます。

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx)。このテスト計画は 3 ノード クラスターで取り込みテストを実行します。

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx)。このテスト計画は 6 ノード クラスターで取り込みテストを実行します。

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx)。このテスト計画は 6 ノード クラスターで取り込みとクエリのテストを実行します。

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx)。このテスト計画は 6 ノード クラスターでクエリのみのテストを実行します。


ノードの増減が必要な場合、独自のシナリオの基礎として以上のテスト計画を利用できます。

テスト計画では JUnit Request サンプラーを利用し、テスト データを生成し、アップロードします。JMeter テスト計画はこのサンプルを作成し、実行し、パフォーマンス データの Elasticsearch ノードを監視します。詳細については、「[Tuning Data Ingestion Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの取り込みパフォーマンスを調整する)]」と「[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの集計とクエリのパフォーマンスを調整する)]」というドキュメントの付録を参照してください。

## JUnit JAR と依存関係の構築とデプロイ
回復力テストを実行する前に、performance/junitcode フォルダーにある JUnit テストをダウンロードし、コンパイルし、デプロイする必要があります。これらのテストは JMeter テスト計画で参照されます。詳細については、「[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance (Elasticsearch パフォーマンスをテストするために JMeter JUnit サンプラーをデプロイする)]」ドキュメントの「Importing an Existing JUnit Test Project into Eclipse (既存の JUnit テスト プロジェクトを Eclipse にインポートする)」手順を参照してください。

JUnit テスト: [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73) の 2 つのバージョンがあります。取り込みテストの実行にこのコードを使用します。これらのテストでは、Elasticsearch 1.73 から [Elasticsearch 2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2) が使用されます。クエリ テストの実行にこのコードを使用します。これらのテストでは、Elasticsearch 2.1 以降が使用されます。

適切な JAR ファイルと残りの依存関係を JMeter コンピューターにコピーします。このプロセスの説明は「[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance (Elasticsearch パフォーマンスをテストするために JMeter JUnit サンプラーをデプロイする)][]」にあります。

> **重要** JUnit テストをデプロイしたら、JMeter を利用し、この JUnit テストを参照するテスト計画を読み込み、構成し、*BulkInsertLarge* スレッド グループが正しい JAR ファイル、JUnit クラス名、テスト方法を参照することを確認します。
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> テストを実行する前に、更新したテスト計画を保存します。

## テスト インデックスの作成
各テストは、テストの実行時に指定された単一のインデックスに対して取り込みまたはクエリを実行します。「[Tuning Data Ingestion Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの取り込みパフォーマンスを調整する)]」と「[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの集計とクエリのパフォーマンスを調整する)]」というドキュメントの付録にあるスキーマを利用してインデックスを作成し、テスト シナリオに基づいてインデックスを構成する必要があります (doc 値の有効/無効、複数のレプリカなど)。 テスト計画では、インデックスが「*ctip*」という名前の単一タイプから構成されると想定されます。

## テスト スクリプト パラメーターの構成
JMeter サーバー コンピューターに次のテスト スクリプト パラメーター ファイルをコピーします。

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties)。このファイルにより、使用する JMeter テスト スレッドの数、テストの時間 (秒単位)、Elasticsearch クラスターのノード (あるいは、ロード バランサー) の IP アドレス、クラスターの名前が指定されます。

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  このファイルを編集し、テストとクラスターに適切な値を指定します。

* [query-config-win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) と [query-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini)。これらの 2 つのファイルには、同じ情報が含まれています。*win* ファイルは Windows のファイル名とパスに合わせて書式設定されています。*nix* ファイルは Linux のファイル名とパスに合わせて書式設定されています。

  ```ini
  [DEFAULT]
  debug=true #if true shows console logs.

  [RUN]
  pathreports=C:\\Users\\administrator1\\jmeter\\test-results\\ #path where tests results are saved.
  jmx=C:\\Users\\administrator1\\testplan.jmx #path to the JMeter test plan.
  machines=10.0.0.1,10.0.02,10.0.0.3 #IPs of the Elasticsearch data nodes separated by commas.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #Name of the reports separated by commas.
  tests=idx1 #Elasticsearch index name to test.
  properties=run.properties #Name of the properties file.
  ```

  このファイルを編集し、テスト結果の場所、実行する JMeter テスト計画の名前、Elasticsearch データ ノードの IP アドレス、生成される生パフォーマンス データを含むレポート、テスト対象のインデックスの名前を指定します。*run.properties* ファイルが別のフォルダーまたはディレクトリにある場合、このファイルに完全パスを指定します。

## テストの実行

* JMeter サーバー コンピューターの *run.properties* ファイルと *query-config-win.ini* (*query-config-nix.ini*) ファイルと同じフォルダーに [query-test.py](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) ファイルをコピーします。

* *jmeter.bat* (Windows) または *jmeter.sh* (Linux) がご利用の環境の実行可能パスにあることを確認します。

* コマンド ラインから *query-test.py* スクリプトを実行し、テストを実行します。

  ```cmd
  py query-test.py
  ```

* テストが完了したら、*query-config-win.ini* (*query-config-nix.ini*) ファイルに指定されている CSV ファイルのセットとして結果が保存されます。Excel を使用し、このデータを分析し、グラフ化できます。


[Tuning Data Ingestion Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの取り込みパフォーマンスを調整する)]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure (Azure で Elasticsearch のデータの集計とクエリのパフォーマンスを調整する)]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Creating a Performance Testing Environment for Elasticsearch on Azure (Azure で Elasticsearch のパフォーマンス テスト環境を構築する)]: guidance-elasticsearch-creating-performance-testing-environment.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance (Elasticsearch パフォーマンスをテストするために JMeter JUnit サンプラーをデプロイする)]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->
