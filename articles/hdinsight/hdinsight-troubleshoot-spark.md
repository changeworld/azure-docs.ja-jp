---
title: "Spark のトラブルシューティング - Azure HDInsight | Microsoft Docs"
description: "Spark のよくあるご質問を利用して、Azure HDInsight プラットフォーム上の Spark に関する一般的な質問に対する回答を示します。"
keywords: "Azure HDInsight, Spark, よくあるご質問, トラブルシューティング ガイド, 一般的な問題, アプリケーションの構成, Ambari"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 7655acd689c3f5b869eb0ddb5f186cff032ca8b6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="spark-troubleshooting"></a>Spark のトラブルシューティング

この記事では、Apache Ambari での Spark ペイロードの操作に関連する主な問題とその解決策について説明します。

## <a name="how-do-i-configure-a-spark-application-through-ambari-on-clusters"></a>クラスターで Ambari を使用して Spark アプリケーションを構成するにはどうすればよいですか

### <a name="issue"></a>問題:

Ambari で、Spark アプリケーションで使用可能なメモリの量とコアの数を構成する必要があります。  

### <a name="resolution-steps"></a>解決手順: 

この手順では、HDInsight Spark クラスターで設定済みの構成値を調整します。 Spark のどの構成をどのような値に設定する必要があるかを判断するには、「[Why did my Spark application fail with an OutOfMemoryError? (Spark アプリケーションが OutOfMemoryError により失敗したのはなぜですか)](#why-did-my-spark-application-fail-with-an-outofmemoryerror)」を参照してください。

1. クラスターの一覧から **[Spark2]** を選択します。

    ![一覧からのクラスターの選択](media/hdinsight-troubleshoot-spark/update-config-1.png)

1. **[Configs]** タブをクリックします。

    ![[Configs]\(構成\) タブの選択](media/hdinsight-troubleshoot-spark/update-config-2.png)

1. 構成の一覧で **[Custom-spark2-defaults]** を選択します。

    ![[custom-spark-defaults] の選択](media/hdinsight-troubleshoot-spark/update-config-3.png)

1. **[spark.executor.memory]** など、調整する必要のある値の設定を見つけます。 この場合、4608m という値は大きすぎます。

    ![[spark.executor.memory] フィールドの選択](media/hdinsight-troubleshoot-spark/update-config-4.png)

1. 推奨される値に設定します。 この設定では、2048 が推奨される値です。

    ![値を 2048m へと変更](media/hdinsight-troubleshoot-spark/update-config-5.png)

1. 設定値を保存してから、構成を保存します。 

    ツール バーの **[Save]\(保存\)** をクリックします。

    ![設定および構成の保存](media/hdinsight-troubleshoot-spark/update-config-6a.png)

    注意の必要な値がある場合は警告が表示されます。 警告をメモして **[Proceed Anyway]\(警告を無視して続行\)** をクリックします。 

    ![[Proceed Anyway]\(警告を無視して続行\) をクリック](media/hdinsight-troubleshoot-spark/update-config-6b.png)

    構成の変更内容に関するメモを記入して、**[Save]\(保存\)** をクリックします。

    ![変更内容のメモの記入](media/hdinsight-troubleshoot-spark/update-config-6c.png)

1. 構成を保存すると、サービスを再起動するように求められます。 **[Restart]\(再起動\)**をクリックします。

    ![[Restart]\(再起動\) をクリック](media/hdinsight-troubleshoot-spark/update-config-7a.png)

    再起動を確定します。

    ![[Confirm Restart All]\(すべて再起動\) をクリック](media/hdinsight-troubleshoot-spark/update-config-7b.png)

    実行中のプロセスを確認できます。

    ![実行中のプロセスの確認](media/hdinsight-troubleshoot-spark/update-config-7c.png)

1. また、構成を追加することもできます。 構成の一覧で、手順 3 で行ったのと同様に **[custom-spark2-defaults]** を選択し、**[Add Property]\(プロパティの追加\)** をクリックします。

    ![[Add Property]\(プロパティの追加\) をクリック](media/hdinsight-troubleshoot-spark/update-config-8.png)

1. 新しいプロパティを定義します。 データ型など特定の設定用のダイアログを使用して単一のプロパティを定義するか、複数のプロパティを 1 行に 1 つずつ定義することができます。 

    この例では、"**spark.driver.memory**" プロパティの値を "4g" に定義しています。

    ![新しいプロパティの定義](media/hdinsight-troubleshoot-spark/update-config-9.png)

1. 構成を保存し、手順 6 と 7 の説明に従ってサービスを再起動します。

これらの変更はクラスター全体に適用されますが、実際の Spark ジョブの送信時に上書きできます。

### <a name="further-reading"></a>参考資料:

[HDInsight クラスターでの Spark ジョブの送信](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters"></a>クラスターで Jupyter ノートブックを使用して Spark アプリケーションを構成するにはどうすればよいですか

### <a name="issue"></a>問題:

HDInsight クラスターで Jupyter ノートブックを使用する場合に Spark アプリケーションで使用可能なメモリの量とコアの数を構成する必要があります。 

1. Spark のどの構成をどのような値に設定する必要があるかを判断するには、「[Why did my Spark application fail with an OutOfMemoryError? (Spark アプリケーションが OutOfMemoryError により失敗したのはなぜですか)](#spark-application-failure-outofmemory)」を参照してください。
1.  Spark 構成を、%%configure ディレクティブの後にある最初の Jupyter ノートブックのセルに有効な JSON 形式で指定します (実際の値は適切に変更します)。 

>![構成の追加](media/hdinsight-troubleshoot-spark/add-configuration-cell.png)

### <a name="further-reading"></a>参考資料:

[HDInsight クラスターでの Spark ジョブの送信](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-livy-on-clusters"></a>クラスターで LIVY を使用して Spark アプリケーションを構成するにはどうすればよいですか

### <a name="issue"></a>問題:

LIVY を使用した送信時に、Spark アプリケーションが HDInsight クラスター上で使用可能なメモリの量とコアの数を構成する必要があります。 

1. Spark のどの構成をどのような値に設定する必要があるかを判断するには、「[Why did my Spark application fail with an OutOfMemoryError? (Spark アプリケーションが OutOfMemoryError により失敗したのはなぜですか)](#spark-application-failure-outofmemory)」を参照してください。
1. CURL などの REST クライアントを使用し、次のようなコマンド (実際の値は適切に変更します) で Spark アプリケーションを LIVY に送信します。

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

### <a name="further-reading"></a>参考資料:

[HDInsight クラスターでの Spark ジョブの送信](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-spark-submit-on-clusters"></a>クラスターで spark-submit を使用して Spark アプリケーションを構成するにはどうすればよいですか

### <a name="issue"></a>問題: 

spark-submit を使用した送信時に、Spark アプリケーションが HDInsight クラスター上で使用可能なメモリの量とコアの数を構成する必要があります。

1. Spark のどの構成をどのような値に設定する必要があるかを判断するには、「[Why did my Spark application fail with an OutOfMemoryError? (Spark アプリケーションが OutOfMemoryError により失敗したのはなぜですか)](#spark-application-failure-outofmemory)」を参照してください。
1. 次のようなコマンドを使用して spark-shell を起動します (実際の構成の値は適切に変更します)。 

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="further-reading"></a>参考資料:

[HDInsight クラスターでの Spark ジョブの送信](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)



## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>Spark アプリケーションの OutOfMemoryError 例外の原因は何ですか

### <a name="error"></a>エラー:

Spark アプリケーションが OutOfMemoryError 例外で失敗しました。

### <a name="detailed-description"></a>詳しい説明:

Spark アプリケーションが、次の種類のキャッチされない例外で失敗しました。  
```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>考えられる原因:

この例外の最も可能性が高い原因として考えられるのは、Spark アプリケーションの一部として実行子またはドライバーとして起動される Java 仮想マシン (JVM) に割り当てられたヒープ メモリの不足です。 

### <a name="resolution-steps"></a>解決手順:

1. Spark アプリケーションによって処理されるデータの最大サイズを決定します。 入力データ、入力データの変換によって生成される中間データ、および中間データの変換によって生成される出力データの各最大サイズに基づいてサイズを推測することができます。 またこれは、正式な最初の推測が可能でない場合、反復的なプロセスになる場合があります。 
1. 使用する HDInsight クラスターにメモリや、Spark アプリケーションに対応できるコアなどのリソースが十分にあることを確認してください。 この判断は、クラスターの YARN UI の [Cluster Metrics]\(クラスター メトリック\) セクションで、[Memory Used]\(使用中のメモリ\) の値と [Memory Total]\(メモリ合計\) の値、および [VCores Used]\(使用中の VCore\) の値と [VCores Total]\(VCore 合計\) の値を比較することで行うことができます。

1. 次の Spark の構成を、YARN で表示された利用可能なメモリおよびコア数の 90％ 以下となる値で、Spark アプリケーションのメモリ要件を十分に満たすように設定します。 

```apache
spark.executor.instances (Example: 8 for 8 executor count) 
spark.executor.memory (Example: 4g for 4 GB) 
spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
spark.executor.cores (Example: 2 for 2 cores per executor) 
spark.driver.memory (Example: 8g for 8GB) 
spark.driver.cores (Example: 4 for 4 cores)   
spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
```

すべての実行プログラムで使用されるメモリの合計は次のとおりです。 
```apache
spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
```
ドライバーによって使用されるメモリの合計は次のとおりです。 
```apache
spark.driver.memory + spark.yarn.driver.memoryOverhead
```

### <a name="further-reading"></a>参考資料:

- [Spark のメモリ管理の概要](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [HDInsight クラスターでの Spark アプリケーションのデバッグ](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)










