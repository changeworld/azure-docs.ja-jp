---
title: "Azure HDInsight のドキュメント - トラブルシューティング ガイド | Microsoft Docs"
description: "HDInsight 上の Hadoop ワークロードのトラブルシューティングを行います。手順に関するドキュメントでは、HDInsight での Hive、Spark、HBase、Storm、Kafkaに関する一般的な問題の解決方法が示されています。"
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 29aa2510fc04f24c437d6bb6142eebb8859569c0
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---


# <a name="azure-hdinsight-troubleshooting"></a>Azure HDInsight のトラブルシューティング

| Apache ワークロード | よくある質問 |
|---|---|
|![HBASE](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[HBASE のトラブルシューティング](hdinsight-troubleshoot-hbase.md)|<br>[複数の未割り当てリージョンで hbck コマンド レポートを実行する方法](hdinsight-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[リージョンの割り当てに関する hbck コマンドで生じるタイムアウトの問題を解決する方法](hdinsight-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[クラスターで HDFS のセーフ モードを無効にする方法](hdinsight-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-an-cluster)<br><br>[Apache Phoenix との JDBC 接続または sqlline 接続の問題を解決する方法](hdinsight-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[マスター サーバーの起動が失敗する原因](hdinsight-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[リージョン サーバーでの再起動が失敗する原因](hdinsight-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[HDFS のトラブルシューティング](hdinsight-troubleshoot-hdfs.md)|<br>[クラスター内からローカルの HDFS にアクセスする方法](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[クラスターで HDFS のセーフ モードを無効にする方法](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![HIVE](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[HIVE のトラブルシューティング](hdinsight-troubleshoot-hive.md)|<br>[Hive metastore をエクスポートして別のクラスターにインポートする方法](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[クラスターにある Hive のログを特定する方法](hdinsight-troubleshoot-hive.md#how-do-locate-hive-logs-on-a-cluster)<br><br>[クラスターで特定の構成を使って Hive シェルを起動する方法](hdinsight-troubleshoot-hive.md#how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[クラスターのクリティカル パスで Tez DAG データを分析する方法](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[クラスターから Tez DAG データをダウンロードする方法](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![SPARK](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Spark のトラブルシューティング](hdinsight-troubleshoot-SPARK.md)|<br>[クラスターで Ambari を使用して Spark アプリケーションを構成するにはどうすればよいですか](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-ambari-on-clusters)<br><br>[クラスターで Jupyter ノートブックを使用して Spark アプリケーションを構成するにはどうすればよいですか](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters)<br><br>[クラスターで LIVY を使用して Spark アプリケーションを構成するにはどうすればよいですか](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-livy-on-clusters)<br><br>[クラスターで spark-submit を使用して Spark アプリケーションを構成するにはどうすればよいですか](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-spark-submit-on-clusters)<br><br>[Spark アプリケーションの OutOfMemoryError 例外の原因は何ですか](hdinsight-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![STORM](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[STORM のトラブルシューティング](hdinsight-troubleshoot-STORM.md)|<br>[クラスターの Storm UI にアクセス方法](hdinsight-troubleshoot-storm.md#how-do-i-access-storm-ui-on-a-cluster)<br><br>[Storm EventHub Spout チェックポイント情報の 1 つのトポロジから別のトポロジへの転送する方法](hdinsight-troubleshoot-storm.md#how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[クラスターで Storm バイナリを見つける方法](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Storm クラスターのデプロイ トポロジを特定する方法](hdinsight-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[開発用に Storm-EventHub-Spout バイナリを見つける方法](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-eventhub-spout-binaries-for-development)<br><br>[クラスターで Storm Log4J 構成ファイルを見つける方法](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[YARN のトラブルシューティング](hdinsight-troubleshoot-YARN.md)|<br>[クラスターで新しい Yarn キューを作成する方法](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[クラスターから Yarn ログをダウンロードする方法](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight のトラブルシューティング リソース
その他のトラブルシューティング ヘルプについては、次の記事をご覧ください。

| 対象 | 参考資料 |
| --- | --- |
| Linux 上の HDInsight と最適化 | [Linux での HDInsight の使用方法](hdinsight-hadoop-linux-information.md)<br>[Hadoop のメモリとパフォーマンス](hdinsight-hadoop-stack-trace-error-messages.md)<br>[Hive クエリのパフォーマンス](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| ログとダンプ | [Linux での Hadoop YARN アプリケーション ログへのアクセス](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>[Hadoop サービスのヒープ ダンプの有効化](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>[HDInsight ログの分析](hdinsight-debug-jobs.md)|
| エラー | [WebHCat エラーの説明と解決策](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>[メモリ不足エラーを修正するための Hive 設定](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| ツール | [Ambari ビューを使用して Tez ジョブをデバッグする](hdinsight-debug-ambari-tez-view.md)<br>[Hive クエリの最適化](hdinsight-hadoop-optimize-hive-query.md) |



