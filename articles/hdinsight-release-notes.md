<properties 
	pageTitle="HDInsight リリース ノート | Azure" 
	description="HDInsight リリース ノート" 
	services="hdinsight" 
	documentationCenter="" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/30/2015" 
	ms.author="bradsev"/>


#Microsoft HDInsight リリース ノート

## HDinsight の 2015 年 1 月 29 日リリース ##

今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight 	2.1.10.455.1309616	(HDP 1.3.9.0-01351 - 変更なし)
* HDInsight 	3.0.6.455.1309616	(HDP 2.0.9.0-2097 -  変更なし)
* HDInsight 	3.1.2.455.1309616	(HDP 2.1.9.0-2196 -  変更なし)
* SDK			N/A

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>説明</th>
<th>影響を受ける領域 
(例: サービス、OSS コンポーネント、SDK、PS、AUX)</p></th>
<th>影響を受けるクラスターの種類 (例: Hadoop、HBase、Storm、すべて)</th>
<th>JIRA (該当する場合)</th>
</tr>


<tr>

<td> バグの修正</td>
<td>Azure のアップグレード中に HDInsight クラスターの信頼性を向上させる重要な修正をいくつか行いました。</td>
<td>サービス</td>
<td></td>
<td>該当なし</td>
</tr>



</table>
<br>

## HDinsight の 2015 年 1 月 5 日リリース ##

今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight 	2.1.10.420.1246118	(HDP 1.3.9.0-01351 - 変更なし)
* HDInsight 	3.0.6.420.1246118	(HDP 2.0.9.0-2097 - 変更なし)
* HDInsight 	3.1.2.420.1246118	(HDP 2.1.9.0-2196 - 変更なし)


このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>説明</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>


<tr>
<td>Twitter の傾向分析と Mahout に基づく映画のリコメンデーションのサンプル</td>
<td><p>このリリースでは、HDInsight クエリ コンソールに 2 つの追加のサンプルがあります。</p>

<p><b>Twitter の傾向分析</b><br>
Twitter などのサイトが公開している API を介して収集したデータは、現在の動向を分析して把握するための有益な情報源となります。このチュートリアルで Hive を使用して、特定の単語を含むツイートを最も多く送信した Twitter ユーザーの一覧を取得する方法について説明します。 </p>

<p><b>Mahout 映画のリコメンデーション</b><br>
Apache Mahout は、Apache Hadoop の機械学習ライブラリです。Mahout には、フィルター処理、分類、クラスタリングなどデータを処理するためのアルゴリズムが含まれています。このチュートリアルでは、リコメンデーション エンジンを使用し、友人たちが鑑賞した映画に基づいて映画のリコメンデーションを生成します。</p></td>
<td>クエリ コンソール</td>
<td>Hadoop</td>
<td>該当なし</td>
</tr>

<tr>
<td>hive config hive.auto.convert.join.noconditionaltask.size の既定値への変更</td>
<td><p>このサイズ構成は、自動変換されたマップの結合に適用されます。この値は、メモリに収まる hashmap に変換できるテーブルのサイズの合計を表します。以前のリリースで、この値は既定値の 10MB から 128MB に増えました。ただし、128MB という新しい値がメモリ不足でジョブが失敗する原因になっていました。このリリースでは、既定値を 10MB に戻します。ユーザーは、クエリとテーブル サイズに応じて、クラスターの作成中にこの値を変更することができます。この設定および設定を変更する方法の詳細については、Hortonworks のドキュメントの <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">「Optimize Auto Join Conversion (Auto Join Conversion の最適化)」</a> を参照してください。 </p></td>
<td>Hive</td>
<td>Hadoop、HBase</td>
<td>該当なし</td>
</tr>

</table>
<br>

## HDinsight の 2014 年 12 月 23 日リリース ##

今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight 	2.1.10.420.1246783	(HDP バージョンは変更なし)
* HDInsight 	3.0.6.420.1246783	(HDP バージョンは変更なし)
* HDInsight 	3.1.1.420.1246783	(HDP バージョンは変更なし)

このリリースには、次の更新プログラムが含まれています。

<table border="1">
<tr>
<th>タイトル</th>
<th>説明</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>


<tr>
<td>負荷が高すぎることによる断続的なクラスター作成エラー</td>
<td><p>クラスター作成中の HDP パッケージのダウンロード アルゴリズムの改良により、負荷の増加によるエラーをより確実に処理できます。この分野では、クラスター作成に関連した負荷の増加に合わせたスケーリングを向上させる改良がさらにいくつか期待されています。</p></td>
<td>サービス</td>
<td>Hadoop、Hbase、Storm</td>
<td>該当なし</td>
</tr>



</table>
<br>

## HDinsight の 2014 年 12 月 18 日リリース ##

このリリースでは、コンポーネントに対して以下の更新を実施しています。

<table border="1">
<tr>
<th>タイトル</th>
<th>説明</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>

<tr>
<td><a href = "http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">クラスター カスタマイズの GA</a></td>
<td><p>カスタマイズ機能により、Apache Hadoop エコシステムから利用できるプロジェクトで Azure HDInsight クラスターをカスタマイズできます。この新しい機能により、Hadoop プロジェクトを実験し、Azure HDInsight に展開できるようになりました。これは、カスタム スクリプトを使用して、Hadoop クラスターを任意の方法で変更できる <b>スクリプト アクション</b>機能により実現されます。このカスタマイズ機能は、Hadoop、HBase、および Storm などあらゆる種類の HDInsight クラスターで利用できます。この機能の能力を示すために、人気の高い <a href = "http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-hadoop-spark-install/" target="_blank">Spark</a>、 <a href = "http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-hadoop-r-scripts/" target="_blank">R</a>、 <a href = "http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-hadoop-solr-install/" target="_blank">Solr</a>、および <a href = "http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-hadoop-giraph-install/" target="_blank">Giraph</a> モジュールのインストール プロセスをドキュメントにまとめました。またこのリリースでは、ユーザーが Azure 管理ポータルを使用してカスタム スクリプト アクションを指定する機能が追加され、ヘルパーメソッドを使用してカスタム スクリプト アクションを作成するためのガイドラインとベスト プラクティスの提供、さらにスクリプト アクションをテストする方法に関するガイドラインの提供が行われます。 </p></td>
<td>機能の GA</td>
<td>すべて</td>
<td>該当なし</td>
</tr>


</table>
<br>

## HDinsight の 2014 年 12 月 5 日リリース ##

今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight 	2.1.9.406.1221105	(HDP 1.3.9.0-01351)
* HDInsight 	3.0.5.406.1221105	(HDP 2.0.9.0-2097)
* HDInsight 	3.1.1.406.1221105	(HDP 2.1.9.0-2196)
* HDInsight SDK N/A

このリリースでは、コンポーネントに対して以下の更新を実施しています。

<table border="1">
<tr>
<th>タイトル</th>
<th>説明</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>

<tr>
<td>バグの修正:Hive DDL でテーブルに大量のパーティションを追加する際に断続的にエラーが発生する。 </td>
<td><p>Hive テーブルに対してパーティションを大量に追加しようとした際に、Hive メタストア データベースとの間に断続的に接続エラーが発生した場合には、Hive DDL にエラーが発生することがあります。このエラーが発生した場合には、この Hive エラー ログに以下の文言が表示されます。 </p><p>"ERROR [main]: ql.Driver (SessionState.java:printError(547)) - FAILED:Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask.MetaException(message:java.lang.RuntimeException: commitTransaction was called but openTransactionCalls = 0.This probably indicates that there are unbalanced calls to openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop、HBase</td>
<td>HIVE-482 (これは、内部 JIRA の一種です。このため、外部から引用することはできません。ここには参考までに記載しています)</td>
</tr>

<tr>
<td>バグの修正:HDInsight クエリが時々ハングする  クエリ コンソール</td>
<td>このエラーが発生すると、WebHCat ランチャー ジョブの WebHCat ログに以下の文言が表示されます。 <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException):Could not load history file {wasb url to the history file}"</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (これは、内部 JIRA の一種です。このため、外部から引用することはできません。ここには参考までに記載しています)</td>
</tr>

<tr>
<td>バグの修正:HBase クエリの待機時間が急激に増加することがある</td>
<td>このエラーが発生すると、HBase クエリによる待ち時間が突然 3 秒に増加します。 </td>
<td>HDInsight クラスター ゲートウェイ</td>
<td>HBase</td>
<td>該当なし</td>
</tr>

<tr>
<td>HDP jar ファイルの名称変更</td>
<td>バージョン 3.0 の HDI クラスターでは、HDP によってイントールされる内部の jar ファイルの名称がいくつか変更になります。jetty-6.1.26.jar は、
 jetty-6.1.26.hwx.jar という名称に変更になります。jetty-util-6.1.26.jar は、jetty-util-6.1.26.hwx.jar という名称に変更になります。これらの変更は、Hadoop、Mahout、WebHCat および Oozie プロジェクトに適用されます。**</td>
<td>Hadoop、Mahout、WebHCat、Oozie</td>
<td>Hadoop、HBase</td>
<td>該当なし</td>
</tr>

</table>
<br>


## HDinsight の 2014 年 11 月 25 日リリース ##

今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight 2.1.9.382.1169709 (2014 年 11 月 14 日から変更なし)
* HDInsight 3.0.5.382.1169709 (2014 年 11 月 14 日から変更なし)
* HDInsight 3.1.1.382.1169709 (2014 年 11 月 14 日から変更なし)
* HDInsight SDK 1.4.0

このリリースでは、コンポーネントに対して以下の更新を実施しています。

<table border="1">
<tr>
<th>タイトル</th>
<th>説明</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>

<tr>
<td>アプリケーション ログへのアクセス</td>
<td>クラスターで実行されたアプリケーションをプログラムを使って列挙し、アプリケーションまたはコンテナーごとのログをダウンロードできるようになりました。この機能は、問題のあるアプリケーションのデバッグに役立ちます。</td>
<td>SDK</td>
<td>Hadoop</td>
<td>該当なし</td>
</tr>

<tr>
<td>IHdInsightClient.DeleteCluster でのリージョン名の指定 </td>
<td>Azure HDInsight SDK で **DeleteCluster** の使用時にリージョン名の指定が可能になりました。これまでは、別々のリージョンに同じ名前のリソースが 1 つずつある場合に、どちらかだけを削除することはできませんでした。この更新では、そのような問題が解消します。</td>
<td>SDK</td>
<td>すべて</td>
<td>該当なし</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>**ClusterDetails** オブジェクトが **DeploymentID** フィールドを返すようになりました。このフィールドは、クラスターを一意に識別する情報です。この情報は、同じ名前のクラスターを作成しようとしても常に一意の状態が確保されます。</td>
<td>SDK</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>
<br>

## HDInsight の 2014 年 11 月 14 日リリース ##

今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

このリリースでは、以下の機能追加、コンポーネントの更新、およびバグ修正が実施されます。

<table border="1">
<tr>
<th>タイトル</th>
<th>説明</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>

<tr>
<td>スクリプト アクション (プレビュー):</td>
<td>クラスター カスタマイズ機能のプレビュー版であり、カスタム スクリプトを使用して任意の方法で Hadoop クラスターを変更できます。この新機能により、ユーザーは、Apache Hadoop エコシステムから入手できるプロジェクトを検証し、Azure HDInsight クラスターにデプロイできます。このカスタマイズ機能は、Hadoop、HBase、Storm など、すべての種類の HDInsight クラスターで使用できます。</td>
<td>新機能</td>
<td>すべて</td>
<td>該当なし</td>
</tr>

<tr>
<td>Azure Websites および Azure Storage のログ分析用に既成のジョブを用意</td>
<td>HDInsight クエリ コンソールには、既にあるデータまたはサンプルのデータを使用するソリューションで役立つ初心者向けギャラリーが用意されています。
<p>既にあるデータを使用するソリューション:<br>
データ分析のなかでも特によくあるもののいくつかについて、ジョブを作成しました。自分でソリューションを作成する際の第一歩として役立ちます。各ジョブには既にお持ちのデータを使って、どのように動作するかを確認することができます。その後準備ができたら、これまでに得た知識を使って、既成のジョブを手本に自分のソリューションを作成します。</p>
<p>サンプルのデータを使用するソリューション:<br>
Web ログやセンサー データの分析など、基本的なシナリオをいくつかなぞっていくことによって、HDInsight の使い方を学ぶことができます。HDInsight を使ってデータを分析する方法だけでなく、その他のアプリケーションやサービスにこのデータを接続する方法も学べます。こちらは、Microsoft Excel に接続してデータを視覚化する場合などにこ役立ちます。</p></td>
<td>クエリ コンソール</td>
<td>Hadoop</td>
<td>該当なし</td>
</tr>

<tr>
<td>Templeton のメモリ リークを修正</td>
<td>クラスターを長時間実行したり、1 秒あたり何百回とジョブ リクエストを送信したりした場合に問題になっていた Templeton のメモリ リークを修正しました。この Templeton 5xx という種のエラーについては、回避策としてサービスを再起動する必要がありました。今回の更新により、再起動は不要になりました。</td>
<td>Templeton</td>
<td>すべて</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>
<br>


注:クラスターのカスタマイズによって利用が可能になる新機能の例として、スクリプト アクションを使ってクラスターに Spark と R の 2 つのモジュールをインストールする手順が文書化されています。詳細については、以下を参照してください。

* [Install and use Spark 1.0 on HDInsight clusters (HDInsight クラスターで Spark をインストールして使用する)][hdinsight-install-spark]
* [Install and use R on HDInsight Hadoop clusters (HDInsight Hadoop クラスターで R をインストールして使用する)][hdinsight-r-scripts]




## HDInsight の 2014 年 11 月 7 日リリース ##

今回のリリースがデプロイされている HDInsight クラスターのバージョン番号は、以下のとおりです。

* HDInsight 2.1	2.1.9.374.1153876
* HDInsight 3.0	3.0.5.374.1153876
* HDInsight 3.1	3.1.1.374.1153876

このリリースでは、コンポーネントに対して以下の更新を実施しています。

<table border="1">
<tr>
<th>タイトル</th>
<th>説明</th>
<th>コンポーネント</th>
<th>クラスターの種類</th>
<th>JIRA (該当する場合)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>このリリースは、Hortonworks Data Platform (HDP) 2.1.7 に基づくものです。HDP 2.1.7 のリリース ノートは、Hortonworks のサイト (http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html) で確認できます。</td>
<td>HDP</td>
<td>すべて</td>
<td>該当なし</td>
</tr>

<tr>
<td>YARN Timeline Server</td>
<td>YARN Timeline Server (別名 Generic Application History Server) は、既定で有効になっています。Timeline Server は、アプリケーション ID、アプリケーション名、アプリケーションの状態、アプリケーションの送信時刻、アプリケーションの完了時刻など、完了済みのアプリケーションに関する全般的情報を提供するものです。 <p>このアプリケーション情報は、ヘッド ノードから URI (http://headnodehost:8188) にアクセスするか、YARN コマンド (yarn application -list -appStates ALL) を実行すると取得できます。</p> 
<p>この情報は、REST API (https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/) からリモートで取得することもできます。</p> 
<p>Timeline Server に関する詳しい情報は、http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html を参照してください。</p></td>
<td>サービス、YARN</td>
<td>Hadoop、HBase</td>
<td>該当なし</td>
</tr>

<tr>
<td>クラスター デプロイメント ID</td>
<td>SDK の最新バージョン (1.3.3.1.5426.29232) より、HDInsight が各クラスターに対して発行した一意の ID にアクセスできるようになります。これにより、作成とドロップのシナリオで DNS 名を再利用する場合に、ユーザーがクラスターのインスタンスを一意に特定できるようになります。</td>
<td>SDK</td>
<td>すべて</td>
<td>該当なし</td>
</tr>
</table>
<br>

* ポータルにバージョン番号の全体が表示されなかったバグと、SDK または PowerShell によって返されるバージョン番号が全部でなかったバグについても、今回のリリースで修正しました。 

## 2014 年 10 月 15 日リリース ##

この修正プログラムのリリースでは、Templeton のヘビー ユーザーに影響を与えた Templeton のメモリ リークを修正しました。場合によっては、要求の実行に十分なメモリがないために、Templeton を何回も実行したユーザーに 500 エラー コードとしてエラーが表示されることがあります。Templeton サービスを再起動すると、この問題を回避できます。この問題は現在修正されています。


## 2014 年 10 月 7 日リリース ##

* Ambari エンドポイント (https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}) を使用すると、 *host_name* フィールドがホスト名そのものではなくノードの完全修飾ドメイン名 (FQDN) を返します。たとえば、"**headnode0**" を返す代わりに、FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**" を返します。この変更は、1 つの仮想ネットワーク (VNET) に HBase や Hadoop などの複数のクラスターの種類をデプロイできるシナリオの実現を容易にするために必須でした。このシナリオは、Hadoop のバックエンド プラットフォームとして HBase を使用する場合などが該当します。

* HDInsight クラスターの既定のデプロイメントのために、新しいメモリ設定が用意されています。前の既定のメモリ設定は、デプロイされる CPU コアの数についてガイダンスの考慮が適切ではありませんでした。今回の新しいメモリ設定は、Hortonworks による推奨に基づき、既定の設定を改善するものです。メモリ設定の変更については、クラスター構成の変更に関する SDK リファレンス ドキュメントを参照してください。既定の 4 CPU コア (8 コンテナー) HDInsight クラスターで使用される新しいメモリ設定を次の表に示します(前のリリースで使用された値もかっこ内に記載します)。 
 
<table border="1">
<tr><th>コンポーネント</th><th>メモリの割り当て</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768MB (以前は 512MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144MB (変更なし)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144MB (変更なし)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768MB (以前は 512MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-X m x 512 m (以前は -X m x 410 m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536MB (以前は 1024MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-X m x 1024 m (以前は -X m x 819 m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768MB (以前は 1024MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-X m x 512 m (以前は -X m x 819 m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256 MB (以前は 200 MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536 MB (変更なし)</td></tr>

</table><br>

HDInsight で使用される Hortonworks Data Platform で YARN および MapReduce によって使用されるメモリ構成の設定の詳細については、「[Determine HDP Memory Configuration Settings (HDP メモリ構成の設定の決定)](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html)」を参照してください。Hortonworks は、適切なメモリ設定を計算するツールも提供しています。

HDInsight PowerShell/SDK のエラー:"*Cluster is not configured for Http Services access (クラスターが HTTP サービスのアクセス用に構成されていません)*":

* このエラーは既知の[互換性の問題](https://social.msdn.microsoft.com/Forums/azure/en-US/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight)であり、SDK/PowerShell のバージョンとクラスターのバージョンが異なることが原因で起こることがあります。8 月 15 日以降に作成されたクラスターは、仮想ネットワークへの新しいプロビジョニング機能をサポートします。ただし、SDK/PowerShell の以前のバージョンでは、この機能が正しく解釈されません。一部のジョブ送信の操作で失敗する結果になります。SDK API または PowerShell コマンドレットを使用して、ジョブ (**Use-AzureHDInsightCluster**, **Invoke-Hive**) を送信すると、エラー メッセージ "*Cluster <clustername> is not configured for Http Services access(クラスター <クラスタ名> は Http サービス アクセス用に構成されていません)*" または、操作によってはこれ以外にも、"*Cannot connect to cluster (クラスターに接続できません)*" などのエラー メッセージが表示されてこれらの操作が失敗する可能性があります。

* これらの互換性の問題は、HDInsight SDK および Azure PowerShell の最新バージョンで解決されます。HDInsight SDK をバージョン 1.3.1.6 以降に、Azure PowerShell ツールをバージョン 0.8.8 以降に更新することをお勧めします。[NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) から最新の HDInsight SDK に、「[Azure PowerShell のインストールおよび構成方法](http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell/)」から Azure PowerShell ツールにアクセスできます。

* SDK および PowerShell は、クラスターのバージョンが同じである限り、クラスターへの新しい更新プログラムを引き続き処理すると想定できます。たとえば、クラスター Version 3.1 は、常に最新のバージョン SDK/PowerShell 1.3.1.6 および 0.8.8 と互換性があります。


## HDInsight 3.1 の 2014 年 9 月 12 日リリース##

* このリリースは、Hortonworks Data Platform (HDP) 2.1.5 に基づくものです。このリリースで修正されたバグの一覧については、Hortonworks のサイトで「[Fixed in this Release (このリリースでの修正点)](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html)」ページを参照してください。
* Pig ライブラリ フォルダーで、ファイル "avro-mapred-1.7.4.jar" が "avro-mapred-1.7.4-hadoop2.jar" に変更されています。これらのファイルの内容には、重要でない小さなバグ修正が含まれます。ファイルの名前が変更されたときにファイルが壊れるのを避けるため、JAR ファイル自体の名前と直接依存関係を持たないことをお勧めします。


## 2014 年 8 月 21 日リリース ##

* 以下の新しい WebHCat 構成 (HIVE-7155) を追加しました。この構成は Templeton コントローラーのジョブに対し、既定のメモリ上限を 1 GB (以前の既定値は 512 MB) に設定します。
	
	* templeton.mapper.memory.mb (=1024)
	* これにより、メモリ上限が低いことが原因となり特定の Hive クエリで発生する次のエラーに対処します。"コンテナーは物理メモリ上限を超過して実行しています"
	* クラスター作成時に PowerShell SDK で以下のコマンドを使用すると、この構成値を使用していない既定値の 512 に戻すことができます。
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* zookeeper ロールのホスト名が zookeeper に変更されました。これはクラスター内の名前解決には影響しますが、外部の REST API には影響しません。ホスト名 zookeepernode を使用するコンポーネントがある場合は、代わりに新しい名前を使用するようにそのコンポーネントを更新する必要があります。3 つの zookeeper ノードの新しい名前は以下のとおりです。 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* HBase のバージョン サポート マトリックスが更新されました。運用環境の HBase ワークロードでサポートされるのは、バージョン HDInsight 3.1 (HBase バージョン 0.98) のみです。プレビューで利用可能だったバージョン 3.0 は、今後はサポートされなくなります。移行期間中はバージョン 3.0 のクラスターを引き続き作成できます。 

## 2014 年 8 月 15 日より前に作成されたクラスターに関する留意事項 ##

メッセージ "クラスター <クラスター名> がHTTP サービスのアクセス用に構成されていません" (操作によっては"クラスターに接続できません" などのその他のメッセージ) を伴う HDInsight PowerShell/SDK のエラーが、SDK/PowerShell とクラスターのバージョンが異なることが原因で発生する場合があります。8 月 15 日以降に作成されたクラスターは、仮想ネットワークへの新しいプロビジョニング機能をサポートします。SDK/PowerShell の以前のバージョンでは、この機能が正しく解釈されません。その結果、ジョブの送信の操作で失敗します。SDK API または PowerShell コマンドレットを使用して Use-AzureHDInsightCluster や Invoke-AzureHDInsightHiveJob などのジョブを送信する場合、これらの操作は上に示したエラー メッセージで失敗する可能性があります。

これらの互換性の問題は、SDK および Azure PowerShell の最新バージョンで解決されます。HDInsight SDK をバージョン 1.3.1.6 以降に、Azure PowerShell ツールをバージョン 0.8.8 以降に更新することをお勧めします。[NuGet](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/) から最新の HDInsight SDK に、「[Microsoft Web PI](http://go.microsoft.com/?linkid=9811175&clcid=0x409)」から Azure PowerShell ツールにアクセスできます。

SDK および PowerShell は、クラスターのバージョンが同じである限り、クラスターへの新しい更新プログラムを引き続き処理すると想定できます。たとえば、クラスター Version 3.1 は、常に最新のバージョン SDK/PowerShell 1.3.1.6 および 0.8.8 と互換性があります。


## 2014 年 7 月 28 日リリース ##

* **HDInsight が新しいリージョンで利用可能**:このリリースでは、HDInsight の地理的なプレゼンスを以下の新しい 3 つのリージョンに拡張しています。HDInsight の顧客は、これらのリージョンでクラスターを作成できるようになりました。 
	* 東アジア 
	* 米国中北部 
	* 米国中南部 
* このリリースでは、HDInsight v1.6 (HDP1.1、Hadoop 1.0.3) および HDInsight v2.1 (HDP1.3、Hadoop 1.2) が Azure の管理ポータルから削除されています。これらのバージョンの Hadoop クラスターは、HDInsight PowerShell コマンドレット ([New-AzureHDInsightCluster](http://msdn.microsoft.com/ja-jp/library/dn593744.aspx)) または [HDInsight SDK](http://msdn.microsoft.com/ja-jp/library/azure/dn469975.aspx) を使用すれば引き続き作成できます。詳細については、「[HDInsight で提供されるクラスター バージョンの新機能](http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-component-versioning/)」のページを参照してください。
* このリリースでは、Hortonworks Data Platform (HDP) が以下のように変更されました。 

<table border="1">
<tr><th>HDP</th><th>変更点</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>変更なし</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>変更なし</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper:['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## 2014 年 6 月 24 日リリース ##

このリリースには、以下のように、HDInsight サービスの新しい拡張機能が数種類含まれています。 

* **HDP 2.1 が使用可能**:HDP 2.1 が含まれる HDInsight 3.1 が一般に使用可能となり、新しいクラスターの既定のバージョンになりました。
* **HBase - Azure の管理ポータルの改良**:HBase クラスターがプレビューで使用可能になりました。ポータルで 3 回クリックするだけで HBase クラスターが作成できるようになりました。

![](http://i.imgur.com/cmOl5fM.png)

HBase を使用すると、大規模なデータセットを処理する対話的な Web サイトから、何百万ものエンド ポイントからのセンサーのデータや利用統計情報のデータを格納するサービスまで、さまざまなリアルタイムのワークロードを HDInsight で構築できます。次の手順としては Hadoop ジョブでこれらのワークロードのデータを分析しますが、これは PowerShell や Hive クラスター ダッシュボードなどと同様の方法によって HDInsight で即座に実現可能です。

### Apache Mahout が HDInsight 3.1 にプレインストール ###

 HDInsight 3.1 Hadoop クラスターには [Mahout](http://hortonworks.com/hadoop/mahout/) がプレインストールされています。そのため、他のクラスター構成を追加することなく、Mahout ジョブを実行できます。たとえば、リモート デスクトップ プロトコル (RDP) を使用して Hadoop クラスターにリモート接続したり、以下のように、追加の手順なしで Hello world Mahout コマンドを実行したりできます。

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

この手順の詳しい説明については、Apache Mahout Web サイトのドキュメント「[Breiman Example (Breiman の例)](https://mahout.apache.org/users/classification/breiman-example.html)」を参照してください。 


### HDInsight 3.1 で Hive クエリに Tez を使用可能 ###

HDInsight 3.1 で Hive 0.13 が利用可能になり、Tez を使用してクエリを実行できるようになりました。これを活用してパフォーマンスを向上できます。 
既定では、Hive クエリでの Tez の使用は有効ではありません。これを使用するにはオプトインが必要です。以下のコード スニペットを実行すると Tez を有効にできます。

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks では、Tez を使用した Hive クエリのパフォーマンスの向上について、標準ベンチマークによる詳細なブレークダウンを公開しています。詳細については、「[Benchmarking Apache Hive 13 for Enterprise Hadoop (Enterprise Hadoop 向け Apache Hive 13 のベンチマーク)](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/)」を参照してください。 

Tez を使用した Hive に関する詳細については、[Hive on Tez wiki page (wiki ページ「Tez を使用した Hive」)](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) を参照してください。

###グローバル対応
Hadoop 2.2 対応の Azure HDInsight のリリースに伴い、すべての主要な Azure 地域で HDInsight が利用可能になりました。具体的には、西ヨーロッパと東南アジアのデータ センターがオンラインになりました。これで、お客様は、距離的に近く、コンプライアンス要件が似ていると思われる地域にあるデータ センター内のクラスターを見つけることができます。 


###クラスター バージョン間でできることとできないこと

**HDInsight 3.1 クラスターで使用される Oozie メタストアは HDInsight 2.1 クラスターとの下位互換性はなく、以前のバージョンで使用できません。**

HDInsight 3.1 クラスターでデプロイされたカスタム Oozie メタストア データベースは、HDInsight 2.1 クラスターでは再利用できません。これは、そのメタストアが最初に 2.1 クラスターで作成されたものであったとしても同じです。3.1 クラスターで使用するときにメタストア スキーマがアップグレードされるため、このシナリオはサポートされていません。そのため、2.1 クラスターで必要とされるメタストアとの互換性がなくなります。HDInsight 3.1 クラスターで使用されていた Oozie メタストアを再利用しようと試みると、2.1 クラスターが使用できなくなります。 

**Oozie メタストアはクラスター間で共有できない**
より一般的な言い方をすれば、Oozie メタストアは特定のクラスターに接続されるものであり、クラスター間で共有することはできません。

###重大な変更

**プレフィックスの構文**:
HDInsight 3.0 および 3.1 クラスターでは、"wasb://" 構文のみがサポートされます。旧バージョンの "asv://" 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターまたは以降のバージョンではサポートされません。HDInsight 3.0  または 3.1 クラスターに送信され、明示的に "asv://" 構文を使用するジョブはすべて失敗します。代わりに、wasb:// 構文を使用する必要があります。また、既存のメタストアにより、asv:// 構文を使用してリソースへの明示的な参照を含むジョブを作成し、HDInsight 3.0 または 3.1 クラスターに送信すると、そのジョブは失敗します。これらのメタストアは、wasb:// を使用してリソースをアドレス指定するように再作成する必要があります。 


**ポート**:HDInsight サービスで使用されるポートが変更されました。これまで使用されてきたポート番号は、Windows OS の一時的なポート範囲内の番号です。ポートは、短期間のインターネット プロトコル ベースの通信に対して一時的に事前定義される範囲から自動的に割り当てられます。今回 Hortonworks Data Platform (HDP) サービスに許可された新しいポート番号のセットは、この範囲の外部にあり、ヘッドノードで実行するサービスが使用するポート番号の競合の可能性が回避されます。新しいポート番号によって重大な変更が発生することはありません。今回使用される番号は以下のとおりです。

 **HDInsight 1.6 (HDP 1.1)**
<table border="1">
<tr><th>名前</th><th>値</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.0 および 3.1 (HDP 2.0 および 2.1)**
<table border="1">
<tr><th>名前</th><th>値</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###依存関係 

HDInsight 3.x (HDP2.x) に以下の依存関係が追加されました。

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

HDInsight 3.x (HDP2.x) から削除された依存関係は以下のとおりです。

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###バージョンの変更 

HDInsight 2.x (HDP1.x) と HDInsight 3.x (HDP2.x) との間で以下のバージョンが変更されました。

* metrics-core:['2.1.2'] -> ['3.0.0']
* derbynet:['10.4.2.0'] -> ['10.10.1.1']
* datanucleus:['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler:['5.5.12'] -> ['5.5.23']
* log4j:['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient:['10.4.2.0'] -> ['10.10.1.1']
* httpcore:['4.2.4'] -> ['4.2.5']
* hsqldb:['1.8.0.10'] -> ['2.0.0']
* jets3t:['0.6.1'] -> ['0.9.0']
* protobuf-java:['2.4.1'] -> ['2.5.0']
* derby:['10.4.2.0'] -> ['10.10.1.1']
* jasper:['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon:['1.0.1'] -> ['1.0.13']
* datanucleus-core:['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo:['3.0.7'] -> ['3.2.6']
* zookeeper:['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp:['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###ドライバー
SQL Server JDBC ドライバーは HDInsight によって内部的に使用され、外部の操作では使用されません。ODBC を使用して HDInsight に接続する場合は、Microsoft Hive ODBC ドライバーを使用してください。Hive ODBC の使い方の詳細については、「[Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][connect-excel-with-hive-ODBC]」を参照してください。


### バグの修正 ###

このリリースで、次の HDInsight   (Hortonworks Data Platform - HDP) バージョンをいくつかのバグ修正により更新しました。

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## Hortonworks リリース ノート ##

各バージョンの HDInsight クラスターで使用される HDP のリリース ノートは、以下の場所から利用可能です。

* HDInsight クラスター Version 3.1 は、[Hortonworks Data Platform 2.1.7][hdp-2-1-7] を基盤とする Hadoop ディストリビューションを使用します (これは、2014/11/07 以降の Azure HDInsight ポータルを使用する場合に作成される既定の Hadoop クラスターです)。2014/11/07 以前に作成された HDInsight クラスター Version 3.1 は、[Hortonworks Data Platform 2..1.1][hdp-2-1-1] を基盤としていました。 

* HDInsight クラスター バージョン 3.0 は、[Hortonworks Data Platform 2.0][hdp-2-0-8] を基盤とする Hadoop ディストリビューションを使用します。

* HDInsight クラスター バージョン 2.1 は、[Hortonworks Data Platform 1.3][hdp-1-3-0] を基盤とする Hadoop ディストリビューションを使用します。 

* HDInsight クラスター バージョン 1.6 は、[Hortonworks Data Platform 1.1][hdp-1-1-0] を基盤とする Hadoop ディストリビューションを使用します。 

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html


[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/



<!--HONumber=42-->
