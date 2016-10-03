<properties
	pageTitle="HDInsight クラスターで使用できるコンポーネントの種類 | Microsoft Azure"
	description="HDInsight は、デプロイできる Hadoop クラスター コンポーネントとバージョンを複数サポートしています。サポートされている Hadoop および Hortonworks Data Platform (HDP) ディストリビューションのバージョンを確認します。"
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="jgao"/>


# HDInsight で使用できる Hadoop コンポーネントの種類を教えてください。

HDInsight が提供する各種サービス レベルと、HDInsight に含まれる各 hadoop コンポーネントのバージョンをご確認ください。

## HDInsight Standard と HDInsight Premium

Azure HDInsight は、ビッグ データのクラウド サービスを **Standard** と **Premium** の 2 つのカテゴリで提供します。以下の表に、Premium で**のみ**利用できる機能を示します。この表に明示的に書かれていない機能は、Standard の機能として提供されています。

>[AZURE.NOTE] HDInsight Premium は現在プレビューの段階で、Linux クラスターでのみ利用できます。

| HDInsight Premium の機能 | Description |
|--------------|---------------|
| Microsoft R Server (プレビュー) | Microsoft R Server は、スケーラブルな R 向けの最も広く利用されている企業向け分析プラットフォームです。R 言語は、さまざまなビッグ データ統計、予測モデリング、および機械学習機能をサポートしています。HDInsight Premium では、大規模なデータセットとモデルを使用できる R Server を使用して HDInsight クラスターを作成できるようになりました。この新機能により、データ サイエンティストや統計学の専門家は、クラスターのセットアップやメンテナンスに労力をかけることなく、HDInsight を通じてオンデマンドで拡張できる、使いやすい R インターフェイスを利用できます。<br> <br>詳細については、[HDInsight での R Server の使用に関するページ](hdinsight-hadoop-r-server-get-started.md)をご覧ください。

### Premium でサポートされるクラスターの種類

次の表に、HDInsight クラスターの種類と Premium のサポート状況を示します。

| クラスターの種類 | Standard | プレミアム |
|--------------|---------------|--------------|
| Hadoop は、 | あり | あり |
| Spark | あり | あり |
| HBase | あり | いいえ |
| Storm | あり | なし |

HDInsight Premium でサポートされるクラスターの種類が追加されたら、それに応じてこの表も更新されます。

### 料金と SLA

HDInsight Premium の料金と SLA の詳細については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

## HDInsight の各バージョンで使用できる Hadoop コンポーネント

Azure HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントが作成されます。HDInsight クラスター バージョンに対応するコンポーネントのバージョンを、次の表にまとめます。Azure HDInsight で使用される既定のクラスター バージョンは、2016 年 9 月 14 日現在、HDP 2.4 を基盤とした 3.4 です。

> [AZURE.NOTE] サービスの既定のバージョンは、予告なく変更される場合があります。バージョンに依存する .NET SDK/Azure PowerShell や Azure CLI を使用する場合は、クラスターの作成時にバージョンを指定することをお勧めします。

コンポーネント|HDInsight Version 3.4 (既定) | HDInsight Version 3.3 | HDInsight Version 3.2 |HDInsight Version 3.1 |HDInsight Version 3.0|
---|---|---|---|---|---
Hortonworks Data Platform|2\.4|2\.3|2\.2|2\.1.7|2\.0|
Apache Hadoop & YARN|2\.7.1|2\.7.1|2\.6.0|2\.4.0|2\.2.0|
Apache Tez|0\.7.0|0\.7.0 | 0\.5.2|0\.4.0||
Apache Pig|0\.15.0|0\.15.0|0\.14.0|0\.12.1|0\.12.0|
Apache Hive & HCatalog|1\.2.1|1\.2.1|0\.14.0|0\.13.1|0\.12.0|
Apache HBase |1\.1.2|1\.1.1|0\.98.4|0\.98.0||
Apache Sqoop|1\.4.6|1\.4.6|1\.4.5|1\.4.4|1\.4.4|1\.4.3
Apache Oozie|4\.2.0|4\.2.0|4\.1.0|4\.0.0|4\.0.0|
Apache Zookeeper|3\.4.6|3\.4.6|3\.4.6|3\.4.5|3\.4.5|
Apache Storm|0\.10.0|0\.10.0|0\.9.3|0\.9.1||
Apache Mahout|0\.9.0+|0\.9.0+|0\.9.0|0\.9.0||
Apache Phoenix|4\.4.0|4\.4.0|4\.2.0|4\.0.0.2.1.7.0-2162||
Apache Spark|1\.6.0 (Linux のみ)|1\.5.2 (Linux のみ/試験的ビルド)|1\.3.1 (Windows のみ)|||


**現在のコンポーネントのバージョンの取得**

HDInsight クラスターのバージョンに関連付けられたコンポーネントのバージョンは、将来 HDInsight が更新されたときに変更される可能性があります。利用可能なコンポーネントを特定し、どのバージョンがクラスターに使用されているかどうかを確認する 1 つの方法は、Ambari REST API を使用することです。**GetComponentInformation** コマンドを使用すると、サービス コンポーネントに関する情報を取得できます。詳細については、「[Ambari のドキュメント][ambari-docs]」を参照してください。この情報を取得するもう 1 つの方法は、リモート デスクトップを使用してクラスターにログインし、"C:\\apps\\dist\" ディレクトリの内容を直接確認することです。


**リリース ノート**

HDInsight の最新バージョンに関する追加のリリース ノートは、[HDInsight リリース ノート](hdinsight-release-notes.md)を参照してください。


## サポートされる HDInsight のバージョン
次の表に、現在使用できる HDInsight のバージョン、該当する Hortonworks Data Platform のバージョン、そのリリース日を示します。非推奨となった場合は、サポート有効期限とその日付も記載されます。以下の点に注意してください。

* ヘッド ノードを 2 つ備えた可用性の高いクラスターは、HDInsight 2.1 以降では既定でデプロイされています。HDInsight 1.6 クラスターでは利用できません。
* 特定のバージョンのサポート期限が切れると、Azure ポータルから利用できなくなる可能性があります。次の表に、Azure クラシック ポータルで利用できるバージョンを示します。クラスター バージョンは、非推奨となるまでは、Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) コマンドの `Version` パラメーターと .NET SDK で利用できます。

HDInsight のバージョン|HDP のバージョン|VM の OS|高可用性|リリース日|Azure ポータルでの利用|サポート有効期限|非推奨となる日
---|---|---|---|---|---|---|---
HDI 3.4|HDP 2.4|Ubuntu 14.0.4 LTS|はい|2016 年 3 月 29 日|はい||
HDI 3.3|HDP 2.3|Ubuntu 14.0.4 LTS または Windows Server 2012R2|はい|2015 年 12 月 2 日|はい|2016 年 6 月 27 日|2017 年 7 月 31 日
HDI 3.2|HDP 2.2|Ubuntu 12.04 LTS または Windows Server 2012R2|はい|2015 年 2 月 18 日|はい|2016 年 3 月 1 日|2017 年 4 月 1 日
HDI 3.1|HDP 2.1|Windows Server 2012R2|はい|2014 年 6 月 24 日|なし|2015 年 5 月 18 日|2016 年 6月 30 日
HDI 3.0|HDP 2.0|Windows Server 2012R2|はい|02/11/2014|なし|09/17/2014|06/30/2015
HDI 2.1|HDP 1.3|Windows Server 2012R2|はい|10/28/2013|なし|05/12/2014|05/31/2015
HDI 1.6|HDP 1.1||なし|10/28/2013|なし|04/26/2014|05/31/2015

**既定以外のクラスターのデプロイメント**

### HDInsight クラスター バージョンのサービス レベル アグリーメント

SLA は、"サポート ウィンドウ" の条件で定義されます。サポート ウィンドウとは、HDInsight クラスターのバージョンが Microsoft カスタマー サービス & サポートによってサポートされる期間を指しています。HDInsight クラスターは、そのバージョンの**サポート有効期限**が現在の日付を過ぎている場合、サポート対象外となります。サポートされている HDInsight クラスターのバージョンの一覧は上記のテーブルに示されています。所定の HDInsight Version X (新しい X+1 バージョンが利用可能なった後) のサポート有効期限は、次の数式で計算した日付のうち、遅い方とされます。

- 数式 1: HDInsight クラスター バージョン X がリリースされた日に 180 日を加える。
- 数式 2: HDInsight クラスター バージョン X+1 (X の次のバージョン) がポータルで使用可能になった日付に 90 日を加える。

**非推奨となる日**は、それ以降 HDInsight でそのクラスター バージョンが作成できなくなる日付です。

> [AZURE.NOTE] Windows ベースの HDInsight クラスター (バージョン 2.1、3.0、3.1、3.2、および 3.3 を含む) は Azure ゲスト OS ファミリ 4 で実行されます。この OS は Windows Server 2012 R2 の 64 ビット版を使用し、.NET Framework 4.0、4.5、4.5.1、および 4.5.2 をサポートします。

## HDInsight バージョンに対応する Hortonworks リリース ノート##

* HDInsight クラスター Version 3.4 は、[Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) を基盤とする Hadoop ディストリビューションを使用します。これが、ポータルの使用時に作成される**既定**の Hadoop クラスターです。



* HDInsight クラスター Version 3.3 は、[Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) を基盤とする Hadoop ディストリビューションを使用します。
	* Apache Storm リリース ノートは、[こちら](https://storm.apache.org/2015/11/05/storm0100-released.html)で入手できます。
	* Apache Hive リリース ノートは、[こちら](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843)で入手できます。

* HDInsight クラスター Version 3.2 は、[Hortonworks Data Platform 2.2][hdp-2-2] を基盤とする Hadoop ディストリビューションを使用します。

	* 各 Apache コンポーネントのリリース ノート - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)、[Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)。


* HDInsight クラスター Version 3.1 は、[Hortonworks Data Platform 2.1.7][hdp-2-1-7] を基盤とする Hadoop ディストリビューションを使用します。2014 年 11 月 7 日より前に作成された HDInsight 3.1 クラスターは、[Hortonworks Data Platform 2.1.1][hdp-2-1-1] に基づいています。

* HDInsight クラスター Version 3.0 は、[Hortonworks Data Platform 2.0][hdp-2-0-8] を基盤とする Hadoop ディストリビューションを使用します。

* HDInsight クラスター Version 2.1 は、[Hortonworks Data Platform 1.3][hdp-1-3-0] を基盤とする Hadoop ディストリビューションを使用します。

* HDInsight クラスター Version 1.6 は、[Hortonworks Data Platform 1.1][hdp-1-1-0] を基盤とする Hadoop ディストリビューションを使用します。


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0921_2016-->