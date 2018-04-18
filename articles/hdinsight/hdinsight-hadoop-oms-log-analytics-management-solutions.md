---
title: Azure Log Analytics に HDInsight クラスター管理ソリューションを追加する | Microsoft Docs
description: Azure Log Analytics を使って HDInsight クラスター用のカスタム ビューを作成する方法を説明します。
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: ab959d2cdaf840a1a88a71a0b8f29d84a8979330
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Log Analytics に HDInsight クラスター管理ソリューションを追加する

HDInsight では、Azure Log Analytics に追加できるクラスター固有の管理ソリューションが提供されます。 [管理ソリューション](../log-analytics/log-analytics-add-solutions.md)を使用すると、Log Analytics に機能を追加して、追加のデータおよび分析ツールを提供できます。 これらのソリューションは、HDInsight クラスターから重要なパフォーマンス メトリックを収集し、メトリックを検索するツールを提供します。 また、これらのソリューションは、HDInsight でサポートされるほとんどのクラスターの種類に対する視覚化とダッシュボードも提供します。 このソリューションで収集したメトリックを使用して、独自の監視ルールおよびアラートを作成できます。 

この記事では、クラスター固有の管理ソリューションを Log Analytics ワークスペースに追加する方法を説明します。

## <a name="prerequisites"></a>前提条件

* Azure Log Analytics を使用するように HDInsight クラスターを構成しておく必要があります。 手順については、「[Log Analytics を使用して Azure HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-tutorial.md)」をご覧ください。

## <a name="add-cluster-specific-management-solutions"></a>クラスター固有の管理ソリューションを追加する

このセクションでは、既存の Log Analytics ワークスペースに HBase クラスター管理ソリューションを追加します。

1. Azure Portal で HDInsight クラスターを開き、**[監視]**、**[OMS ダッシュボードを開く]** を順にクリックします。

    ![Operations Management Suite ダッシュボードを開く](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "OMS ダッシュボードを開く")

1. ダッシュボードで、**[ソリューション ギャラリー]** または左側のウィンドウの**ビュー デザイナー** アイコンをクリックします。

    ![Log Analytics に管理ソリューションを追加する](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Operations Management Suite に管理ソリューションを追加する")

2. ソリューション ギャラリーで、次のタイルのいずれかをクリックします。

    - HDInsight Hadoop Monitoring
    - HDInsight HBase Monitoring (プレビュー)
    - HDInsight Kafka Monitoring
    - HDInsight Storm Monitoring
    - HDInsight Spark Monitoring

3. 次の画面で、**[追加]** をクリックします。  次のスクリーンショットは、HBase の監視の [追加] ボタンを示しています。

     ![HBase 管理ソリューションを追加する](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "HBase 管理ソリューションを追加する")

4. ダッシュボードに HBase 管理ソリューションのタイルを表示することができます。 (この記事の前提条件の一部として) Operations Management Suite と関連付けたクラスターが HBase クラスターである場合、タイルにはクラスターの名前とクラスターのノード数が表示されます。

    ![追加された HBase 管理ソリューション](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "追加された HBase 管理ソリューション")

## <a name="next-steps"></a>次の手順

* [Azure Log Analytics でクエリを実行して Azure HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>関連項目

* [Log Analytics の使用](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Log Analytics でのアラート ルールの作成](../log-analytics/log-analytics-alerts-creating.md)
