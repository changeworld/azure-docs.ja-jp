---
title: "Azure Logic Apps で統合アカウントを作成、リンク、削除、または移動する | Microsoft Docs"
description: "統合アカウントを作成してロジック アプリにリンクする方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: dca762854f22721de76f69cbc28c0377d563fe28
ms.openlocfilehash: e26271a1291fc76e3ea8f93a7aa9ab8c811ff604


---

# <a name="what-is-an-integration-account"></a>統合アカウントとは
統合アカウントによって、エンタープライズ統合アプリでスキーマ、マップ、証明書、パートナー、契約などのアーティファクトを管理できます。 作成した統合アプリケーションはすべて、統合アカウントを使用してこれらのスキーマ、マップ、証明書などにアクセスします。

## <a name="create-an-integration-account"></a>統合アカウントの作成
1. **[参照]** を選択します。   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[統合アカウント]** を選択します。     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. ページ上部のメニューから *[追加]* を選択します。      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. **名前**を入力し、使用する**サブスクリプション**を選択します。新しい**リソース グループ**を作成するか、既存のリソース グループを選択し、統合アカウントがホストされる**場所**を選択します。**価格レベル**を選択し、**[作成]** ボタンを選択します。   
   
   この時点で、統合アカウントは選択した場所にプロビジョニングされます。 これは、1 分以内に完了する必要があります。    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. ページを更新します。 新しい統合アカウントがリストされていることを確認します。  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

次に、今作成した統合アカウントをロジック アプリにリンクさせます。 

## <a name="link-an-integration-account-to-a-logic-app"></a>ロジック アプリに統合アカウントをリンクさせる方法
Logic Apps でマップ、スキーマ、契約、および統合アカウントにある他のアーティファクトにアクセスするには、ロジック アプリに統合アカウントをリンクさせます。

#### <a name="prereqs"></a>前提条件
* 統合アカウント
* ロジック アプリ

> [!NOTE] 
> 開始する前に、統合アカウントおよびロジック アプリが**同じ Azure の場所**にあることを確認してください。

1. ロジック アプリのメニューから **[設定]** リンクを選択します。  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. [設定] ブレードから **[統合アカウント]** 項目を選択します。  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. **[統合アカウントを選択してください]** ボックスの一覧からロジック アプリにリンクする統合アカウントを選択します。  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. 作業内容を保存します。  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. 統合アカウントがロジック アプリにリンクされていることと、統合アカウント内のすべてのアーティファクトがロジック アプリに対して使用できるようになったことを示す通知が表示されます。  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

統合アカウントがロジック アプリにリンクされ、Logic Apps 内で B2B コネクタが使用できるようになりました。 一般的な B2B コネクタには、XML の検証、フラット ファイルのエンコードやデコードなどがあります。  

## <a name="how-to-delete-an-integration-account"></a>統合アカウントの削除方法
1. **[参照]** を選択します。  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[統合アカウント]** を選択します。     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 削除する**統合アカウント**を選択します。  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. メニューに配置されている **[削除]** リンクを選択します。   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. 選択内容を確認します。    

## <a name="how-to-move-an-integration-account"></a>統合アカウントの移動方法
統合アカウントは、新しいサブスクリプションと新しいリソース グループに簡単に移動できます。 統合アカウントを移動する必要がある場合は、次の手順に従います。

> [!IMPORTANT]
> 統合アカウントを移動すると、新しいリソース ID を使用するために、すべてのスクリプトを更新する必要があります。

1. **[参照]** を選択します。  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[統合アカウント]** を選択します。     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. 削除する**統合アカウント**を選択します。  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. メニューに配置されている **[移動]** リンクを選択します。   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. 選択内容を確認します。    

## <a name="next-steps"></a>次のステップ
* [契約についての詳細情報](../logic-apps/logic-apps-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  




<!--HONumber=Jan17_HO5-->


