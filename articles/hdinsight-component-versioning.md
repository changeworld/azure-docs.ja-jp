<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight　Hadoop バージョン" pageTitle="HDInsight で提供されるクラスター バージョンの新機能| Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight は、いつでも展開可能な複数の Hadoop クラスター バージョンをサポートしています。サポートされている Hadoop および HortonWorks Data Platform (HDP) のディストリビューションのバージョンを確認してください。" services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="HDInsight で提供されるクラスター バージョンの新機能" authors="bradsev" />


#HDInsight で提供されるクラスター バージョンの新機能

Azure HDInsight は、HDinsight クラスター バージョン 3.0 での Hadoop 2.2 をサポートしており、このプラットフォームを最大限に活用して、広範囲にわたる大きな利点をお客様に提供します。これには、特に以下の利点があります。

- Hive: Hive クエリの応答時間 (最大 40 x) および、Optimized Row Columnar (ORC) 形式によるデータ圧縮 (最大 80%) への重要な機能強化の序列。

- YARN: Hadoop クラスターでのデータ処理に使用される従来の Apache Hadoop MapReduce フレームワークに代わる、新しい汎用的な分散アプリケーション管理フレームワーク。Hadoop オペレーティング システムとして効果的に機能し、単一の目的のバッチ処理用データ プラットフォームであった Hadoop を、バッチ、インタラクティブ、オンライン、ストリームの各処理が可能な多目的プラットフォームにします。この新しい管理フレームワークにより、容量の保証、公平性、サービス レベル アグリーメントなどの基準に従って、スケーラビリティおよびクラスター使用率が向上します。

- Pig、Sqoop、Qozie、Ambari: HDinsight クラスター バージョン 3.0 (HDP 2.0) のコンポーネント バージョン アップグレードで、HDinsight クラスター バージョン 2.1 (HDP 1.3) でのパリティを提供します。詳細については、以下のバージョンの各表を参照してください。Hbase、Mahout、Flume は含まれていないことに注意してください。

**デプロイ**	
Hadoop 2.2 での HDInsight 3.0 クラスターの作成は、Azure ポータル、HDinsight SDK、および Azure PowerShell でサポートされています。

**グローバル対応**		
Hadoop 2.2 上の Azure HDInsight のリリースにより、HDInsight は、中国を除くすべての主要な Azure 地域で使用できるようになりました。具体的には、西ヨーロッパと東南アジアのデータ センターがオンラインになりました。これで、お客様は、距離的に近く、コンプライアンス要件が似ていると思われる地域にあるデータ センター内のクラスターを見つけることができます。

**重大な変更**	
HDInsight 3.0 クラスターでは、"wasb://" 構文のみがサポートされます。旧バージョンの "asv://" 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターではサポートされず、以降のバージョンでもサポートされません。つまり、"asv://" 構文を明示的に使用するジョブを HDInsight 3.0 クラスターに送信すると、そのジョブは失敗します。代わりに、wasb:// 構文を使用する必要があります。また、既存のメタストアにより、asv:// 構文を使用してリソースへの明示的な参照を含むジョブを作成し、HDInsight 3.0 クラスターに送信すると、そのジョブは失敗します。これらのメタストアは、wasb:// を使用してリソースをアドレス指定するように再作成する必要があります。

##HDInsight のバージョン
HDInsight は、いつでも展開できる Hadoop クラスター バージョンを複数サポートしています。各バージョンを選択すると、特定のバージョンの Hortonworks Data Platform (HDP) ディストリビューション、および、そのディストリビューションに含まれるコンポーネントがプロビジョニングされます。

###クラスター バージョン 3.0

Azure HDInsight で Hadoop 2.2 がサポートされるようになりました。Hortonworks Data Platform Version 2.0 に基づいており、次の表に示すコンポーネント バージョンの Hadoop サービスが提供されます。

<table border="1">
<tr><th>コンポーネント</th><th>バージョン</th></tr>
<tr><td>Apache Hadoop</td><td>2.2.0</td></tr>
<tr><td>Apache Hive</td><td>0.12.0</td></tr>
<tr><td>Apache Pig</td><td>0.12</td></tr>
<tr><td>Apache Sqoop</td><td>1.4.4</td></tr>
<tr><td>Apache Oozie</td><td>4.0.0</td></tr>
<tr><td>Apache HCatalog</td><td>Hive にマージ</td></tr>
<tr><td>Apache Templeton</td><td>Hive にマージ</td></tr>
<tr><td>Ambari</td><td>API v1.0</td></tr>
</table>

###クラスター バージョン 2.1

[Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) で使用される既定のクラスター バージョンは 2.1 です。Hortonworks Data Platform Version 1.3.0 に基づいており、次の表に示すコンポーネント バージョンの Hadoop サービスが提供されます。

<table border="1">
<tr><th>コンポーネント</th><th>バージョン</th></tr>
<tr><td>Apache Hadoop</td><td>1.2.0</td></tr>
<tr><td>Apache Hive</td><td>0.11.0</td></tr>
<tr><td>Apache Pig</td><td>0.11</td></tr>
<tr><td>Apache Sqoop</td><td>1.4.3</td></tr>
<tr><td>Apache Oozie</td><td>3.2.2</td></tr>
<tr><td>Apache HCatalog</td><td>Hive にマージ</td></tr>
<tr><td>Apache Templeton</td><td>Hive にマージ</td></tr>
<tr><td>Ambari</td><td>API v1.0</td></tr>
</table>


###クラスター バージョン 1.6

[Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) クラスター バージョン 1.6 も利用できます。Hortonworks Data Platform Version 1.1.0 に基づいており、次の表に示すコンポーネント バージョンの Hadoop サービスが提供されます。

<table border="1">
<tr><th>コンポーネント</th><th>バージョン</th></tr>
<tr><td>Apache Hadoop</td><td>1.0.3</td></tr>
<tr><td>Apache Hive</td><td>0.9.0</td></tr>
<tr><td>Apache Pig</td><td>0.9.3</td></tr>
<tr><td>Apache Sqoop</td><td>1.4.2</td></tr>
<tr><td>Apache Oozie</td><td>3.2.0</td></tr>
<tr><td>Apache HCatalog</td><td>0.4.1</td></tr>
<tr><td>Apache Templeton</td><td>0.1.4</td></tr>
<tr><td>SQL Server JDBC ドライバー</td><td>3.0</td></tr>
</table>


###HDInsight クラスターのプロビジョニング時にバージョンを選択

HDInsight PowerShell コマンドレットまたは HDInsight .NET SDK を使用してクラスターを作成するときは、"Version" パラメーターを使用して、HDInsight Hadoop クラスターのバージョンを選択することができます。

**簡易作成** オプションを使用する場合は、HDInsight Hadoop クラスターのバージョン 2.1 が既定で使用されます。Azure ポータルで **[カスタム作成]** オプションを使用する場合は、**[クラスターの詳細]** ページの **[HDInsight のバージョン]** ボックスで、展開されるクラスターのバージョンを選択できます。HDInsight Hadoop クラスターのバージョン 3.0 は、**カスタム作成**ウィザードでのオプションとしてのみ使用可能です。

![HDI.Versioning.VersionScreen][image-hdi-versioning-versionscreen]


##サポートされているバージョン
次の表に、現在使用できる HDInsight のバージョン、該当する Hortonworks Data Platform (HDP) のバージョン、そのリリース日を示します。非推奨となった場合は、その日付も記載されます。

<table border="1">
<tr><th>HDInsight のバージョン</th><th>HDP のバージョン</a></th><th>リリース日</th></tr>
<tr><td>HDI 3.0</td><td>HDP 2.0</td><td>02/11/2014</td></tr>
<tr><td>HDI 2.1</td><td>HDP 1.3</td><td>10/28/2013</td></tr>
<tr><td>HDI 1.6</td><td>HDP 1.1</td><td>10/28/2013</td></tr>
</table><br/>


### HDInsight クラスター バージョンのサービス レベル アグリーメント (SLA)
SLA は、"サポート ウィンドウ" の条件で定義されます。サポート ウィンドウとは、HDInsight クラスターのバージョンが Microsoft カスタマー サポートによってサポートされる期間を指しています。HDInsight クラスターは、そのバージョンのサポート有効期限が現在の日付を過ぎている場合、サポート ウィンドウの枠外です。サポートされている HDInsight クラスターのバージョンの一覧は上記のテーブルに示されています。所定の HDInsight のバージョン (バージョン X とする) のサポート有効期限は、次の数式で計算した日付のうち、遅い方とされます。

- 数式 1: HDInsight クラスター バージョン X がリリースされた日に 180 日を加える。
- 数式 2: HDInsight クラスター バージョン X+1 (X の次のバージョン) が Azure 管理ポータルで使用可能になった日付に 90 日を加える。

> [WACOM.NOTE] HDInsight 2.1 および 3.0 は両方とも、.NET Framework 4.0 を含む Windows 2008 R2 SP1 の64 ビット バージョンを使用します。 

**バージョン管理に関するその他のメモと情報**	

*SQL Server JDBC ドライバーは HDInsight によって内部的に使用され、外部の操作では使用されません。ODBC を使用して HDInsight に接続する場合は、Microsoft Hive ODBC ドライバーを使用してください。Hive ODBC の使い方の詳細については、「[Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][connect-excel-with-hive-ODBC]」を参照してください。

*HDInsight クラスター バージョン 3.0 は、[Hortonworks Data Platform 2.0][hdp-2-0-8] を基盤とする Hadoop ディストリビューションを使用します。

*HDInsight クラスター バージョン 2.1 は、[Hortonworks Data Platform 1.3][hdp-1-3-0] を基盤とする Hadoop ディストリビューションを使用します。これが、Azure HDInsight ポータルの使用時に作成される既定の Hadoop クラスターです。

*HDInsight クラスター バージョン 1.6 は、[Hortonworks Data Platform 1.1][hdp-1-1-0] を基盤とする Hadoop ディストリビューションを使用します。

*HDInsight クラスターのバージョンに関連付けられたコンポーネントのバージョンは、将来 HDInsight が更新されたときに変更される可能性があります。利用可能なコンポーネントを特定し、どのバージョンがクラスターに使用されているかどうかを確認する 1 つの方法は、Ambari REST API を使用することです。GetComponentInformation コマンドを使用すると、サービス コンポーネントに関する情報を取得できます。詳細については、「[Ambari のドキュメント][ambari-docs]」を参照してください。この情報を取得するもう 1 つの方法は、リモート デスクトップを使用してクラスターにログインし、"C:\apps\dist\" ディレクトリの内容を直接確認することです。

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://www.windowsazure.com/ja-jp/support/forums/

[connect-excel-with-hive-ODBC]: /ja-jp/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

