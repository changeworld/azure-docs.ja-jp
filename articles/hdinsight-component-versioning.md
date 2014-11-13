<properties urlDisplayName="HDInsight Hadoop Version" pageTitle="HDInsight で提供される Hadoop クラスター バージョンの新機能| Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。サポートされている Hadoop および Hortonworks Data Platform (HDP) ディストリビューションのバージョンを確認します。" services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="HDInsight で提供されるクラスター バージョンの新機能" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# HDInsight で提供される Hadoop クラスター バージョンの新機能

## HDInsight のバージョン

HDInsight は、いつでも展開できる Hadoop クラスター バージョンを複数サポートしています。各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューションと、そのディストリビューションに含まれるコンポーネントがプロビジョニングされます。各 HDInsight クラスター バージョンに対応するコンポーネントのバージョンを、次の表にまとめます。[Azure HDInsight][Azure HDInsight] で使用される既定のクラスター バージョンは、現在、HDP 2.1 を基盤とした 3.1 です。

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">コンポーネント</th>
<th align="left">HDInsight Version 3.1 (既定)</th>
<th align="left">HDInsight Version 3.0</th>
<th align="left">HDIinsight Version 2.1</th>
<th align="left">HDInsight Version 1.6</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Hortonworks Data Platform (HDP)</td>
<td align="left">2.1</td>
<td align="left">2.0</td>
<td align="left">1.3</td>
<td align="left">1.1</td>
</tr>
<tr class="even">
<td align="left">Apache Hadoop &amp; YARN</td>
<td align="left">2.4.0</td>
<td align="left">2.2.0</td>
<td align="left">1.2.0</td>
<td align="left">1.0.3</td>
</tr>
<tr class="odd">
<td align="left">Tez</td>
<td align="left">0.4.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Apache Pig</td>
<td align="left">0.12.1</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.3</td>
</tr>
<tr class="odd">
<td align="left">Apache Hive &amp; HCatalog</td>
<td align="left">0.13.0</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.0</td>
</tr>
<tr class="even">
<td align="left">HBase</td>
<td align="left">0.98.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Apache Sqoop</td>
<td align="left">1.4.4</td>
<td align="left">1.4.4</td>
<td align="left">1.4.3</td>
<td align="left">1.4.2</td>
</tr>
<tr class="even">
<td align="left">Apache Oozie</td>
<td align="left">4.0.0</td>
<td align="left">4.0.0</td>
<td align="left">3.3.2</td>
<td align="left">3.2.0</td>
</tr>
<tr class="odd">
<td align="left">Apache HCatalog</td>
<td align="left">Hive にマージ</td>
<td align="left">Hive にマージ</td>
<td align="left">Hive にマージ</td>
<td align="left">0.4.1</td>
</tr>
<tr class="even">
<td align="left">Apache Templeton</td>
<td align="left">Hive にマージ</td>
<td align="left">Hive にマージ</td>
<td align="left">Hive にマージ</td>
<td align="left">0.1.4</td>
</tr>
<tr class="odd">
<td align="left">Ambari</td>
<td align="left">1.5.1</td>
<td align="left">1.4.1</td>
<td align="left">API v1.0</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Zookeeper</td>
<td align="left">3.4.5</td>
<td align="left">3.4.5</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Mahout</td>
<td align="left">0.9.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

**現在のコンポーネント バージョン情報を取得する**

HDInsight クラスターのバージョンに関連付けられたコンポーネントのバージョンは、将来 HDInsight が更新されたときに変更される可能性があります。利用可能なコンポーネントを特定し、どのバージョンがクラスターに使用されているかどうかを確認する 1 つの方法は、Ambari REST API を使用することです。**GetComponentInformation** コマンドを使用すると、サービス コンポーネントに関する情報を取得できます。詳細については、「[Ambari のドキュメント][Ambari のドキュメント]」を参照してください。この情報を取得するもう 1 つの方法は、リモート デスクトップを使用してクラスターにログインし、"C:\\apps\\dist\\" ディレクトリの内容を直接確認することです。

**リリース ノート**

HDInsight の最新バージョンに関する追加のリリース ノートは、「[HDInsight Release Notes (HDInsight のリリース ノート)][HDInsight Release Notes (HDInsight のリリース ノート)]」を参照してください。

### HDInsight クラスターのプロビジョニング時にバージョンを選択

HDInsight PowerShell コマンドレットまたは HDInsight .NET SDK を使用してクラスターを作成するときは、"Version" パラメーターを使用して、HDInsight Hadoop クラスターのバージョンを選択することができます。

**簡易作成**オプションを使用する場合は、Hadoop クラスターを作成する HDInsight Version 3.1 が既定で使用されます。Windows Azure ポータルで **[カスタム作成]** オプションを使用する場合は、**[クラスターの詳細]** ページの **[HDInsight のバージョン]** ボックスで、展開されるクラスターのバージョンを選択できます。

## 機能概要

現在、Azure HDInsight は、HDInsight クラスター Version 3.1 (Hortonworks Data Platform 2.1 を使用) で Hadoop 2.4 を既定でサポートしています。プラットフォームを最大限に活用して、広範囲にわたる大きな利点をお客様に提供します。これには、特に以下の利点があります。

-   **HBase**: HBase は、ビッグ データのオンライン トランザクション処理を可能にする遅延時間の短い NoSQL データベースです。HBase は、管理されるクラスターとして、Azure 環境に統合されて提供されます。クラスターは、Azure BLOB ストレージにデータを直接格納するよう構成されるため、遅延時間が短くなり、パフォーマンスとコストを考慮した選択肢に大きな弾力性が生まれます。そのため、顧客は大規模なデータセットを処理する対話的な Web サイトを構築できます。これにより、何百万ものエンドポイントからセンサーのデータおよび利用統計情報のデータを格納するサービスを構築したり、Hadoop ジョブによりこれらのデータを分析したりすることができます。

-   **クラスター ダッシュボード**: HDInsight クラスターにデプロイする新しい Web アプリケーション。Hive クエリの実行、ジョブ ログの確認、Azure BLOB ストレージの表示に使用します。この Web アプリケーションにアクセスするための URL は \<*クラスター名*\>.azurehdinsight.net です。

-   **Microsoft Avro ライブラリ**: このライブラリは、Microsoft.NET 環境向けの Apache Avro データ シリアル化システムを実装します。Apache Avro は、シリアル化のためのコンパクトなバイナリ データ交換形式を提供します。Apache Avro は、JSON を使用して、言語の相互運用性を保証する、言語に依存しないスキーマを定義します。ある言語でシリアル化されたデータは、別の言語で読むことができます。現在、C、C++、C#、Java、PHP、Python、および Ruby がサポートされています。Apache Avro シリアル化形式は、Hadoop MapReduce ジョブ内の複雑なデータ構造を表すために Azure HDInsight で広く使用されています。

-   **YARN**: Hadoop クラスターでのデータ処理に使用される従来の Apache Hadoop MapReduce フレームワークに代わる、新しい汎用的な分散アプリケーション管理フレームワーク。Hadoop オペレーティング システムとして効果的に機能し、単一の目的のバッチ処理用データ プラットフォームであった Hadoop を、バッチ、インタラクティブ、オンライン、ストリームの各処理が可能な多目的プラットフォームにします。この新しい管理フレームワークにより、容量の保証、公平性、サービス レベル アグリーメントなどの基準に従って、スケーラビリティおよびクラスター使用率が向上します。

-   **Tez (HDInsight 3.1 のみ)**: Hadoop で小規模なワークロードと大規模なワークロードの両方にまたがる簡単なデータ処理タスクを作成する、カスタマイズ可能な汎用フレームワーク。1 つのジョブでタスクの複雑な無閉路有効グラフ (DAG) を実行できるため、Apache Hive や Apache Pig などの Apache Hadoop エコシステムのプロジェクトで、人間が介在する応答時間やペタバイト規模の高速スループットに関する要件を満たすことができます。Hive 0.13 では、MapReduce ではなく Tez 上で Hive クエリを実行できます。

-   **高可用性**: サービスの可用性を高めるために、HDInsight によってデプロイされた Hadoop クラスターに 2 つ目のヘッドノードが追加されました。通常 Hadoop クラスターの標準的な実装には、ヘッドノードは 1 つだけあります。HDInsight はセカンダリ ヘッドノードを追加することで、この単一障害点をなくします。顧客が既定の L サイズ ノードを使わず、XL サイズのヘッド ノードでクラスターをプロビジョニングしない限り、新しい HA クラスター構成に切り替えてもクラスターの価格は変わりません。

-   **Hive のパフォーマンス**: Hive クエリの応答時間 (最大 40 x) および **Optimized Row Columnar** (ORC) 形式によるデータ圧縮 (最大 80%) への重要な機能強化の序列。

-   **Pig、Sqoop、Oozie、Ambari**: HDInsight クラスター Version 2.1 (HDP 1.3/Hadoop 1.2) でのパリティを提供する、HDInsight クラスター Version 3.0 (HDP 2.0/Hadoop 2.2) のコンポーネント バージョンのアップグレード。詳細については、以下のバージョンの各表を参照してください。

-   **Mahout**: このスケーラブルな機械学習アルゴリズムのライブラリは、HDInsight 3.1 Hadoop クラスターにプレインストールされています。そのため、他のクラスター構成を追加することなく、Mahout ジョブを実行できます。

-   **Virtual Network のサポート**: HDInsight クラスターと Azure Virtual Network を組み合わせることにより、クラウド リソースの分離、つまり、クラウドのリソースとご利用のデータセンター内のリソースとをリンクさせたハイブリッド ソリューションを実現できます。

## サポートされているバージョン

次の表に、現在使用できる HDInsight のバージョン、該当する Hortonworks Data Platform (HDP) のバージョン、そのリリース日を示します。非推奨となった場合は、サポート有効期限とその日付も記載されます。以下の点に注意してください。

-   ヘッド ノードを 2 つ備えた可用性の高いクラスターは、HDInsight 2.1、3.0、および 3.1 クラスターでは既定でデプロイされています。HDInsight 1.6 クラスターでは利用できません。
-   特定のバージョンのサポート期限が切れると、Azure の管理ポータルから利用できなくなる可能性があります。次の表に、Azure の管理ポータルで利用できるバージョンを示します。クラスター バージョンは、非推奨となるまでは、PowerShell [New-AzureHDInsightCluster][New-AzureHDInsightCluster] コマンドの `Version` パラメーターと .NET SDK で利用できます。

<table border="1">
<tr>
<th>
HDInsight のバージョン

</th>
<th>
HDP のバージョン</a>

<th>
高可用性

</th>
</th>
<th>
リリース日

</th>
<th>
Azure の管理ポータルで利用可能

</th>
<th>
サポート有効期限

</th>
<th>
非推奨となる日

</th>
</tr>
<tr>
<td>
HDI 3.1

</td>
<td>
HDP 2.1

</td>
<td>
あり

</td>
<td>
</td>
<td>
あり

</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td>
HDI 3.0

</td>
<td>
HDP 2.0

</td>
<td>
あり

</td>
<td>
02/11/2014

</td>
<td>
あり

</td>
<td>
09/17/2014

</td>
<td>
06/30/2015

</td>
</tr>
<tr>
<td>
HDI 2.1

</td>
<td>
HDP 1.3

</td>
<td>
あり

</td>
<td>
10/28/2013

</td>
<td>
いいえ

</td>
<td>
05/12/2014

</td>
<td>
05/31/2015

</td>
</tr>
<tr>
<td>
HDI 1.6

</td>
<td>
HDP 1.1

</td>
<td>
いいえ

</td>
<td>
10/28/2013

</td>
<td>
いいえ

</td>
<td>
04/26/2014

</td>
<td>
05/31/2015

</td>
</tr>
</table>
**既定以外のクラスターのデプロイメント**

Hadoop 2.4 での HDInsight 3.1 クラスターの作成は、Azure ポータル、HDInsight SDK、および Azure PowerShell でサポートされています。Hadoop 2.4 では、既定では HDInsight 3.1 クラスターが作成されるため、他のバージョンの HDInsight クラスターを作成する場合は、ポータルで **[カスタム作成]** オプションを使用してバージョンを指定する必要があります。

### HDInsight クラスター バージョンのサービス レベル アグリーメント (SLA)

SLA は、"サポート ウィンドウ" の条件で定義されます。サポート ウィンドウとは、HDInsight クラスターのバージョンが Microsoft カスタマー サポートによってサポートされる期間を指しています。HDInsight クラスターは、そのバージョンの**サポート有効期限**が現在の日付を過ぎている場合、サポート対象外となります。サポートされている HDInsight クラスターのバージョンの一覧は上記のテーブルに示されています。所定の HDInsight Version X (新しい X+1 バージョンが利用可能なった後) のサポート有効期限は、次の数式で計算した日付のうち、遅い方とされます。

-   数式 1: HDInsight クラスター Version X がリリースされた日に 180 日を加える。
-   数式 2: 数式 2: HDInsight クラスター Version X+1 (X の次のバージョン) が Azure の管理ポータルで利用可能になった日付に 90 日を加える。

**非推奨となる日**は、それ以降 HDInsight でそのクラスター バージョンが作成できなくなる日付です。

> [WACOM.NOTE] HDInsight 2.1 および 3.0 のクラスターは Azure ゲスト OS [ファミリ 4][ファミリ 4] で実行されます。この OS は Windows Server 2012 R2 の 64 ビット版を使用し、.NET Framework 4.0、4.5. および 4.5.1 をサポートします。

## HDInsight バージョンに対応する Hortonworks リリース ノート

-   HDInsight クラスター Version 3.1 は、[Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1] を基盤とする Hadoop ディストリビューションを使用します。これは、Azure HDInsight ポータルを使用する場合に作成される既定の Hadoop クラスターです。

-   HDInsight クラスター Version 3.0 は、[Hortonworks Data Platform 2.0][Hortonworks Data Platform 2.0] を基盤とする Hadoop ディストリビューションを使用します。

-   HDInsight クラスター Version 2.1 は、[Hortonworks Data Platform 1.3][Hortonworks Data Platform 1.3] を基盤とする Hadoop ディストリビューションを使用します。

-   HDInsight クラスター Version 1.6 は、[Hortonworks Data Platform 1.1][Hortonworks Data Platform 1.1] を基盤とする Hadoop ディストリビューションを使用します。

  [Azure HDInsight]: http://go.microsoft.com/fwlink/?LinkID=285601
  [Ambari のドキュメント]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [HDInsight Release Notes (HDInsight のリリース ノート)]: http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-release-notes/
  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/ja-jp/library/dn593744.aspx
  [ファミリ 4]: http://msdn.microsoft.com/ja-jp/library/azure/ee924680.aspx#explanation
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
