---
title: "Azure Log Analytics でクエリを実行して Azure HDInsight クラスターを監視する | Microsoft Docs"
description: "Azure Log Analytics でクエリを実行し、HDInsight クラスターで実行されているジョブを監視する方法を説明します。"
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
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: da7b83846418bfe5f95b126d4f5f7b34d3a7b35d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Azure Log Analytics でクエリを実行して HDInsight クラスターを監視する

Azure Log Analytics を使用して Azure HDInsight クラスターを監視する基本的なシナリオを説明します。

* [HDInsight クラスターのメトリックを分析する](#analyze-hdinsight-cluster-metrics)
* [特定のログ メッセージの検索する](#search-for-specific-log-messages)
* [イベント アラートを作成する](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>前提条件

* Azure Log Analytics を使用するように HDInsight クラスターを構成しておく必要があります。 手順については、「[Log Analytics を使用して Azure HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-tutorial.md)」をご覧ください。

* 「[Log Analytics に HDInsight クラスター管理ソリューションを追加する](hdinsight-hadoop-oms-log-analytics-management-solutions.md)」の手順に従って、HDInsight クラスター固有の管理ソリューションを [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) ワークスペースに追加しておく必要があります。

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight クラスターのメトリックを分析する

HDInsight クラスターの特定のメトリックを検索する方法を説明します。

1. Azure Portal で Azure Log Analytics に関連付けた HDInsight クラスターを開きます。
2. **[監視]** をクリックして、**[OMS ダッシュボードを開く]** をクリックします。

    ![OMS ダッシュボードを開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS ダッシュボードを開く")

2. 左側のメニューにある **[ログ検索]** をクリックします。

    ![ログ検索を開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "ログ検索を開く")

3. 検索ボックスに、Azure Log Analytics を使用するように構成したすべての HDInsight クラスターで使用できるすべてのメトリックを検索する次のクエリを入力して、**Enter** キーを押します。

        `search *` 

    ![すべてのメトリックを検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "すべてのメトリックを検索")

    出力は次のようになります。

    ![すべてのメトリックの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "すべてのメトリックの検索結果")

5. 左側のウィンドウの **[種類]** の下で、詳しく調べたいメトリックを選択して **[適用]** をクリックします。 次のスクリーンショットは `metrics_resourcemanager_queue_root_default_CL` 種類を選択した場合を示しています。 

    > [!NOTE]
    > 目的のメトリックが表示されていない場合は、**[[+]More]\([+] その他\)** ボタンをクリックします。 また、**[適用]** ボタンはリストの一番下にあるので、このボタンを表示するには下までスクロールする必要があります。
    > 
    >    

    テキスト ボックスのクエリが、下記のスクリーンショットの赤枠で示された内容に変わっていることを確認してください。

    ![特定のメトリックの検索](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "特定のメトリックの検索")

6. この特定のメトリックのデータを詳しく調べるには: たとえば、次のクエリを使用すると、クラスター名ごとに、10 分間に使用されたリソースの平均値に基づいて出力結果を調整することができます。

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. 使用されたリソースの平均値に基づいて結果を調整する代わりに、次のクエリを使用すると、10 分間で最大 (および 90 パーセンタイルと 95 パーセンタイル) のリソースが使用された時刻に基づいて結果を調整できます。

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>特定のログ メッセージを検索する

特定の時間枠中のエラー メッセージを検索する方法を説明します。 ここで説明する手順は、目的のエラー メッセージを探すためのひとつの方法にすぎません。 使用可能な任意のプロパティを使用して、目的のエラーを探すことができます。

1. Azure Portal で Azure Log Analytics に関連付けた HDInsight クラスターを開きます。
2. **[監視]** をクリックして、**[OMS ダッシュボードを開く]** をクリックします。

    ![OMS ダッシュボードを開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS ダッシュボードを開く")

2. OMS ダッシュボードのホーム画面で **[ログ検索]** をクリックします。

    ![ログ検索を開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "ログ検索を開く")

3. Azure Log Analytics を使用するように構成したすべての HDInsight クラスターのすべてのエラー メッセージを検索する次のクエリを入力して、**Enter** キーを押します。 

         search "Error"

    出力は次のようになります。

    ![すべてのエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "すべてのエラーの検索結果")

5. 左側のウィンドウの **[種類]** カテゴリの下で、詳しく調べたいエラーの種類を選択して、**[適用]** をクリックします。  選択した種類のエラーのみが表示されるように結果が調整されたことを確認してください。
7. 左側のウィンドウにあるオプションを使って、このエラーの一覧を詳しく調べることができます。 たとえば、次のように入力します。 

    - 特定のワーカー ノードからのエラー メッセージを表示するには:

        ![特定のエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "特定のエラーの検索結果")

    - 特定の時刻に発生したエラーを表示するには:

        ![特定のエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "特定のエラーの検索結果")

9. 特定のエラーを表示するには: **[[+]show more]\([+] もっと見る\)** をクリックすると、実際のエラー メッセージを確認できます。

    ![特定のエラーの検索結果](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "特定のエラーの検索結果")

## <a name="create-alerts-for-tracking-events"></a>イベント追跡用のアラートを作成する

アラートを作成する最初の手順は、アラートをトリガーする基準となるクエリを作成することです。 わかりやすくするため、ここでは HDInsight クラスターで実行が失敗したアプリケーションの一覧を返す次のクエリを使用します。

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

アラートの作成には任意のクエリを使用できます。

1. Azure Portal で Azure Log Analytics に関連付けた HDInsight クラスターを開きます。
2. **[監視]** をクリックして、**[OMS ダッシュボードを開く]** をクリックします。

    ![OMS ダッシュボードを開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS ダッシュボードを開く")

2. OMS ダッシュボードのホーム画面で **[ログ検索]** をクリックします。

    ![ログ検索を開く](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "ログ検索を開く")

3. アラートを作成する次のクエリを実行して、**Enter** キーを押します。

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. ページの上部にある **[アラート]** をクリックします。

    ![クエリを入力してアラートを作成する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "クエリを入力してアラートを作成する")

4. **[アラート ルールの追加]** ウィンドウで、クエリとその他の情報を入力してアラートを作成し、**[保存]** をクリックします。

    ![クエリを入力してアラートを作成する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "クエリを入力してアラートを作成する")

    スクリーン ショットは、アラートのクエリが出力を返したときに電子メールによる通知を送信するための構成を示しています。

5. 既存のアラートを編集または削除することができます。 そのためには、OMS ポータルの任意のページで **[設定]** アイコンをクリックします。

    ![クエリを入力してアラートを作成する](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "クエリを入力してアラートを作成する")

6. **[設定]** ページで **[アラート]** をクリックし、作成したアラートを確認します。 アラートの有効/無効を切り替えたり、アラートを編集、削除したりすることもできます。 詳細については、「[Log Analytics のアラート ルールの操作](../log-analytics/log-analytics-alerts-creating.md)」を参照してください。

## <a name="see-also"></a>関連項目

* [Working with Operations Management Suite Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/) (Operations Management Suite Log Analytics の操作)
* [Log Analytics でのアラート ルールの作成](../log-analytics/log-analytics-alerts-creating.md)
