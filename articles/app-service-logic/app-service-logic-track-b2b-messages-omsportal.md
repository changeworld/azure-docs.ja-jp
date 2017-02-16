---
title: "OMS ポータルで B2B メッセージを追跡する | Microsoft Docs"
description: "OMS ポータルで B2B メッセージを追跡するには"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 7f64fe625b7c3b7e58f86154b9d601eff03e31d3
ms.openlocfilehash: 854db453a3af459c0398c4b88a6789c87747e920


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>OMS ポータルでの B2B メッセージの追跡
B2B 通信では、実行中の 2 つのビジネス プロセスまたはアプリケーション間でメッセージ交換が行われます。 OMS ポータルで B2B メッセージを追跡すると、メッセージが正しく処理されたかどうかを確認できる、Web ベースの豊富な追跡機能を利用できます。  以下の追跡を行うことができます。

* メッセージの数と状態
* 受信確認の状態
* メッセージと受信確認の関連付け
* エラーの詳細な説明
* 検索機能

## <a name="prerequisites"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 統合アカウント。[統合アカウント](app-service-logic-enterprise-integration-create-integration-account.md)を作成し、ログ記録を有効にすることができます。手順については、[こちら](app-service-logic-monitor-b2b-message.md)を参照してください。
* ロジック アプリ。[ロジック アプリ](app-service-logic-create-a-logic-app.md)を作成し、ログ記録を有効にすることができます。手順については、[こちら](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)を参照してください。

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>OMS ポータルへの Logic Apps B2B ソリューションの追加

1. ポータルで **[その他のサービス]** を選択し、**log analytics** を検索して、**[Log Analytics]** を選択します。
![Log Analytics の検索](./media/app-service-logic-track-b2b-messages-omsportal/browseloganalytics.png)  

2. **Log Analytics** を選択します。
![Log Analytics の選択](./media/app-service-logic-track-b2b-messages-omsportal/selectla.png)

3. **[OMS ポータル]** を選択し、OMS ポータルのホーム ページを開きます。![OMS ポータルの参照](./media/app-service-logic-track-b2b-messages-omsportal/omsportalpage.png)

4. **[ソリューション ギャラリー]** を選択します。    
![[ソリューション ギャラリー]](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage1.png) を選択します。

5. **[Logic Apps B2B]** を選択します。
![Logic Apps B2B の選択](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage2.png)

6. **[追加]** をクリックして、**[Logic Apps B2B メッセージ]** をホーム ページに追加します。![[追加] の選択](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage3.png)

7. ホーム ページを参照して、**[Logic Apps B2B メッセージ]** を確認します。
![ホーム ページの選択](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="tracking-data-in-oms-portal"></a>OMS ポータルでのデータの追跡

1. メッセージの投稿が処理されると、ホーム ページでメッセージの数が更新されます。![ホーム ページの選択](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage6.png)

2. ホーム ページで **[Logic Apps B2B メッセージ]** を選択すると、AS2 および X12 のメッセージの状態が表示されます。  データは、直前の 1 日のものです。
![[Logic Apps B2B メッセージ] の選択](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage5.png)

3. 状態別の AS2 または X12 メッセージを選択すると、メッセージの一覧が表示されます。![AS2 メッセージの状態の選択](./media/app-service-logic-track-b2b-messages-omsportal/as2messagelist.png)

    ![X12 メッセージの状態の選択](./media/app-service-logic-track-b2b-messages-omsportal/x12messagelist.png)

4. AS2 または X12 メッセージの一覧で行を選択すると、ログの検索画面が表示されます。  ログ検索では、同じ**実行 ID** を持つすべてのアクションが一覧表示されます。
![メッセージの状態の選択](./media/app-service-logic-track-b2b-messages-omsportal/logsearch.png)

## <a name="queries-in-oms-portal"></a>OMS ポータルでのクエリ

検索クエリは [検索] ページで作成できます。検索結果は、ファセット コントロールを使用してフィルタリングすることができます。

### <a name="how-to-create-a-query"></a>クエリの作成方法

1. ログ検索で、クエリを記述して **[保存]** を選択します。  クエリを記述する手順については[こちら](app-service-logic-track-b2b-messages-omsportal-query-filter-control-number.md)を参照してください。![ホーム ページの選択](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery.png)

2. **[検索条件を保存]** が開きます。  **[名前]**、**[カテゴリ]** を指定して **[保存]** をクリックします。   
![ホーム ページの選択](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. クエリを表示するには、**[お気に入り]** を選択します。    
![ホーム ページの選択](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![ホーム ページの選択](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="how-to-use-a-saved-query"></a>保存されたクエリを使用する方法

1. ログ検索で、**[お気に入り]** を選択し、保存されたクエリを表示します。  いずれかのクエリを選択するとクエリの結果が表示されます。![ホーム ページの選択](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>次のステップ
[カスタム追跡スキーマ](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[AS2 の追跡スキーマ](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[X12 の追跡スキーマ](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[Enterprise Integration Pack についての詳細情報](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


