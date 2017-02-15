---
title: "Enterprise Integration Pack での XML 検証の概要 | Microsoft Docs"
description: "Enterprise Integration Pack と Logic Apps での検証のしくみについて説明します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7a40b20c8845d09547e80eb230c73ef7683eb2b7


---
# <a name="enterprise-integration-with-xml-validation"></a>XML 検証を使用した Enterprise Integration
## <a name="overview"></a>概要
B2B のシナリオでは多くの場合、契約の対象となるパートナーは、データの処理が開始される前に、パートナーの間で交換されるメッセージが有効であることを検証する必要があります。 Enterprise Integration Pack では、XML 検証コネクタを使用して、定義済みのスキーマに対してドキュメントを検証できます。  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>XML 検証コネクタを使用してドキュメントを検証する方法
1. ロジック アプリを作成し、XML データの検証に使用するスキーマが含まれた [統合アカウントにリンク](app-service-logic-enterprise-integration-accounts.md "ロジック アプリへの統合アカウントのリンクについての詳細情報")します。
2. ロジック アプリに **[Request - When an HTTP request is received (要求 - HTTP 要求の受信時)]** トリガーを追加します。  
   ![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)    
3. **[アクションの追加]** を選択し、**[XML の検証]** アクションを追加します。  
4. 検索ボックスに「 *xml* 」と入力し、すべてのアクションから使用するアクションだけをフィルター処理します。 
5. **[XML の検証]**    を選択します。  
   ![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)   
6. **[コンテンツ]** ボックスを選択します。  
   ![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. 検証する内容として body タグを選択します。   
   ![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)  
8. **[スキーマ名]** リスト ボックスを選択し、上側の *[コンテンツ]* の入力内容を検証するために使用するスキーマを選択します。     
   ![](./media/app-service-logic-enterprise-integration-xml/xml-4.png) 
9. 作業内容を保存します。  
   ![](./media/app-service-logic-enterprise-integration-xml/xml-5.png) 

この時点で、検証コネクタの設定が終了します。 実際のアプリケーションでは、検証されたデータを SalesForce などの LOB アプリケーション内に格納する必要がある場合があります。 検証の出力を Salesforce に送信するアクションを簡単に追加できます。 

これで、HTTP エンドポイントに要求を送信して、検証のアクションをテストできます。  

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")   




<!--HONumber=Nov16_HO3-->


