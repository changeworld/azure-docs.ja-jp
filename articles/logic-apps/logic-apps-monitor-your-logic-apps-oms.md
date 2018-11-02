---
title: Log Analytics でロジック アプリの実行を監視する - Azure Logic Apps | Microsoft Docs
description: トラブルシューティングと診断のために、Log Analytics を使用してロジック アプリの実行に関する洞察の取得とデータのデバッグを行います
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 177c361734a88acab5fc10d6b460645be82bf437
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457141"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Log Analytics を使用してロジック アプリの実行を監視して洞察を得る

監視と豊富なデバッグ情報の獲得のために、ロジック アプリの作成と同時に Log Analytics をオンにできます。 Log Analytics では、Azure Portal を通じて、ロジック アプリの実行を監視して診断ログを収集できます。 Logic Apps の管理ソリューションを追加すると、ロジック アプリの実行の状態の集計や、その状態、実行時間、再送信の状態、および相関関係 ID などの詳細情報を取得できます。

この記事では、Log Analytics をオンにして、ロジック アプリの実行のランタイム イベントやデータを確認できます。

 > [!TIP]
 > 既存のロジック アプリを監視するには、これらの手順に従って[診断ログの記録をオンにしてロジック アプリのランタイム データを Log Analytics に送信](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)します。

## <a name="requirements"></a>必要条件

開始する前に、Log Analytics ワークスペースを用意しておく必要があります。 [Log Analytics ワークスペースの作成方法](../log-analytics/log-analytics-quick-create-workspace.md)に関するページを参照してください。 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>ロジック アプリの作成時に診断ログの記録をオンにする

1. [Azure Portal](https://portal.azure.com) で、ロジック アプリを作成します。 **[リソースの作成]** > **[統合]** > **[ロジック アプリ]** を選択します。

   ![ロジック アプリを作成します](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. **[ロジック アプリの作成]** で、次に示すタスクを実行します。

   1. ロジック アプリに名前を付け、Azure サブスクリプションを選択します。 

   1. Azure リソース グループを作成するか選択します。

   1. **[Log Analytics]** を **[オン]** にします。 

   1. Log Analytics ワークスペースの一覧から、ロジック アプリの実行データを送信するワークスペースを選択します。 

      ![ロジック アプリを作成する](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      この手順を完了すると、Azure によりロジック アプリが作成されます。このアプリは、Log Analytics ワークスペースに関連付けられています。 
      また、この手順では、ワークスペースに Logic Apps 管理ソリューションが自動的にインストールされます。

   1. 操作が完了したら、**[作成]** を選択します。

1. ロジック アプリの実行を確認するには、[この手順を続行します](#view-logic-app-runs-oms)。

## <a name="install-the-logic-apps-management-solution"></a>Logic Apps 管理ソリューションをインストールする

ロジック アプリの作成時にすでに Log Analytics をオンにしている場合は、この手順をスキップします。 Logic Apps 管理ソリューションが既にインストールされています。

1. [Azure portal](https://portal.azure.com) で **[すべてのサービス]** を選択します。 検索ボックスにフィルターとして「log analytics」と入力し、**Log Analytics** を選択します。

   !["Log Analytics" を選択する](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. **[Log Analytics]** で、ご利用の Log Analytics ワークスペースを見つけて選択します。 

   ![Log Analytics ワークスペースを選択する](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. **[監視ソリューションの構成]** で **[ソリューションの表示]** を選択します。

   ![[ソリューションの表示] を選択する](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. [概要] ページで、**[追加]** を選択して、**[管理ソリューション]** の一覧を開きます。 その一覧から **[Logic Apps 管理]** を選択します。 

   ![[Logic Apps 管理] を選択する](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   ソリューションが見つからない場合は、ソリューションが表示されるまで一覧の下端にある **[さらに読み込む]** を選択します。

1. **[作成]** を選択すると、ソリューションがインストールされます。

   ![[Logic Apps Management]\(Logic Apps 管理\) の [追加] を選択する](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-runs-in-log-analytics-workspace"></a>Log Analytics ワークスペースでロジック アプリの実行を表示する

1. ロジック アプリの実行の数と状態を確認するには、Log Analytics ワークスペースに移動して、[概要] ページを開きます。 

   ロジック アプリの実行に関する詳細が、**[Logic Apps 管理]** タイルに表示されます。 ロジック アプリの実行に関する詳細が含まれる概要を確認するには、**[Logic Apps Management]\(Logic Apps 管理\)** タイルを選択します。 

   ![ロジック アプリの実行の数と状態を示す概要タイル](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   ここでは、ロジック アプリの実行は名前または実行状態でグループ化されます。 
   このページには、ロジック アプリの実行のアクションまたはトリガーで発生したエラーに関する詳細も表示されます。

   ![ロジック アプリの実行状態の概要](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. 特定のロジック アプリまたは状態のすべての実行を表示するには、ロジック アプリまたは状態の行を選択します。

   特定のロジック アプリのすべての実行を表示する例は次の通りです。

   ![あるロジック アプリまたは状態の実行を表示する](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   このページには、次のような高度なオプションがあります。

   * **[追跡対象プロパティ]**:

     この列には、ロジック アプリの追跡プロパティがアクション別にグループ化されて表示されます。 追跡対象プロパティを表示するには、**[表示]** を選択します。 
     追跡対象プロパティを検索するには、列フィルターを使用します。
   
     ![ロジック アプリの追跡対象プロパティを表示する](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     追跡対象プロパティを新たに追加した場合、最初に表示されるまでに 10 - 15 分かかる場合があります。 [ロジック アプリに追跡対象プロパティを追加する方法](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)をご覧ください。

   * **[再送信]** : 失敗、成功、または実行中の状態の、1 つ以上のロジック アプリの実行を再送信できます。 再送信する実行のチェックボックスをオンにして、**[再送信]** を選択します。 

     ![ロジック アプリの実行を再送信する](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. これらの結果をフィルター処理するには、クライアント側およびサーバー側の両方でフィルター処理を実行します。

   * **クライアント側のフィルター**: 各列で、必要なフィルターを選択します。

     ![列フィルターの例](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **サーバー側のフィルター**: 特定の時間枠を選択または表示される実行の数を制限するには、ページ上部のスコープ コントロールを使用します。 既定では、一度に 1000 件のレコードのみ表示されます。
   
     ![時間枠を変更する](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. 特定の実行のすべてのアクションとその詳細を表示するには、ロジック アプリの実行の行を選択します。

   特定のロジック アプリの実行のすべてアクションを表示する例は次の通りです。

   ![ロジック アプリの実行のアクションを表示する](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. 結果ページで、結果の背後にあるクエリを表示したり、すべての結果を表示したりするには、**[See All]\(すべて表示\)** を選択します。すると、[ログ検索] ページが開きます。
   
   ![結果ページの [See All]\(すべて表示\)](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   [ログ検索] ページで、

   * クエリの結果を表で確認するには、**[表]** を選択します。

   * クエリを変更するには、検索バーでクエリ文字列を編集します。 
   より高いエクスペリエンスを得るには、**[高度な分析]** を選択します。

     ![ロジック アプリの実行のアクションと詳細を表示する](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     [Azure Log Analytics] ページで、クエリを更新して表から結果を確認できます。 このクエリは [Kusto クエリ言語](https://aka.ms/LogAnalyticsLanguageReference)を使用します。他の結果を表示するには編集します。 

     ![Azure Log Analytics - クエリ ビュー](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>次の手順

* [B2B メッセージを監視する](../logic-apps/logic-apps-monitor-b2b-message.md)