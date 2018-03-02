---
title: "Azure Log Analytics に HDInsight クラスター管理ソリューションを追加する | Microsoft Docs"
description: "Azure Log Analytics を使って HDInsight クラスター用のカスタム ビューを作成する方法を説明します。"
services: hdinsight
documentationcenter: 
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
ms.openlocfilehash: f8a073fbf40feaef97053bb1165d63bbca3e3feb
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2018
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Log Analytics に HDInsight クラスター管理ソリューションを追加する

HDInsight では、Azure Log Analytics に追加できるクラスター固有の管理ソリューションが提供されます。 [管理ソリューション](../log-analytics/log-analytics-add-solutions.md)を使用すると、[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) に機能を追加して、Log Analytics に追加のデータおよび分析ツールを提供できます。 これらのソリューションは、HDInsight クラスターから重要なパフォーマンス メトリックを収集し、メトリックを検索するツールを提供します。 また、これらのソリューションは、HDInsight でサポートされるほとんどのクラスターの種類に対する視覚化とダッシュボードも提供します。 このソリューションで収集したメトリックを使用して、独自の監視ルールおよびアラートを作成できます。 

この記事では、クラスター固有の管理ソリューションを Operations Management Suite ワークスペースに追加する方法を説明します。

## <a name="prerequisites"></a>前提条件

* Azure Log Analytics を使用するように HDInsight クラスターを構成しておく必要があります。 手順については、「[Log Analytics を使用して Azure HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-tutorial.md)」をご覧ください。

## <a name="add-cluster-specific-management-solutions"></a>クラスター固有の管理ソリューションを追加する

このセクションでは、既存の Operations Management Suite ワークスペースに HBase クラスター管理ソリューションを追加します。

1. Azure Portal で HDInsight クラスターを開き、**[監視]**、**[OMS ダッシュボードを開く]** を順にクリックします。

    ![Operations Management Suite ダッシュボードを開く](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "OMS ダッシュボードを開く")

1. OMS ダッシュボードで、**[ソリューション ギャラリー]** または左側のウィンドウの**ビュー デザイナー** アイコンをクリックします。

    ![Operations Management Suite に管理ソリューションを追加する](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Operations Management Suite に管理ソリューションを追加する")

2. ソリューション ギャラリーで、次のタイルのいずれかをクリックします。

    - HDInsight Hadoop Monitoring
    - HDInsight HBase Monitoring (プレビュー)
    - HDInsight Kafka Monitoring
    - HDInsight Storm Monitoring
    - HDInsight Spark Monitoring

3. 次の画面で、**[追加]** をクリックします。  次のスクリーンショットは、HBase の監視の [追加] ボタンを示しています。

     ![HBase 管理ソリューションを追加する](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "HBase 管理ソリューションを追加する")

4. OMS ダッシュボードに HBase 管理ソリューションのタイルを表示することができます。 (この記事の前提条件の一部として) Operations Management Suite と関連付けたクラスターが HBase クラスターである場合、タイルにはクラスターの名前とクラスターのノード数が表示されます。

    ![追加された HBase 管理ソリューション](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "追加された HBase 管理ソリューション")

## <a name="next-steps"></a>次の手順

* [Azure Log Analytics でクエリを実行して Azure HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>関連項目

* [Operations Management Suite Log Analytics の操作](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Log Analytics でのアラート ルールの作成](../log-analytics/log-analytics-alerts-creating.md)
