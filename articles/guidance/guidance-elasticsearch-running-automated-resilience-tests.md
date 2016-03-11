<properties
   pageTitle="Elasticsearch の自動回復性テストの実行 | Microsoft Azure"
   description="独自の環境で回復性テストを実行する方法について説明します。"
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

# Elasticsearch の自動回復性テストの実行

これは[一連の記事の一部](guidance-elasticsearch.md)です。

「[Resilience and Recovery Testing]」では、システムが一般的な障害にどの程度対処し、どの程度回復したかを確認するために、サンプルの Elasticsearch クラスターに対して実行された一連のテストについて説明しています。次の 4 つのシナリオがテストされました。

- **ノードの障害が発生し、データ損失を伴わずに再起動する**: データ ノードが停止され、5 分後に再起動されます。Elasticsearch は、この期間に失われたシャードを再割り当てしないように構成されているため、シャードを移動するための追加の I/O は発生しません。ノードが再起動すると、回復プロセスによって、そのノードのシャードが最新の状態に戻されます。

- **致命的なデータ損失を伴うノードの障害が発生する**: データ ノードが停止され、致命的なディスク障害をシミュレートするために、データ ノードに保持されているデータが消去されます。5 分後にノードは再起動されますが、実質的には元のノードの代わりとして機能します。回復プロセスでは、このノードの失われたデータを再構築する必要があり、他のノードで保持されているシャードの再配置が必要になる場合もあります。

- **ノードの障害が発生し、データ損失はないが、シャードが再割り当てされて再起動する**: データ ノードが停止され、ノードが保持するシャードが他のノードに再割り当てされます。その後、ノードが再起動され、クラスターを再バランス調整するために追加の再割り当てが発生します。

- **ローリング更新**: クラスターの各ノードが停止され、ソフトウェアの更新後に再起動されるマシンをシミュレートするために短い間隔の後に再起動されます。常に 1 つのノードだけが停止されます。ノードの停止中、シャードは再割り当てされません。

これらのテストは、自動的に実行できるようにスクリプト化されました。このドキュメントでは、独自の環境でこれらのテストを再現する方法について説明します。

## 前提条件

自動テストには次の項目が必要です。

- Elasticsearch クラスター。

- [パフォーマンス テストのガイダンス]に従った JMeter 環境のセットアップ。

- JMeter マスター VM にのみインストールされた次のソフトウェア。

    - Java ランタイム 7

    - Nodejs 4.x.x 以降

    - Git コマンド ライン ツール

## スクリプトの機能

テスト スクリプトは、JMeter マスター VM で実行されます。実行するテストを選択すると、スクリプトによって次の一連の操作が実行されます。

1.  指定したパラメーターを渡す JMeter テスト計画を開始します。

2.  テストで必要な操作を実行するスクリプトを、クラスターの指定した VM にコピーします。コピー先には、パブリック IP アドレスを持つ任意の VM を指定できます。また、[Azure Elasticsearch クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch)を使用してクラスターを構築した場合は、*ジャンプボックス* VM を指定することもできます。

3.  VM (またはジャンプボックス) でスクリプトを実行します。

次の図は、テスト環境と Elasticsearch クラスターの構造を示しています。テスト スクリプトでは、ノードの停止や再起動など、Elasticsearch のさまざまな操作を実行するために、SSH を使用してクラスターの各ノードに接続します。

![](./media/guidance-elasticsearch/resilience-testing1.png)

## JMeter テストの設定

回復性テストを実行する前に、*resiliency/jmeter/tests* フォルダーにある JUnit テストをコンパイルしてデプロイする必要があります。これらのテストは JMeter テスト計画によって参照されます。詳細については、「[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance][]」の「Importing an Existing JUnit Test Project into Eclipse」をご覧ください。

次のフォルダーに JUnit テストの 2 つのバージョンがあります。

- **Elasticsearch17**: このフォルダー内のプロジェクトは、Elasticsearch17.jar ファイルを生成します。この JAR は、Elasticsearch バージョン 1.7.x のテストに使用します。

- **Elasticsearch20**: このフォルダー内のプロジェクトは、Elasticsearch20.jar ファイルを生成します。この JAR は、Elasticsearch バージョン 2.0.0 以降のテストに使用します。

適切な JAR ファイルと残りの依存関係を JMeter マシンにコピーします。このプロセスは、「[Considerations for JMeter]」の「Deploying a JUnit Test to JMeter」に記載されています。

## 各ノードの VM のセキュリティの構成

テスト スクリプトには、クラスターの各 Elasticsearch ノードにインストールする認証証明書が必要です。これにより、さまざまな VM に接続するときに、ユーザー名またはパスワードの入力を求められることなく、スクリプトを自動的に実行できるようになります。

まず、Elasticsearch クラスターのノードのいずれか (またはジャンプボックス VM) にログインし、次のコマンドを実行して認証キーを生成します。

```Shell
ssh-keygen -t rsa
```

Elasticsearch ノード (またはジャンプボックス) に接続中に、Elasticsearch クラスターのすべてのノードで次のコマンドを実行します。`<username>` を各 VM の有効なユーザーの名前に置き換え、`<nodename>` を Elasticsearch ノードをホストする VM の IP アドレスの DNS 名に置き換えます。これらのコマンドを実行するときに、ユーザーのパスワードの入力を求められます。詳細については、「[SSH login without password](http://www.linuxproblem.org/art_9.html)」をご覧ください。

```Shell
ssh <username>@<nodename> mkdir -p .ssh (
cat .ssh/id\_rsa.pub | ssh <username>*@<nodename> 'cat &gt;&gt; .ssh/authorized\_keys'
```

## テスト スクリプトのダウンロードと構成

テスト スクリプトは Git リポジトリに用意されています。次の手順に従って、スクリプトをダウンロードし、構成します。

テストを実行する JMeter マスター マシンで Git デスクトップ ウィンドウ (Git BASH) を開き、次のように、スクリプトが格納されたリポジトリを複製します。

```Shell
git clone https://github.com/mspnp/azure-guidance.git
```

*resiliency-tests* フォルダーに移動し、次のコマンドを実行して、テストを実行するために必要な依存関係をインストールします。

```Shell
npm install
```

JMeter マスターが Windows で実行されている場合は、[PLINK をダウンロード](http://the.earth.li/~sgtatham/putty/latest/x86/plink.exe)し、*resiliency-tests/lib* フォルダーにコピーします。

JMeter マスターが Linux で実行されている場合、PLINK をダウンロードする必要はありませんが、JMeter マスターと「[各ノードの VM のセキュリティの構成](#configuring-vm-security-for-each-node)」に記載された手順で使用した Elasticsearch ノードまたはジャンプボックスの間でパスワード不要の SSH を構成する必要があります。

`config.js` ファイルを編集し、テスト環境と Elasticsearch クラスターに合わせて次の構成パラメーターを編集します。これらのパラメーターは、すべてのテストに共通するものです。

| 名前 | 説明 | 既定値 |
| ---- | ----------- | ------------- |
| `jmeterPath` | JMeter が配置されているローカル パス。 | `C:/apache-jmeter-2.13` |
| `resultsPath` | スクリプトが結果をダンプする相対ディレクトリ。 | `results` |
| `verbose` | スクリプトが詳細モードで出力するかどうかを示します。 | `true` |
| `remote` | JMeter テストをローカルとリモート サーバーのどちらで実行するかを示します。 | `true` |
| `cluster.clusterName` | Elasticsearch クラスターの名前。 | `elasticsearch` |
| `cluster.jumpboxIp` | ジャンプボックス マシンの IP アドレス。 |-| 
| `cluster.username` | クラスターのデプロイ時に作成した管理ユーザー。 |-| 
| `cluster.password` | 管理ユーザーのパスワード。 |-| 
| `cluster.loadBalancer.ip` | Elasticsearch のロード バランサーの IP アドレス。 |-| 
| `cluster.loadBalancer.url` | ロード バランサーのベース URL。 |-|

## テストの実行

*resiliency-tests* フォルダーに移動し、次のコマンドを実行します。

```Shell
node app.js
```

次のメニューが表示されます。

![](./media/guidance-elasticsearch/resilience-testing2.png)

実行するシナリオの番号 (`11`、`12`、`13`、または `21`) を入力します。

シナリオを選択すると、テストが自動的に実行されます。*results* ディレクトリの下に作成されたフォルダーに、一連の CSV ファイルとして結果が保存されます。実行ごとに独自の results フォルダーがあります。Excel を使用して、このデータを分析し、グラフにすることができます。

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[パフォーマンス テストのガイダンス]: guidance-elasticsearch-performance-testing-environment.md
[JMeter guidance]: guidance-elasticsearch-implementing-jmeter.md
[Considerations for JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md
[Query aggregation and performance]: guidance-elasticsearch-query-aggregation-performance.md
[Resilience and Recovery]: guidance-elasticsearch-resilience-recovery.md
[Resilience and Recovery Testing]: guidance-elasticsearch-resilience-testing.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->
