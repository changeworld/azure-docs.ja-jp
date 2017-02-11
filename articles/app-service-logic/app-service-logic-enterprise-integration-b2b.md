---
title: "Enterprise Integration Pack での B2B ソリューションの作成 |Microsoft Docs"
description: "Enterprise Integration Pack の B2B 機能を使用したデータの受信についての詳細情報"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dbf5e3bcdbaccfb4cd3d25a94d1070d6ca9d0bc6


---
# <a name="learn-about-receiving-data-using-the-b2b-features-of-the-enterprise-integration-pack"></a>Enterprise Integration Pack の B2B 機能を使用したデータの受信についての詳細情報
## <a name="overview"></a>Overview
このドキュメントは、Logic Apps Enterprise Integration Pack の一部です。 [Enterprise Integration Pack の機能](app-service-logic-enterprise-integration-overview.md)の詳細については、概要のページを参照してください。

## <a name="prerequisites"></a>前提条件
AS2 および X12 アクションを使用するには、エンタープライズ統合アカウントが必要です。

[エンタープライズ統合アカウントの作成方法](app-service-logic-enterprise-integration-accounts.md)

## <a name="how-to-use-the-logic-apps-b2b-connectors"></a>Logic Apps B2B コネクタの使用方法
統合アカウントを作成し、パートナーと契約を追加したら、企業間 (B2B) ワークフローを実装するロジック アプリを作成する準備が整います。

このウォークスルーで、AS2 および X12 アクションの使用方法を確認し、取引先からデータを受信する企業間ロジック アプリを作成します。

1. 新しいロジック アプリを作成し、[統合アカウントにリンク](app-service-logic-enterprise-integration-accounts.md)します。  
2. ロジック アプリに **[Request - When an HTTP request is received (要求 - HTTP 要求の受信時)]** トリガーを追加します。  
   ![](./media/app-service-logic-enterprise-integration-b2b/flatfile-1.png)  
3. **[アクションの追加]** を選択し、**[Decode AS2 (AS2 デコード)]** アクションを追加します。  
   ![](./media/app-service-logic-enterprise-integration-b2b/transform-2.png)  
4. 検索ボックスに「**as2**」と入力し、すべてのアクションから使用するアクションだけをフィルタリングします。  
   ![](./media/app-service-logic-enterprise-integration-b2b/b2b-5.png)  
5. **[AS2 - Decode AS2 message (AS2 - AS2 メッセージのデコード)]** アクションを選択します。  
   ![](./media/app-service-logic-enterprise-integration-b2b/b2b-6.png)  
6. ここで示すように、入力として受け取る **本文** を追加します。 この例では、ロジック アプリをトリガーした HTTP 要求の本文を選択します。 代わりに、**[ヘッダー]** フィールドに式を入力し、ヘッダーを入力することができます。
   
    @triggerOutputs()['headers']
7. AS2 に必要な **ヘッダー** を追加します。 これらは HTTP 要求ヘッダーになります。 この例では、ロジック アプリをトリガーした HTTP 要求のヘッダーを選択します。
8. ここで、**[アクションの追加]** をもう一度選択して、X12 メッセージのデコード アクションを追加します。  
   ![](./media/app-service-logic-enterprise-integration-b2b/b2b-9.png)   
9. 検索ボックスに「**x12**」と入力し、すべてのアクションから使用するアクションだけをフィルタリングします。  
   ![](./media/app-service-logic-enterprise-integration-b2b/b2b-10.png)  
10. **[X12 - Decode X12 message (X12 - X12 メッセージのデコード)]** アクションを選択し、ロジック アプリに追加します。  
    ![](./media/app-service-logic-enterprise-integration-b2b/b2b-as2message.png)  
11. ここで、上記の AS2 アクションの出力となるこのアクションへの入力を指定する必要があります。 実際のメッセージの内容は JSON オブジェクトにあり、base64 でエンコードされています。 そのため、入力として式を指定する必要があります。**[X12 FLAT FILE MESSAGE TO DECODE (デコードする X12 フラット ファイル メッセージ)]** 入力フィールドに次の式を入力します。  
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])  
12. この手順では、取引先から受信した X12 データをデコードし、JSON オブジェクト内の項目の数の出力します。 データの受信をパートナーに知らせるために、HTTP 応答アクションでの AS2 Message Disposition Notification (MDN) を含む応答を返信することができます。  
13. **[応答]** アクションを追加するには、**[アクションの追加]** を選択します。   
    ![](./media/app-service-logic-enterprise-integration-b2b/b2b-14.png)  
14. 検索ボックスに「**response**」と入力し、すべてのアクションから使用するアクションだけをフィルタリングします。  
    ![](./media/app-service-logic-enterprise-integration-b2b/b2b-15.png)  
15. **[応答]** アクションを選択し、追加します。  
    ![](./media/app-service-logic-enterprise-integration-b2b/b2b-16.png)  
16. 応答の **[本文]** フィールドを設定するには、次の式を使用して、**[Decode X12 message (X12 メッセージのデコード)]** アクションの出力から MDN にアクセスします。  
    
    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])  

![](./media/app-service-logic-enterprise-integration-b2b/b2b-17.png)  

1. 作業内容を保存します。  
   ![](./media/app-service-logic-enterprise-integration-b2b/transform-5.png)  

この時点で、B2B ロジック アプリの設定が終了します。 実際のアプリケーションでは、デコードされた X12 データを LOB アプリケーションまたはデータ ストア内に格納する必要がある場合があります。 アクションをさらに追加してこれを行ったり、カスタム API を記述して独自のLOB アプリケーションに接続し、ロジック アプリでこれらの API を使用したりすることが簡単にできます。

## <a name="features-and-use-cases"></a>機能とユース ケース
* AS2 と X12 のデコードおよびエンコード アクションにより、ロジック アプリを使用した業界標準プロトコルによる取引先とのデータの送受信が可能になります。  
* 相互に AS2 および X12 を使用しているかどうかにかかわらず、必要に応じて、取引先とデータを交換できます。
* B2B アクションにより、統合アカウントでパートナーと契約を簡単に作成し、ロジック アプリでこれらを使用できます。  
* 他のアクションでロジック アプリを拡張することにより、SalesForce などの他のアプリケーションや サービスと、データを送受信できます。  

## <a name="learn-more"></a>詳細情報
[Enterprise Integration Pack についての詳細情報](app-service-logic-enterprise-integration-overview.md)  




<!--HONumber=Nov16_HO3-->


