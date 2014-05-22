<properties linkid="manage-services-hdinsight-howto-monitor-hdinsight" urlDisplayName="監視" pageTitle="HDInsight の監視 | Azure" metaKeywords="" description="HDInsight クラスターを監視し、Azure 管理ポータルを介して Hadoop ジョブ履歴を表示する方法を説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight を監視する方法" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />




#HDInsight を監視する方法

このトピックでは、HDInsight クラスターを監視する方法を学習します。

##目次

* [方法: HDInsight クラスターを監視する](#monitorcluster)
* [方法: Hadoop ジョブ履歴を表示する](#jobhistory)

##<a id="monitorcluster"></a>方法: HDInsight クラスターを監視する

HDInsight クラスターの状態と HDInsight クラスター上で実行されている Hadoop ジョブを監視するには、HDInsight ダッシュボードに接続して、[クラスターの管理] タイルをクリックします。

![HDI.TileMonitorCluster][hdi-monitor-cluster-tile]

次のような監視ページが表示されます。

![HDI.MonitorPage][hdi-monitor-page]


画面右側に、Namenode とジョブ追跡機能の両方が実行中であること、および 4 つのデータ ノードが正常に実行されていることが示されています。

画面左側には、過去 30 分間の MapReduce 統計データが表示されます。データの監視期間は、30 分、1 時間、3 時間、12 時間、1 日、2 日、1 週間、2 週間から選択できます。

##<a id="jobhistory"></a>方法: Hadoop ジョブ履歴を表示する
Hadoop ジョブ履歴を表示するには、HDInsight ダッシュボードに接続して [ジョブ履歴] タイルをクリックします。

![HDI.TileJobHistory][hdi-job-history-tile]

このタイルには、これまでに実行されたジョブの数が表示されます。たとえば、前の画像では、6 つのジョブの履歴を参照できることがわかります。次のようなジョブ履歴ページが表示されます。

![HDI.JobHistoryPage][hdi-job-history-page]


##関連項目

* [方法: HDInsight を管理する](/ja-jp/manage/services/hdinsight/howto-administer-hdinsight/)
* [方法: HDInsight クラスターをプログラムで展開する](/ja-jp/manage/services/hdinsight/howto-deploy-cluster/)
* [方法: HDInsight クラスターでリモート ジョブをプログラムから実行する](/ja-jp/manage/services/hdinsight/howto-execute-jobs-programmatically/)
* [チュートリアル: Azure HDInsight の概要](/ja-jp/manage/services/hdinsight/get-started-hdinsight/)

[hdi-monitor-cluster-tile]: ./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG
[hdi-monitor-page]: ./media/hdinsight-monitor/HDI.MonitorPage.PNG
[hdi-job-history-tile]: ./media/hdinsight-monitor/HDI.TileJobHistory.PNG
[hdi-job-history-page]: ./media/hdinsight-monitor/HDI.JobHistoryPage.PNG

