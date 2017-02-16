---
title: "統合アカウントと Enterprise Integration Pack の概要 | Microsoft Docs"
description: "統合アカウント、Enterprise Integration Pack と Logic Apps についての詳細情報"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: dec91134-8c21-4d86-92f2-da6c131d2ccf
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: ef6023593d39bd1422bb40f235618dcd8b62bd71


---
# <a name="overview-of-integration-accounts"></a>統合アカウントの概要
## <a name="what-is-an-integration-account"></a>統合アカウントとは
統合アカウントは Azure アカウントです。これにより、エンタープライズ統合アプリでスキーマ、マップ、証明書、パートナーおよび契約を含むアーティファクトを管理することができます。 作成する統合アプリには、スキーマ、マップまたは証明書などにアクセスするために統合アカウントを使用する必要があります。

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
5. ページを更新します。 新しい統合アカウントがリストされていることを確認します。 お疲れさまでした。  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

## <a name="how-to-link-an-integration-account-to-a-logic-app"></a>ロジック アプリに統合アカウントをリンクする方法
ロジック アプリでマップ、スキーマ、契約、および統合アカウントにある他のアーティファクトにアクセスするには、最初にロジック アプリに統合アカウントをリンクする必要があります。

### <a name="here-are-the-steps-to-link-an-integration-account-to-a-logic-app"></a>ロジック アプリに統合アカウントをリンクする手順を次に示します。
#### <a name="prerequisites"></a>前提条件
* 統合アカウント
* ロジック アプリ

> [!NOTE]
> 開始する前に、統合アカウントおよびロジック アプリが **同じ Azure の場所** にあることを確認してください。
> 
> 

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

ロジック アプリに統合アカウントがリンクされると、ロジック アプリに移動し、XML の検証、フラット ファイルのエンコード/デコードや変換などの B2B コネクタを使用して、B2B 機能を備えたアプリを作成することができます。  

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
> 
> 

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
* [Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")  
* [契約についての詳細情報](logic-apps-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  




<!--HONumber=Jan17_HO3-->


