---
title: "パートナーと Enterprise Integration Pack について |Microsoft Docs"
description: "Enterprise Integration Pack と Logic Apps を使用してパートナーを使用する方法について説明します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5272f3a09295aac62c8e9ffa17270803f556ba5


---
# <a name="learn-about-partners-and-enterprise-integration-pack"></a>パートナーと Enterprise Integration Pack についての詳細情報
## <a name="overview"></a>概要
パートナーを作成する前に、相互にやり取りされるメッセージの識別と検証に役立つ情報を、取引を始める相手の組織と共有する必要があります。 こうした話し合いが終わり、取引を始める準備が整ったら、統合アカウントで *パートナー* を作成できます。

## <a name="what-is-a-partner"></a>パートナーとは
パートナーとは、企業間 (B2B) のメッセージングとトランザクションに参加するエンティティです。 

## <a name="how-are-partners-used"></a>パートナーの使用方法
パートナーは、契約の作成に使用されます。 契約は、パートナー間で交換されるメッセージに関する詳細を定義するものです。 

契約を作成する前に、少なくとも 2 つのパートナーを統合アカウントに追加しておく必要があります。 契約の対象となるパートナーのいずれかは、自分の組織である必要があります。 自分の組織を表すパートナーは、**ホスト パートナー**と呼ばれます。 2 つ目のパートナーは、自分の組織とメッセージを交換する他の組織を表し、 **ゲスト パートナー**と呼ばれます。 ゲスト パートナーには、別の会社のほか、自身の組織内の部署も指定できます。  

パートナーを追加した後、追加したパートナーを使用して契約を作成します。 

受信と送信の設定は、ホスト パートナー側から見た方向になります。 たとえば、契約の受信設定は、ホスト パートナーがゲスト パートナーからメッセージを受信する方法を決めるものです。 同様に、契約の送信設定は、ホスト パートナーがゲスト パートナーにメッセージを送信する方法を示すものです。

## <a name="how-to-create-a-partner"></a>パートナーを作成する方法
Azure ポータルで次の手順を実行します。  

1. **[参照]** を選択します。  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[統合アカウント]** を選択します。     
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. パートナーの追加先となる**統合アカウント**を選択します。  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. **[パートナー]** タイルを選択します。  
   ![](./media/app-service-logic-enterprise-integration-partners/partner-1.png)  
5. 開いた [パートナー] ブレードの **[追加]** ボタンを選択します。  
   ![](./media/app-service-logic-enterprise-integration-partners/partner-2.png)  
6. パートナーの**名前**を入力してから、**[Qualifier (修飾子)]** を選択し、最後に**値**を入力します。 この値は、アプリに届くドキュメントを識別するために使用されます。  
   ![](./media/app-service-logic-enterprise-integration-partners/partner-3.png)  
7. *ベル* 通知アイコンを選択して、パートナー作成プロセスの進捗状況を確認します。  
   ![](./media/app-service-logic-enterprise-integration-partners/partner-4.png)  
8. **[パートナー]** タイルを選択します。 これにより、タイルが更新されます。新しいパートナーが正しく追加されたことが反映されており、パートナーの増加後の数が表示されます。    
   ![](./media/app-service-logic-enterprise-integration-partners/partner-5.png)  
9. [パートナー] タイルを選択すると、[パートナー] ブレードにも、新しく追加されたパートナーが表示されます。    
   ![](./media/app-service-logic-enterprise-integration-partners/partner-6.png)  

## <a name="how-to-edit-a-partner"></a>パートナーを編集する方法
統合アカウントに既に存在するパートナーを編集するには、次の手順に従います。  

1. **[パートナー]** タイルを選択します。  
2. [パートナー] ブレードが開いたら、編集するパートナーを選択します。  
3. **[Update Partner (パートナーの更新)]** タイルで、必要な変更を行います。  
4. 変更に問題がない場合は、**[保存]** リンクを選択します。問題がある場合は、**[破棄]** リンクを選択して変更を破棄します。  
   ![](./media/app-service-logic-enterprise-integration-partners/edit-1.png)  

## <a name="how-to-delete-a-partner"></a>パートナーを削除する方法
1. **[パートナー]** タイルを選択します。  
2. [パートナー] ブレードが開いたら、編集するパートナーを選択します。  
3. **[削除]** リンクを選択します。    
   ![](./media/app-service-logic-enterprise-integration-partners/delete-1.png)   

## <a name="next-steps"></a>次のステップ
* [契約についての詳細情報](app-service-logic-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  




<!--HONumber=Nov16_HO3-->


