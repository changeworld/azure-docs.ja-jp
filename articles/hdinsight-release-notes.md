<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Microsoft HDInsight リリース ノート

## 2014 年 8 月 21 日リリース

-   以下の新しい WebHCat 構成 (HIVE-7155) を追加しました。この構成は Templeton コントローラーのジョブに対し、既定のメモリ上限を 1 GB (以前の既定値は 512 MB) に設定します。

    -   templeton.mapper.memory.mb (=1024)
    -   これにより、メモリ上限が低いために特定の Hive クエリで発生する次のエラーに対処します。"Container is running beyond physical memory limits (コンテナーは物理メモリ上限を超過して実行しています)"
    -   クラスター作成時に PowerShell SDK で以下のコマンドを使用すると、この構成値を使用していない既定値の 512 に戻すことができます。

        Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}

-   zookeeper ロールのホスト名が zookeeper に変更されました。これはクラスター内の名前解決には影響しますが、外部の REST API には影響しません。ホスト名 zookeepernode を使用するコンポーネントがある場合は、代わりに新しい名前を使用するようにそのコンポーネントを更新する必要があります。3 つの zookeeper ノードの新しい名前は以下のとおりです。

    -   zookeeper0
    -   zookeeper1
    -   zookeeper2
-   HBase のバージョン サポート マトリックスが更新されました。運用環境の HBase ワークロードでサポートされるのは、バージョン HDInsight 3.1 (HBase バージョン 0.98) のみです。プレビューで利用可能だったバージョン 3.0 は、今後はサポートされなくなります。移行期間中はバージョン 3.0 のクラスターを引き続き作成できます。

## 2014 年 7 月 28 日リリース
-   **HDInsight が新しいリージョンで利用可能**: このリリースでは、HDInsight の地理的なプレゼンスを以下の新しい 3 つのリージョンに拡張しています。HDInsight の顧客は、これらのリージョンでクラスターを作成できるようになりました。
    -   東アジア
    -   米国中北部
    -   米国中南部
-   このリリースでは、HDInsight v1.6 (HDP1.1、Hadoop 1.0.3) および HDInsight v2.1 (HDP1.3、Hadoop 1.2) が Azure の管理ポータルから削除されています。これらのバージョンの Hadoop クラスターは、HDInsight PowerShell コマンドレット ([New-AzureHDInsightCluster][New-AzureHDInsightCluster]) または [HDInsight SDK][HDInsight SDK] を使用すれば引き続き作成できます。詳細については、「[HDInsight で提供されるクラスター バージョンの新機能][HDInsight で提供されるクラスター バージョンの新機能]」のページを参照してください。
-   このリリースでは、Hortonworks Data Platform (HDP) が以下のように変更されました。

| HDP               | 変更点                                                       |
|-------------------|--------------------------------------------------------------|
| HDP 1.3 / HDI 2.1 | 変更なし                                                     |
| HDP 2.0 / HDI 3.0 | 変更なし                                                     |
| HDP 2.1 / HDI 3.1 | zookeeper: ['3.4.5.2.1.3.0-1948'] -\> ['3.4.5.2.1.3.2-0002'] |

## 2014 年 6 月 24 日リリース

このリリースには、以下のように、HDInsight サービスの新しい拡張機能が数種類含まれています。

-   **HDP 2.1 が使用可能**: HDP 2.1 が含まれる HDInsight 3.1 が一般に使用可能となり、新しいクラスターの既定のバージョンになりました。
-   **HBase – Azure の管理ポータルの改良**: HBase クラスターがプレビューで使用可能になりました。ポータルで 3 回クリックするだけで HBase クラスターが作成できるようになりました。

![][]

HBase を使用すると、大規模なデータセットを処理する対話的な Web サイトから、何百万ものエンド ポイントからのセンサーのデータや利用統計情報のデータを格納するサービスまで、さまざまなリアルタイムのワークロードを HDInsight で構築できます。次の手順としては Hadoop ジョブでこれらのワークロードのデータを分析しますが、これは PowerShell や Hive クラスター ダッシュボードなどと同様の方法によって HDInsight で即座に実現可能です。

### Apache Mahout が HDInsight 3.1 にプレインストール

HDInsight 3.1 Hadoop クラスターには [Mahout][Mahout] がプレインストールされています。そのため、他のクラスター構成を追加することなく、Mahout ジョブを実行できます。たとえば、リモート デスクトップ プロトコル (RDP) を使用して Hadoop クラスターにリモート接続したり、以下のように、追加の手順なしで Hello world Mahout コマンドを実行したりできます。

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

この手順の詳しい説明については、Apache Mahout Web サイトのドキュメント「[Breiman Example (Breiman の例)][Breiman Example (Breiman の例)]」を参照してください。

### HDinsight 3.1 で Hive クエリに Tez を使用可能

HDInsight 3.1 で Hive 0.13 が利用可能になり、Tez を使用してクエリを実行できるようになりました。これを活用してパフォーマンスを向上できます。
既定では、Hive クエリでの Tez の使用は有効ではありません。これを使用するにはオプトインが必要です。以下のコード スニペットを実行すると Tez を有効にできます。

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks では、Tez を使用した Hive クエリのパフォーマンスの向上について、標準ベンチマークによる詳細なブレークダウンを公開しています。詳細については、「[Benchmarking Apache Hive 13 for Enterprise Hadoop (Enterprise Hadoop 向け Apache Hive 13 のベンチマーク)][Benchmarking Apache Hive 13 for Enterprise Hadoop (Enterprise Hadoop 向け Apache Hive 13 のベンチマーク)]」を参照してください。

Tez を使用した Hive に関する詳細については、[Hive on Tez wiki page (wiki ページ「Tez を使用した Hive」)][Hive on Tez wiki page (wiki ページ「Tez を使用した Hive」)] を参照してください。

### グローバル対応

Hadoop 2.2 対応の Azure HDInsight のリリースに伴い、すべての主要な Azure 地域で HDInsight が利用可能になりました。具体的には、西ヨーロッパと東南アジアのデータ センターがオンラインになりました。これで、お客様は、距離的に近く、コンプライアンス要件が似ていると思われる地域にあるデータ センター内のクラスターを見つけることができます。

### 重大な変更

**プレフィックスの構文**:
 HDInsight 3.0 および 3.1 クラスターでは、"wasb://" 構文のみがサポートされます。旧バージョンの "asv://" 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターまたは以降のバージョンではサポートされません。つまり、"asv://" 構文を明示的に使用するジョブを HDInsight 3.0 または 3.1 クラスターに送信すると、そのジョブは失敗します。代わりに、wasb:// 構文を使用する必要があります。また、既存のメタストアにより、asv:// 構文を使用してリソースへの明示的な参照を含むジョブを作成し、HDInsight 3.0 または 3.1 クラスターに送信すると、そのジョブは失敗します。これらのメタストアは、wasb:// を使用してリソースをアドレス指定するように再作成する必要があります。

**ポート**: HDInsight サービスで使用されるポートが変更されました。これまで使用されてきたポート番号は、Windows OS の一時的なポート範囲内の番号です。ポートは、短期間のインターネット プロトコル ベースの通信に対して一時的に事前定義される範囲から自動的に割り当てられます。今回 Hortonworks Data Platform (HDP) サービスに許可された新しいポート番号のセットは、この範囲の外部にあり、ヘッドノードで実行するサービスが使用するポート番号の競合の可能性が回避されます。新しいポート番号によって重大な変更が発生することはありません。今回使用される番号は以下のとおりです。

**HDInsight 1.6 (HDP 1.1)**

<table border="1">

<tr>
<th>
名前

</th>
<th>
値

</th>
</tr>

<tr>
<td>
dfs.http.address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.secondary.http.address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
mapred.job.tracker.http.address

</td>
<td>
jobtrackerhost:30030

</td>
</tr>

<tr>
<td>
mapred.task.tracker.http.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
mapreduce.history.server.http.address

</td>
<td>
0.0.0.0:31111

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
**HDInsight 3.0 および 3.1 (HDP 2.0 と 2.1)**

<table border="1">

<tr>
<th>
名前

</th>
<th>
値

</th>
</tr>

<tr>
<td>
dfs.namenode.http-address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.namenode.https-address

</td>
<td>
headnodehost:30470

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.namenode.secondary.http-address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
yarn.nodemanager.webapp.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
### 依存関係

HDInsight 3.x (HDP2.x) に以下の依存関係が追加されました。

-   guice-servlet
-   optiq-core
-   javax.inject
-   activation
-   jsr305
-   geronimo-jaspic\_1.0\_spec
-   jul-to-slf4j
-   java-xmlbuilder
-   ant
-   commons-compiler
-   jdo-api
-   commons-math3
-   paranamer
-   jaxb-impl
-   stringtemplate
-   eigenbase-xom
-   jersey-servlet
-   commons-exec
-   jaxb-api
-   jetty-all-server
-   janino
-   xercesImpl
-   optiq-avatica
-   jta
-   eigenbase-properties
-   groovy-all
-   hamcrest-core
-   mail
-   linq4j
-   jpam
-   jersey-client
-   aopalliance
-   geronimo-annotation\_1.0\_spec
-   ant-launcher
-   jersey-guice
-   xml-apis
-   stax-api
-   asm-commons
-   asm-tree
-   wadl
-   geronimo-jta\_1.1\_spec
-   guice
-   leveldbjni-all
-   velocity
-   jettison
-   snappy-java
-   jetty-all
-   commons-dbcp

HDInsight 3.x (HDP2.x) から削除された依存関係は以下のとおりです。

-   jdeb
-   kfs
-   sqlline
-   ivy
-   aspectjrt
-   json
-   core
-   jdo2-api
-   avro-mapred
-   datanucleus-enhancer
-   jsp
-   commons-logging-api
-   commons-math
-   JavaEWAH
-   aspectjtools
-   javolution
-   hdfsproxy
-   hbase
-   snappy

### バージョンの変更

HDInsight 2.x (HDP1.x) と HDInsight 3.x (HDP2.x) との間で以下のバージョンが変更されました。

-   metrics-core: ['2.1.2'] -\> ['3.0.0']
-   derbynet: ['10.4.2.0'] -\> ['10.10.1.1']
-   datanucleus: ['rdbms-3.0.8'] -\> ['rdbms-3.2.9']
-   jasper-compiler: ['5.5.12'] -\> ['5.5.23']
-   log4j: ['1.2.15', '1.2.16'] -\> ['1.2.16', '1.2.17']
-   derbyclient: ['10.4.2.0'] -\> ['10.10.1.1']
-   httpcore: ['4.2.4'] -\> ['4.2.5']
-   hsqldb: ['1.8.0.10'] -\> ['2.0.0']
-   jets3t: ['0.6.1'] -\> ['0.9.0']
-   protobuf-java: ['2.4.1'] -\> ['2.5.0']
-   derby: ['10.4.2.0'] -\> ['10.10.1.1']
-   jasper: ['runtime-5.5.12'] -\> ['runtime-5.5.23']
-   commons-daemon: ['1.0.1'] -\> ['1.0.13']
-   datanucleus-core: ['3.0.9'] -\> ['3.2.10']
-   datanucleus-api-jdo: ['3.0.7'] -\> ['3.2.6']
-   zookeeper: ['3.4.5.1.3.9.0-01320'] -\> ['3.4.5.2.1.3.0-1948']
-   bonecp: ['0.7.1.RELEASE'] -\> ['0.8.0.RELEASE']

### ドライバー

SQL Server JDBC ドライバーは HDInsight によって内部的に使用され、外部の操作では使用されません。ODBC を使用して HDInsight に接続する場合は、Microsoft Hive ODBC ドライバーを使用してください。Hive ODBC の使い方の詳細については、「[Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][connect-excel-with-hive-ODBC]」を参照してください。

### バグの修正

このリリースでは、以下のバージョンの HDInsight (Hortonworks Data Platform - HDP) でバグが修正されました。

-   HDInsight 2.1 (HDP 1.3)
-   HDInsight 3.0 (HDP 2.0)
-   HDInsight 3.1 (HDP 2.1)

## Hortonworks リリース ノート

各バージョンの HDInsight クラスターで使用される HDP のリリース ノートは、以下の場所から利用可能です。

-   HDInsight クラスター Version 3.1 は、[Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1] を基盤とする Hadoop ディストリビューションを使用します (これは、Azure HDInsight ポータルを使用する場合に作成される既定の Hadoop クラスターです)。

-   HDInsight クラスター Version 3.0 は、[Hortonworks Data Platform 2.0][Hortonworks Data Platform 2.0] を基盤とする Hadoop ディストリビューションを使用します。

-   HDInsight クラスター Version 2.1 は、[Hortonworks Data Platform 1.3][Hortonworks Data Platform 1.3] を基盤とする Hadoop ディストリビューションを使用します。

-   HDInsight クラスター Version 1.6 は、[Hortonworks Data Platform 1.1][Hortonworks Data Platform 1.1] を基盤とする Hadoop ディストリビューションを使用します。

  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/en-us/library/dn593744.aspx
  [HDInsight SDK]: http://msdn.microsoft.com/en-us/library/azure/dn469975.aspx
  [HDInsight で提供されるクラスター バージョンの新機能]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-component-versioning/
  []: http://i.imgur.com/cmOl5fM.png
  [Mahout]: http://hortonworks.com/hadoop/mahout/
  [Breiman Example (Breiman の例)]: https://mahout.apache.org/users/classification/breiman-example.html
  [Benchmarking Apache Hive 13 for Enterprise Hadoop (Enterprise Hadoop 向け Apache Hive 13 のベンチマーク)]: http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/
  [Hive on Tez wiki page (wiki ページ「Tez を使用した Hive」)]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
