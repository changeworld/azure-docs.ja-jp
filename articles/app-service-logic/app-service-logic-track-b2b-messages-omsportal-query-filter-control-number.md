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
ms.sourcegitcommit: 6e56dae0ab30612a7794bd39e6eba2f84bcecc19
ms.openlocfilehash: eae37a357e3e1c00310c7c638d3164118403ee9f


---
# <a name="create-a-query-in-oms-portal"></a>OMS ポータルでクエリを作成する 
特定のインターチェンジ制御番号のデータをフィルターするクエリを作成する手順

## <a name="prerequisites"></a>前提条件

高度な詳細情報とデバッグのために、[統合アカウント](app-service-logic-monitor-b2b-message.md)と、X12 コネクタを持つ [Logic Apps](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) の診断を有効にします。  [ここ](app-service-logic-track-b2b-messages-omsportal.md)の手順に従って、OMS ポータルに診断データを発行します。

## <a name="create-a-query-to-search-data-for-a-specific-interchange-control-number"></a>特定のインターチェンジ制御番号のデータを検索するクエリを作成する

1. ホーム ページで **[ログ検索]** を選択します。  
![ログ検索の選択](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. 検索ウィンドウに「**Type="AzureDiagnostics"**」と入力し、すべてのデータを取得します。  **[追加]** をクリックしてデータをフィルターします。  
![クエリの選択](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. 「**interchange**」と入力して、**event_record_messageProperties_interchangeControlNumber_s** を選択し、**[追加]** をクリックします。  
![制御番号の選択](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. データをフィルターする制御番号を選択し、**[適用]** をクリックします。  
![制御番号の選択](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. 選択した制御番号のデータをフィルターするために作成したクエリを検索できます。   
![制御番号の選択](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. クエリに名前を指定し、保存します。   
![制御番号の選択](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png) 

7. **[お気に入り]** を選択してクエリを表示し、今後の検索に使用します。  
![制御番号の選択](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. 新しいインターチェンジ制御番号を検索するようにクエリを更新することができます。  
![制御番号の選択](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png) 


## <a name="next-steps"></a>次のステップ
[カスタム追跡スキーマ](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[AS2 の追跡スキーマ](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[X12 の追跡スキーマ](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[Enterprise Integration Pack についての詳細情報](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


