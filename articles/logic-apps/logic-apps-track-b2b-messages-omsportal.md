---
title: "OMS ポータルで B2B メッセージを追跡する | Microsoft Docs"
description: "OMS ポータルで B2B メッセージを追跡するには"
author: padmavc
manager: anneta
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
ms.sourcegitcommit: 53195091ac4b93ed94f432990c84c407615fc03e
ms.openlocfilehash: 9c3855c7fce5a9f38424f0bb6cd03f7a2c8d36be


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>OMS ポータルでの B2B メッセージの追跡
B2B 通信では、実行中の&2; つのビジネス プロセスまたはアプリケーション間でメッセージ交換が行われます。 OMS ポータルで B2B メッセージを追跡すると、メッセージが正しく処理されたかどうかを確認できる、Web ベースの豊富な追跡機能を利用できます。  以下の追跡を行うことができます。

* メッセージの数と状態
* 受信確認の状態
* メッセージと受信確認の関連付け
* エラーの詳細な説明
* 検索機能

## <a name="prerequisites"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 統合アカウント。[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を作成し、ログ記録を有効にすることができます。手順については、[こちら](logic-apps-monitor-b2b-message.md)を参照してください。
* ロジック アプリ。[ロジック アプリ](../logic-apps/logic-apps-create-a-logic-app.md)を作成し、ログ記録を有効にすることができます。手順については、[こちら](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)を参照してください。

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>OMS ポータルへの Logic Apps B2B ソリューションの追加

1. ポータルで **[その他のサービス]** を選択し、**Log Analytics** を検索して、**[Log Analytics]** を選択します   
![Log Analytics の検索](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. **[Log Analytics]** を選択します  
![Log Analytics の選択](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. **[OMS ポータル]** を選択し、OMS ポータルのホーム ページを開きます   
![OMS ポータルの参照](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. **[ソリューション ギャラリー]** を選択します。    
![[ソリューション ギャラリー]](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png) を選択します。

5. **[Logic Apps B2B]** を選択します     
![[Logic Apps B2B] の選択](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. **[追加]** をクリックして、**[Logic Apps B2B メッセージ]** をホーム ページに追加します  
![[追加] の選択](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. ホーム ページを参照して、**[Logic Apps B2B メッセージ]** を確認します   
![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="tracking-data-in-oms-portal"></a>OMS ポータルでのデータの追跡

1. メッセージの投稿が処理されると、ホーム ページでメッセージの数が更新されます   
![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. ホーム ページで **[Logic Apps B2B メッセージ]** を選択すると、AS2 および X12 のメッセージの状態が表示されます。  データは、直前の&1; 日のものです。
![[Logic Apps B2B メッセージ] の選択](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)



3. 状態別の AS2 または X12 メッセージを選択すると、メッセージの一覧が表示されます   
![AS2 メッセージの状態の選択](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

| プロパティ | 説明 |
| --- | --- |
| 送信者 | 受信設定で構成されているゲスト パートナーまたは AS2 契約の送信設定で構成されているホスト パートナー |
| 受信者 | 受信設定で構成されているホスト パートナーまたは AS2 契約の送信設定で構成されているゲスト パートナー |
| ロジック アプリ | AS2 アクションが構成されているロジック アプリ |
| 状態 | AS2 メッセージの状態。 成功 = 適切な AS2 メッセージ送受信済み、MDN 未構成。成功 = 適切な AS2 メッセージ送受信済み、MDN 構成済み、MDN 送受信済み。失敗 = 無効な AS2 メッセージ受信済み、MDN 未構成。保留中 = 適切な AS2 メッセージ送受信済み、MDN 構成済み、機能確認要。 |
| Ack | MDN メッセージの状態 |
| 方向 | AS2 メッセージの方向 |
| 関連付け ID | ロジック アプリ内ですべてのトリガーとアクションをアクションを関連付けるための ID |
| メッセージ ID |  AS2 メッセージ ID (AS2 メッセージのヘッダーから取得) |
| Timestamp | AS2 アクションがメッセージを処理した時刻 |
|  |  |


![X12 メッセージの状態の選択](media/logic-apps-track-b2b-messages-omsportal/x12messagelist.png)

| プロパティ | Description |
| --- | --- |
| 送信者 | 受信設定で構成されているゲスト パートナーまたは AS2 契約の送信設定で構成されているホスト パートナー |
| 受信者 | 受信設定で構成されているホスト パートナーまたは AS2 契約の送信設定で構成されているゲスト パートナー |
| ロジック アプリ | AS2 アクションが構成されているロジック アプリ |
| 状態 | X12 メッセージの状態。 成功 = 適切な X12 メッセージ送受信済み、機能確認未構成。成功 = 適切な X12 メッセージ送受信済み、機能確認構成済み、機能確認送受信済み。失敗 = 無効な X12 メッセージ送受信済み。保留中 = 適切な X12 メッセージ送受信済み、機能確認構成済み、機能確認要 |
| Ack | 機能確認 (997) の状態。  承認済み = 肯定の機能確認送受信済み。拒否 = 否定の機能確認送受信済み。保留中 = 機能確認要、未受信。保留中 = 機能確認生成済み、パートナーに未送信 |
| 方向 | X12 メッセージの方向 |
| 関連付け ID | ロジック アプリ内ですべてのトリガーとアクションをアクションを関連付けるための ID |
| メッセージの種類 |  EDI X12 メッセージの種類 |
| ICN | X12 メッセージのインターチェンジ制御番号 |
| TSCN | X12 メッセージのトランザクション セット制御番号 |
| Timestamp | X12 アクションがメッセージを処理した時刻 |
| | |

4. AS2 または X12 メッセージの一覧で行を選択すると、ログの検索画面が表示されます。  ログ検索では、同じ**実行 ID** を持つすべてのアクションが一覧表示されます。
![メッセージの状態の選択](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="queries-in-oms-portal"></a>OMS ポータルでのクエリ

検索クエリは [検索] ページで作成できます。検索結果は、ファセット コントロールを使用してフィルタリングすることができます。

### <a name="how-to-create-a-query"></a>クエリの作成方法

1. ログ検索で、クエリを記述して **[保存]** を選択します。  クエリを記述する手順については[こちら](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)を参照してください。![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. **[検索条件を保存]** が開きます。  **[名前]**、**[カテゴリ]** を指定して **[保存]** をクリックします。   
![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. クエリを表示するには、**[お気に入り]** を選択します。    
![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="how-to-use-a-saved-query"></a>保存されたクエリを使用する方法

* ログ検索で、**[お気に入り]** を選択し、保存されたクエリを表示します。  いずれかのクエリを選択するとクエリの結果が表示されます。![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>次のステップ
[カスタム追跡スキーマ](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[AS2 の追跡スキーマ](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[X12 の追跡スキーマ](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Jan17_HO4-->


