---
title: Azure Monitor の使用 | Microsoft Docs
description: Azure Monitor を使用してリソースの動作を把握し、データに基づいてアクションを実行します。
author: johnkemnetz
manager: rboucher
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: johnkem

---
# Azure Monitor の使用
Azure Monitor は、Azure リソースを監視するための 1 つのソースを提供する新しいプラットフォーム サービスです。Azure Monitor を使用すると、Azure リソースのメトリックとログを視覚化、クエリ、ルーティング、アーカイブし、そのメトリックとログに対してアクションを実行できます。このデータを操作するには、Monitor ポータル ブレード、Insights PowerShell コマンドレット、クロスプラットフォーム CLI、または Azure Insights REST API を使用します。この記事では、Azure Monitor の重要なコンポーネントをいくつか取り上げて説明します。

1. ポータルでは、**[More services (その他のサービス)]** に移動し、**[Monitor]** オプションを見つけます。星のアイコンをクリックして、このオプションをお気に入りリストに追加し、左側のナビゲーション バーから簡単にアクセスできるようにします。
   
    ![サービス一覧の Monitor](./media/monitoring-get-started/monitor-more-services.png)
2. **[Monitor]** をクリックして、**[Monitor]** ブレードを開きます。このブレードは、すべての監視設定とデータが 1 つにまとめられた統合ビューです。最初に開くのは **[アクティビティ ログ]** セクションです。
   
    ![[Monitor] ブレードのナビゲーション](./media/monitoring-get-started/monitor-blade-nav.png)
   
   > [!WARNING]
   > **[サービスの通知]** オプションと **[通知グループ]** オプションはプライベート プレビューです。このオプションは、プライベート プレビューに参加しているユーザーにのみ表示されます。
   > 
   > 
   
    Azure Monitor には、アクティビティ ログ、メトリック、診断ログの 3 つの基本的なデータ監視カテゴリがあります。
3. **[アクティビティ ログ]** をクリックすると、アクティビティ ログ セクションが表示されます。
   
    ![[アクティビティ ログ] ブレード](./media/monitoring-get-started/monitor-act-log-blade.png)
   
    **アクティビティ ログ** には、サブスクリプションのリソースに対して実行されたすべての操作が示されています。アクティビティ ログを使用すると、サブスクリプションのリソースに対する書き込み操作すべてについて、"いつ誰が何を" 行ったのかを確認できます。たとえば、アクティビティ ログにより、いつ誰が Web アプリを停止したかがわかります。アクティビティ ログ イベントがプラットフォームに保存される期間は 90 日です。
   
    一般的なフィルターに対するクエリを作成して保存し、最も重要なクエリをポータル ダッシュボードに固定すると、発生したイベントが条件を満たしているかどうかを常に把握できます。
4. 過去 1 週間の特定のリソース グループが表示されるようにビューをフィルター処理し、**保存**ボタンをクリックします。
   
    ![アクティビティ ログ クエリの保存](./media/monitoring-get-started/monitor-act-log-save.png)
5. **固定**ボタンをクリックします。
   
    ![アクティビティ ログの [固定] をクリック](./media/monitoring-get-started/monitor-act-log-pin.png)
   
    このチュートリアルのビューのほとんどを、ダッシュボードに固定できます。これは、サービスの運用データ情報に対して 1 つのソースを作成するうえで役立ちます。
6. ダッシュボードに戻ります。クエリ (および結果数) がダッシュボードに表示されていることがわかります。
   
    ![ダッシュボードに固定されたアクティビティ ログ](./media/monitoring-get-started/monitor-act-log-db.png)
7. **[Monitor]** タイルに戻り、**[メトリック]** をクリックします。最初に、セクションの上部にあるオプションでフィルター処理して選択し、リソースを選択する必要があります。
   
    ![メトリックのリソースをフィルター処理](./media/monitoring-get-started/monitor-met-filter.png)
   
    すべての Azure リソースによってメトリックが出力されます。このビューでは、そのすべてが 1 つのウィンドウで表示されます。
8. リソースを選択すると、ブレードの左側に使用可能なすべてのメトリックが表示されます。メトリックを選択して複数のメトリックを一度にグラフ化し、グラフの種類と時間の範囲を変更できます。このリソースに設定されたすべてのメトリック アラートを表示することもできます。
   
    ![[メトリック] ブレード](./media/monitoring-get-started/monitor-metric-blade.png)
   
   > [!NOTE]
   > メトリックの中には、リソースで [[Application Insights]](../application-insights/app-insights-overview.md) を有効にしないと使用できないものがあります。
   > 
   > 
9. グラフに問題がない場合は、**[固定]** ボタンを使用して、そのグラフをにダッシュボードに固定できます。
10. **[Monitor]** ブレードに戻り、**[診断ログ]** をクリックします。
    
    ![[診断ログ] ブレード](./media/monitoring-get-started/monitor-diaglogs-blade.png)
    
    Azure 診断ログは、リソースによって出力されるログです。このログでは、その特定のリソースの操作に関するデータを確認できます。たとえば、ネットワーク セキュリティ グループの規則の数とロジック アプリ ワークフロー ログの種類は両方とも診断ログです。こうしたログは、ストレージ アカウントへの格納や、Event Hub へのストリーミングが可能です。また、Microsoft のオペレーション インテリジェンス製品、[Log Analytics](../log-analytics/log-analytics-overview.md) に送信して、高度な検索およびアラート機能を利用できます。
    
    ポータルでは、サブスクリプションのリソースの一覧を表示およびフィルター処理して、診断ログが有効になっているかどうかを確認できます。
11. 診断ログ ブレードのリソースをクリックします。診断ログがストレージ アカウントに保存されている場合は、時間単位のログの一覧が表示されます。このログは直接ダウンロードできます。[診断を有効/無効にする] をクリックして、ストレージ アカウントへのアーカイブ、Event Hubs へのストリーミング、または Log Analytics ワークスペースへの送信を設定することもできます。
    
    ![1 つのリソースの診断ログ](./media/monitoring-get-started/monitor-diaglogs-detail.png)
    
    Log Analytics への診断ログの設定した場合は、ポータルの **[ログ検索]**セクションで、そのログを検索できます。
12. [Monitor] ブレードの **[アラート]** セクションに移動します。
    
    ![パブリックのアラート ブレード](./media/monitoring-get-started/monitor-alerts-nopp.png)
    
    ここで、Azure リソースのすべてのアラートを管理できます。これには、メトリック、アクティビティ ログのイベント (プレビュー)、Application Insights Web テスト (場所)、および Application Insights プロアクティブ診断のアラートが含まれます。アラートにより、電子メールの送信または webhook への投稿をトリガーできます。
13. **[Add metric alert (メトリック アラートの追加)]** をクリックして、アラートを作成します。
    
    ![メトリック アラートの追加](./media/monitoring-get-started/monitor-alerts-add.png)
    
    その後、アラートをダッシュボードに固定すると、いつでも簡単にその状態を確認できます。
14. [Monitor] セクションには、[Application Insights](../application-insights/app-insights-overview.md) アプリケーションと [Log Analytics](../log-analytics/log-analytics-overview.md) 管理ソリューションへのリンクも含まれます。こうした他の Micorosoft 製品は Azure Monitor に緊密に統合されています。
15. Application Insights または Log Analytics を使用していない場合、Azure Monitor は、現在のパートナーの監視、ログ、およびアラート製品と連携している可能性があります。パートナーの一覧と統合方法については、[パートナー ページ](../monitoring-and-diagnostics/monitoring-partners.md)を参照してください。

ここで示した手順を実行し、関連するすべてのタイルをダッシュボードに固定すると、次のようなアプリケーションとインフラストラクチャの包括的なビューを作成できます。

![Azure Monitor ダッシュボード](./media/monitoring-get-started/monitor-final-dash.png)

## 次のステップ
* [Azure Monitor の概要](../monitoring-and-diagnostics/monitoring-overview.md)を確認します

<!----HONumber=AcomDC_0928_2016-->