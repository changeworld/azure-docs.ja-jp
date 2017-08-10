---
title: "YARN のトラブルシューティング - Azure HDInsight | Microsoft Docs"
description: "YARN のよくあるご質問を使って、Azure HDInsight プラットフォームの YARN に関する一般的な質問に回答します。"
keywords: "Azure HDInsight, Yarn, FAQ, トラブルシューティング ガイド, よくあるご質問"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 576d6bfe9697d2abd6f84824960dad62e9ed3c11
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="yarn-troubleshooting"></a>YARN のトラブルシューティング

この記事では、Apache Ambari での YARN ペイロードの操作に関連する主な問題とその解決策について説明します。

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>クラスターで新しい Yarn キューを作成する方法

### <a name="issue"></a>問題:

HDInsight クラスターでの容量の割り当てで新しい Yarn キューを作成する。  

### <a name="resolution-steps"></a>解決手順: 

新しい Yarn キューを作成し、すべてのキューの間で容量の割り当てのバランスを取るには、Ambari で次の手順を実行します。 

この例では、既存の 2 つのキュー (default と thriftsvr) の容量をどちらも 50% から 25% に変更し、新しいキュー (Spark) に 50% の容量を使用できるようにします。
| キュー | 容量 | 最大 容量 |
| --- | --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |
1. Abari ビュー アイコン (グリッド パターン) クリックし、**[Yarn Queue Manager]** を選びます。

    ![Ambari ビュー アイコンをクリックする](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
1. **default** キューを選びます。

    ![default キューを選ぶ](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
1. **default** キューの **[Capacity]\(容量\)** を 50% から 25% に変更し、**thriftsvr** キューについても 25% に変更します。

    ![default キューと thriftsvr キューの容量を 25% に変更する](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
1. **[Add Queue]\(キューの追加\)** をクリックして、新しいキューを作成します。

    ![[Add Queue]\(キューの追加\) をクリックする](media/hdinsight-troubleshoot-yarn/create-queue-4.png)
1. 新しいキューの名前を指定します。

    ![キューの名前を Spark にする](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  
1. **[Capacity]\(容量\)** の値は 50% のままにして、**[Actions]\(アクション\)** ボタンをクリックします。

    ![[Actions]\(アクション\) ボタンをクリックする](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
1. **[Save and Refresh Queues]\(キューの保存と更新\)** を選びます。

    ![[Save and Refresh Queues]\(キューの保存と更新\) を選ぶ](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

これらの変更は Yarn Scheduler UI にすぐに表示されます。

### <a name="further-reading"></a>参考資料:

- [Yarn Capacity Scheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>クラスターから Yarn ログをダウンロードする方法

#### <a name="issue"></a>問題:

Yarn のアプリケーション マスター ログと他のコンテナー ログを HDInsight クラスターからダウンロードする必要がある。  

#### <a name="resolution-steps"></a>解決手順: 

1. Secure Shell (SSH) クライアントで HDInsight クラスターに接続します (以下の「参考資料」セクションを参照)。
1. 現在実行しているすべての Yarn アプリケーションのアプリケーション ID を、次のコマンドで一覧表示します。

```apache
yarn top
```
ID は [`APPLICATIONID`] 列に一覧表示され、そのログをダウンロードします。

```apache
YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                  APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
 application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
 application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
```

すべてのアプリケーション マスターの Yarn コンテナー ログをダウンロードするには、次のコマンドを使います。
   
```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
```

これにより、`amlogs.txt` という名前のテキスト形式のログ ファイルが作成されます。 

最新のアプリケーション マスターだけの Yarn コンテナー ログをダウンロードするには、次のコマンドを使います。

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
```

これにより、`latestamlogs.txt` という名前のテキスト形式のログ ファイルが作成されます。 

最初の 2 つのアプリケーション マスターの Yarn コンテナー ログをダウンロードするには、次のコマンドを使います。

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
```

これにより、`first2amlogs.txt` という名前のテキスト形式のログ ファイルが作成されます。 

すべての Yarn コンテナー ログをダウンロードするには、次のコマンドを使います。

```apache
yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
```

これにより、`logs.txt` という名前のテキスト形式のログ ファイルが作成されます。 

特定のコンテナーの Yarn コンテナー ログをダウンロードするには、次のコマンドを使います。

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
```

これにより、`containerlogs.txt` という名前のテキスト形式のログ ファイルが作成されます。

#### <a name="further-readings"></a>参考資料:

- [SSH を使用して HDInsight クラスターに接続する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

- [Apache Hadoop Yarn の概念とアプリケーション](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)









