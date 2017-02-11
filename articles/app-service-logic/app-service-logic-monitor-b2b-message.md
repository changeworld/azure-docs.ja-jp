---
title: "B2B メッセージを監視する | Microsoft Docs"
description: "統合アカウントを監視する方法"
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
ms.date: 10/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 8ce3585a557f6b2c9fa4a4c85142cdce9357a90b


---
# <a name="monitor-b2b-messages"></a>B2B メッセージを監視する
B2B 通信では、実行中の 2 つのビジネス プロセスまたはアプリケーション間でメッセージ交換が行われます。 リレーションシップは、ビジネス プロセスの間の契約を定義するものです。 通信が確立されると、期待どおりに通信が行われているかどうかを監視する手段が必要になります。  B2B プロトコルの AS2、X12、および EDIFACT 用に、メッセージ追跡が実装されています。  統合アカウントを構成して、高度な詳細情報とデバッグのために診断を使用することができます。

## <a name="prerequisites"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 統合アカウント。[統合アカウント](app-service-logic-enterprise-integration-create-integration-account.md)を作成できます。
* ロジック アプリ。[ロジック アプリ](app-service-logic-create-a-logic-app.md)を作成し、ログ記録を有効にすることができます。手順については、[こちら](app-service-logic-monitor-your-logic-apps.md)を参照してください。

## <a name="enable-logging-for-an-integration-account"></a>統合アカウントのログ記録の有効化
**Azure Portal** でも **Monitor** でも、統合アカウントのログ記録を有効にすることができます。

### <a name="enable-logging-with-azure-portal"></a>Azure ポータルを使用したログの有効化

1. **統合アカウント**を選択し、**診断ログ**を選択します。 
![統合アカウントの選択](./media/app-service-logic-monitor-integration-account/Pic5.png)  

2. **サブスクリプション**と**リソース グループ**を選択し、リソースの種類として**統合アカウント**を選択します。[リソース] の一覧で **[統合アカウント]** を選択し、診断を有効にします。  **[診断を有効にする]** をクリックして、選択した統合アカウントに対する診断を有効にします。               
![統合アカウントの選択](./media/app-service-logic-monitor-integration-account/Pic2.png) 

3. 状態として **[有効]** を選択します。       
![診断を有効化](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. **[Send to Log Analytics (Log Analytics に送信)]** を選択して、データを出力するように Log Analytics を構成します。               
![診断を有効化](./media/app-service-logic-monitor-integration-account/Pic4.png)

### <a name="enable-logging-with-monitor"></a>Monitor を使用したログの有効化

1. **[Monitor]** を選択し、**[診断ログ]** をクリックします。  
![Monitor の選択](./media/app-service-logic-monitor-integration-account/Pic1.png)

2. **サブスクリプション**と**リソース グループ**を選択し、リソースの種類として**統合アカウント**を選択します。[リソース] の一覧で **[統合アカウント]** を選択し、診断を有効にします。  **[診断を有効にする]** をクリックして、選択した統合アカウントに対する診断を有効にします。               
![統合アカウントの選択](./media/app-service-logic-monitor-integration-account/Pic2.png)

3. 状態として **[有効]** を選択します。       
![診断を有効化](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. **[Send to Log Analytics (Log Analytics に送信)]** を選択して、データを出力するように Log Analytics を構成します。![診断の有効化](./media/app-service-logic-monitor-integration-account/Pic4.png)

## <a name="extending-your-solutions"></a>ソリューションの拡張
**Log Analytics** の他に、統合アカウントと[ロジック アプリ](./app-service-logic-monitor-your-logic-apps.md)を構成して、イベント ハブやストレージ アカウントにも出力できます。         
![Azure 診断の設定](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

こうしたテレメトリをイベント ハブやストレージから他のサービス ([Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)、[Power BI](https://powerbi.com) など) に渡すことで、統合ワークフローをリアルタイムに監視することができます。

## <a name="supported-tracking-schema"></a>サポートされている追跡スキーマ
以下の種類の追跡スキーマがサポートされています。  カスタム型以外は、すべてが固定スキーマです。

* [カスタム追跡スキーマ](app-service-logic-track-integration-account-custom-tracking-shema.md)   
* [AS2 の追跡スキーマ](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [X12 の追跡スキーマ](app-service-logic-track-integration-account-x12-tracking-shemas.md)  

## <a name="next-steps"></a>次のステップ
[OMS ポータルでの B2B メッセージの追跡](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Enterprise Integration Pack についての詳細情報](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


