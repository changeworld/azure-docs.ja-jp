---
title: Azure Log Analytics による HBase の監視- Azure HDInsight | Microsoft Docs
description: Azure Log Analytics を使用して HDInsight HBase クラスターを監視します。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 12ec60049cdf267834d251c6c927b35e3c363a4e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165300"
---
# <a name="monitor-hbase-with-log-analytics"></a>Log Analytics による HBase の監視

HDInsight HBase Monitoring では、Azure Log Analytics を使用して、HDInsight のクラスター ノードから HDInsight HBase のパフォーマンス メトリックを収集します。 このモニターでは、HBase 固有の視覚化とダッシュボード、メトリック検索ツール、独自の監視ルールおよびアラートの作成機能が提供されます。 複数の Azure サブスクリプションにわたって複数の HDInsight HBase クラスターのメトリックを監視することができます。

Log Analytics は [Azure](../../operations-management-suite/operations-management-suite-overview.md) のサービスであり、クラウド環境とオンプレミス環境を監視して可用性とパフォーマンスを維持します。 Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。

[Log Analytics 管理ソリューション](../../log-analytics/log-analytics-add-solutions.md)を使用すると、Log Analytics に機能を追加して、追加のデータおよび分析ツールを提供できます。 Log Analytics 管理ソリューションには、特定の領域に関するメトリックを提供するロジックや視覚化、データ取得規則が集約されています。 また、収集対象のレコードの種類を新たに定義することもできます。これらのレコードは、ログ検索または新しいユーザー インターフェイス機能を使用して分析可能です。

[Insight & Analytics](https://azure.microsoft.com/pricing/details/insight-analytics/) は、Log Analytics プラットフォームを基盤としています。 Log Analytics 機能を使用してこのサービスに取り込まれたデータ (GB 単位) の分だけお支払いいただくか、または Insight & Analytics レベルに切り替えてこのサービスの管理対象となるノードの分だけお支払いいただくことができます。 Insight & Analytics では、Log Analytics で提供される機能の上位セットを利用できます。 HBase Monitoring ソリューションは、Log Analytics か Insight & Analytics で利用可能です。

HDInsight HBase Monitoring ソリューションをプロビジョニングするときは、Log Analytics ワークスペースを作成します。 それぞれのワークスペースが、独自のデータ リポジトリ、データ ソース、ソリューションを備えた一意の Log Analytics 環境となります。 サブスクリプションで複数のワークスペースを作成し、運用やテストなど複数の環境をサポートできます。

## <a name="provision-hdinsight-hbase-monitoring"></a>HDInsight HBase Monitoring のプロビジョニング

1. Azure サブスクリプションを使用して、[Azure Portal](https://portal.azure.com) にサインインします。
2. **[新規]** ウィンドウの **[Marketplace]** で、**[監視 + 管理]** を選択します。
3. **[監視 + 管理]** ウィンドウで、**[すべて表示]** を選択します。

    ![[監視 + 管理] ウィンドウ](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. 一覧から **[管理ソリューション]** のバンドを探します。 **[管理ソリューション]** の右側の **[さらに表示]** を選択します。

    ![[監視 + 管理] ウィンドウ](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. **[管理ソリューション]** ウィンドウで、ワークスペースに追加する HDInsight HBase Monitoring 管理ソリューションを選択します。

    ![[管理ソリューション] ウィンドウ](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. [管理ソリューション] ウィンドウで、管理ソリューションに関する情報を確認し、**[作成]** を選択します。 
7. "*管理ソリューション名*" のウィンドウで、管理ソリューションに関連付ける既存のワークスペースを選択するか、新しい Log Analytics ワークスペースを作成して選択します。
8. 必要に応じて、Azure サブスクリプション、リソース グループ、場所に関するワークスペースの設定を変更します。 
    ![ソリューションのワークスペース](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. **[作成]** を選択します。  
10. この新しい管理ソリューションをそのワークスペースで使用するには、**[Log Analytics]** > "***ワークスペース名***" > **[ソリューション]** の順に移動します。 管理ソリューションのエントリが一覧に表示されます。 エントリを選択してソリューションに移動します。

    ![Log Analytics のソリューション](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. HDInsight HBase Monitoring のソリューションのウィンドウが表示されます。

    ![HDInsight HBase ソリューションのウィンドウ](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. Log Analytics にデータを送信するための HDInsight HBase クラスターをまだ構成していないため、[概要] タイルにデータは表示されません。

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>Log Analytics への HDInsight HBase クラスターの接続

HDInsight HBase Monitoring のツールを使用するには、リージョン サーバー、ヘッド ノード、Zookeeper ノードから Log Analytics にメトリックを送信できるようにクラスターを構成する必要があります。 HDInsight HBase クラスターに対してスクリプト操作を実行し、構成を完了します。

### <a name="get-log-analytics-workspace-id-and-workspace-key"></a>Log Analytics ワークスペース ID とワークスペース キーの取得

クラスター内のノードを Log Analytics で認証するには、Log Analytics ワークスペース ID とワークスペース キーが必要です。 これらの値を取得するには、次の手順を実行します。

1. Azure Portal の HBase Monitoring ウィンドウから、[概要] を選択します。

    ![HBase Monitoring ソリューションのウィンドウ](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. [OMS ポータル] を選択し、新しいブラウザー タブまたはウィンドウで OMS ポータルを起動します。

    ![[OMS ポータル] の選択](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. OMS ポータルのホームで、[設定] を選択します。

    ![OMS ポータル](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. [接続されたソース]、[Linux サーバー] の順に選択します。

    ![[接続されたソース]、[Linux サーバー] の順に選択](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. スクリプト操作に必要となるため、表示されるワークスペース ID と主キーの値をメモします。

### <a name="run-the-script-action"></a>スクリプト操作の実行

HDInsight HBase クラスターからのデータ収集を有効にするには、クラスター内のすべてのノードに対してスクリプト操作を実行します。

1. Azure Portal で、使用する HDInsight HBase クラスターのウィンドウに移動します。
2. **[スクリプト操作]** を選択します。

    ![[スクリプト操作]](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. **[新規で送信]** を選択します。

    ![[新規で送信]](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. **[スクリプト操作を追加]** 内で、スクリプトの種類を `"- Custom"` に設定します。
5. そのスクリプトの名前を指定します。
6. **[バッシュ スクリプト URI]** に次の URI を貼り付けます。

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. **[ノードの種類]** で 3 種類すべてを選択します (**[ヘッド]**、**[リージョン]**、**[Zookeeper]**)。
8. **[パラメーター]** ボックスにワークスペース ID とワークスペース キーを入力します。それぞれの値は引用符で囲み、スペースで区切ります。

        "<Workspace ID>" "<Workspace Key>"

9. **[このスクリプト操作は保持され、クラスターに新しいノードが追加されると再実行されます。]** を選択します。

    ![スクリプト操作の設定](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. **[作成]** を選択します。
11. スクリプト操作の実行には数分かかります。 [スクリプト操作] ウィンドウで、その状況を監視できます。

    ![実行されているスクリプト操作](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. スクリプト操作が完了すると、一覧内のスクリプト名の横に緑のチェックマークが表示されます。

    ![完了したスクリプト操作](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>HDInsight HBase Monitoring ソリューションの確認

スクリプト操作が完了すると、数分で監視ソリューションにデータが表示されます。

1. Azure Portal で、使用する HDInsight HBase ソリューションのウィンドウに移動します。
2. **[概要]** タブを選択します。
3. **[概要]** では、監視されているリージョン サーバーの数を示すタイルが表示されます。

    ![監視の [概要] タイル](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. リージョン サーバーの数が表示されているタイルを選択します。 メイン ダッシュボードが表示されます。
5. このダッシュボードでは、リージョンの統計情報、使用中の先書きログ (WAL) の数、Log Analytics 検索のコレクション (リージョン サーバーのログ、Phoenix ログ、例外など)、関連メトリックを視覚化する一般的なグラフのコレクションにアクセスできます。 

    ![メイン ダッシュボード](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. これらのいずれかを選択すると、ログ検索のビューにドリルダウンします。クエリを絞り込んだり、より詳細なデータを得ることができます。

## <a name="next-steps"></a>次の手順

* [Log Analytics のアラートの作成](../../log-analytics/log-analytics-alerts-creating.md)
* [Azure Log Analytics におけるログ検索でのデータ収集](../../log-analytics/log-analytics-log-searches.md)
