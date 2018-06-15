---
title: Azure Monitor の使用
description: Azure Monitor を使用してリソースの動作を把握し、データに基づいてアクションを実行します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/25/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 70807db256f72b77bb29db3f6f59474a892f2939
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263363"
---
# <a name="get-started-with-azure-monitor"></a>Azure Monitor の使用
Azure Monitor は、Azure リソースを監視するための 1 つのソースを提供するプラットフォーム サービスです。 Azure Monitor を使用すると、Azure のリソースのメトリックとログに対して、視覚化、クエリ、ルーティング、アーカイブなどのアクションを実行できます。 このデータを操作するには、Azure Portal、[Monitor PowerShell コマンドレット](insights-powershell-samples.md)、[クロスプラットフォーム CLI](insights-cli-samples.md)、または [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx) を使用します。 この記事では、Azure Monitor の重要なコンポーネントをいくつか取り上げて、ポータルを使用しながら説明します。

## <a name="walkthrough"></a>チュートリアル
1. ポータルでは、**[すべてのサービス]** に移動し、**[Monitor]** オプションを見つけます。 星のアイコンをクリックして、このオプションをお気に入りリストに追加し、左側のナビゲーション バーから簡単にアクセスできるようにします。

    ![サービス一覧の Monitor](./media/monitoring-get-started/monitor-more-services.png)
2. **[Monitor]** オプションをクリックして、**[Monitor]** ページを開きます。 このページは、すべての監視設定とデータが 1 つにまとめられた統合ビューです。 最初に開くのは **[概要]** セクションです。 [概要] では、サブスクリプションのリソースに関連する監視アラート、エラー、サービスの正常性の勧告がすべて含まれるロールアップが表示されます。  

    ![Monitor のナビゲーション](./media/monitoring-get-started/monitor-blade-nav.png)

    Azure Monitor には、**アクティビティ ログ**、**メトリック**、**診断ログ**の 3 つの基本的なデータ監視カテゴリがあります。
3. **[アクティビティ ログ]** をクリックすると、アクティビティ ログ セクションが表示されます。

    [**アクティビティ ログ**](monitoring-overview-activity-logs.md)には、サブスクリプションのリソースに対して実行されたすべての操作が示されています。 アクティビティ ログを使用すると、サブスクリプションのリソースに対する作成、更新、または削除操作すべてについて、"いつ誰が何を" 行ったのかを確認できます。 たとえば、アクティビティ ログにより、いつ誰が Web アプリを停止したかがわかります。 アクティビティ ログ イベントがプラットフォームに保存され、クエリで使用できる期間は 90 日です。

    ![アクティビティ ログ](./media/monitoring-get-started/monitor-act-log-blade.png)

    一般的なフィルターに対するクエリを作成して保存し、最も重要なクエリをポータル ダッシュボードに固定すると、発生したイベントが条件を満たしているかどうかを常に把握できます。
4. 過去 1 週間の特定のリソース グループが表示されるようにビューをフィルター処理し、 **保存** ボタンをクリックします。 クエリに名前を付けます。

    ![アクティビティ ログ クエリの保存](./media/monitoring-get-started/monitor-act-log-save.png)
5. **固定** ボタンをクリックします。

    ![アクティビティ ログの [固定] をクリック](./media/monitoring-get-started/monitor-act-log-pin.png)

    このチュートリアルのビューのほとんどを、ダッシュボードに固定できます。 これは、サービスの運用データ情報に対して 1 つのソースを作成するうえで役立ちます。
6. ダッシュボードに戻ります。 クエリ (および結果数) がダッシュボードに表示されていることがわかります。 これは、最近サブスクリプションで実行されたアクションの中から、目立ったもの (新しいロールの割り当てや VM の削除など) をすばやく確認する必要がある場合に役立ちます。

    ![ダッシュボードにピン留めされたアクティビティ ログ](./media/monitoring-get-started/monitor-act-log-db.png)
7. **[Monitor]** タイルに戻り、**[メトリック]** をクリックします。 最初に、リソースを選択する必要があります。ページの上部にあるドロップダウン オプションを使用して、リソースをフィルター処理して選択します。

    ![メトリックのリソースをフィルター処理](./media/monitoring-get-started/monitor-met-filter.png)

    すべての Azure リソースによって[**メトリック**](monitoring-overview-metrics.md)が出力されます。 このビューでは、すべてのメトリックが 1 つのウィンドウで表示されるため、リソースがどのように実行されているかを簡単に把握できます。 **[メトリック (プレビュー)]** タブをクリックして、[最新のメトリック グラフ化エクスペリエンス](https://aka.ms/azuremonitor/new-metrics-charts)を確認することもできます。
8. リソースを選択すると、ページの左側に使用可能なすべてのメトリックが表示されます。 メトリックを選択して複数のメトリックを一度にグラフ化し、グラフの種類と時間の範囲を変更できます。 このリソースに設定されたすべてのメトリック アラートを表示することもできます。

    ![[メトリック] ブレード](./media/monitoring-get-started/monitor-metric-blade.png)

   > [!NOTE]
   > メトリックの中には、リソースで [[Application Insights]](../application-insights/app-insights-overview.md) や Windows または Linux の Azure 診断拡張機能を有効にしないと使用できないものがあります。
   >
   >

9. グラフに問題がない場合は、 **[固定]** ボタンを使用して、そのグラフをにダッシュボードに固定できます。
10. **[Monitor]** ページに戻り、**[診断ログ]** をクリックします。

    ![[診断ログ] ブレード](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**Azure 診断ログ**](monitoring-overview-of-diagnostic-logs.md)は、リソース*によって*出力されるログです。このログでは、その特定のリソースの操作に関するデータを確認できます。 たとえば、ネットワーク セキュリティ グループの規則の数とロジック アプリ ワークフロー ログの種類は両方とも診断ログです。 こうしたログは、ストレージ アカウントに格納したり、Event Hub にストリーミングしたり、[Log Analytics](../log-analytics/log-analytics-overview.md) に送信したりできます。 Log Analytics は、高度な検索およびアラート機能を備えた Microsoft のオペレーション インテリジェンス製品です。

    ポータルでは、サブスクリプションのリソースの一覧を表示およびフィルター処理して、診断ログが有効になっているかどうかを確認できます。
    > [!NOTE]
    > 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。
    >
    > *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。
    >
    >

11. 診断ログ ページのリソースをクリックします。 診断ログがストレージ アカウントに保存されている場合は、時間単位のログの一覧が表示されます。このログは直接ダウンロードできます。

    ![1 つのリソースの診断ログ](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    **[診断設定]** をクリックして、ストレージ アカウントへのアーカイブ、Event Hubs へのストリーミング、または Log Analytics ワークスペースへの送信を設定したり、こうした設定を変更したりすることもできます。

    ![Azure App Service の Web アプリの診断ログの有効化](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Log Analytics への診断ログの設定した場合は、ポータルの **[ログ検索]** セクションで、そのログを検索できます。
12. [Monitor] ページの **[アラート (クラシック)]** セクションに移動します。

    ![パブリックのアラート ブレード](./media/monitoring-get-started/monitor-alerts-nopp.png)

    ここで、Azure リソースの[**クラシック アラート**](monitoring-overview-alerts.md)をすべて管理できます。 これには、メトリック、アクティビティ ログのイベント、Application Insights Web テスト (場所)、および Application Insights プロアクティブ診断のアラートが含まれます。 アラートはアクション グループに接続されます。 [アクション グループ](monitoring-action-groups.md)を使用することで、アラートが発生したときにユーザーに通知したり、特定のアクションを実行したりできます。

13. **[Add metric alert (メトリック アラートの追加)]** をクリックして、アラートを作成します。

    ![[Add metric alert (メトリック アラートの追加)]](./media/monitoring-get-started/monitor-alerts-add.png)

    その後、アラートをダッシュボードに固定すると、いつでも簡単にその状態を確認できます。

    Azure Monitor では、毎分ごとの低頻度で評価できる[**新しいアラート**](https://aka.ms/azuremonitor/near-real-time-alerts)も使用できるようになりました。

14. [Monitor] セクションには、[Application Insights](../application-insights/app-insights-overview.md) アプリケーションと [Log Analytics](../log-analytics/log-analytics-overview.md) 管理ソリューションへのリンクも含まれます。 こうした他の Micorosoft 製品は Azure Monitor に緊密に統合されています。
15. Application Insights または Log Analytics を使用していない場合、Azure Monitor は、現在のパートナーの監視、ログ、およびアラート製品と連携している可能性があります。 パートナーの一覧と統合方法については、 [パートナー ページ](monitoring-partners.md) を参照してください。

ここで示した手順を実行し、関連するすべてのタイルをダッシュボードに固定すると、次のようなアプリケーションとインフラストラクチャの包括的なビューを作成できます。

![Azure Monitor ダッシュボード](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>次の手順
* Azure Monitor が監視ツールと連動するしくみを把握するには、[Azure のすべての監視ツールの概要](monitoring-overview.md)に関するページを参照してください。
