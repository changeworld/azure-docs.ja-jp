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
ms.date: 02/23/2017
ms.author: LADocs; mandia
ms.openlocfilehash: b238ef8cf9d1328913334a92c042584334d81e3c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="what-is-an-integration-account"></a>統合アカウントとは

統合アカウントは、エンタープライズ統合アプリ (具体的にはロジック アプリ) が B2B アーティファクト (取引先、契約、マップ、スキーマ、証明書など) にアクセスして管理する手段を提供します。 このアクセスを提供するには、統合アカウントとロジック アプリの両方が "*同じ Azure の場所*" であることを確認した後、ロジック アプリに統合アカウントをリンクします。

## <a name="create-an-integration-account"></a>統合アカウントの作成

1. [Azure Portal](http://portal.azure.com "Azure Portal") にサインインします。 

2. Azure のメイン メニューで、**[すべてのサービス]** を選びます。 検索ボックスに「統合」と入力し、"**統合アカウント**" を選びます。

   ![統合アカウントを作成する](./media/logic-apps-enterprise-integration-accounts/account-1.png)

3. ページの上部にある **[追加]** を選択します。

   ![[追加] の選択](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. 統合アカウントの名前を指定し、使用する Azure サブスクリプションを選択します。 新しい**リソース グループ**を作成するか、既存のリソース グループを選択することができます。 統合アカウントをホストする **[場所]** と **[価格レベル]** を選びます。 準備ができたら、**[作成]** をクリックします。

   ![統合アカウントの詳細の指定](./media/logic-apps-enterprise-integration-accounts/account-4.png)

   Azure により、選択した場所に統合アカウントがプロビジョニングされます。通常、1 分以内で完了します。

5. ページを更新します。 新しい統合アカウントがリストされていることを確認します。

   ![新しい統合アカウントの表示](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

次に、作成した統合アカウントをロジック アプリにリンクさせます。 

## <a name="link-an-integration-account-to-a-logic-app"></a>ロジック アプリへの統合アカウントのリンク

ロジック アプリが統合アカウントにある取引先、契約、マップ、スキーマなどの B2B アーティファクトにアクセスできるようにするには、ロジック アプリに統合アカウントをリンクします。 

1. Azure Portal で、ロジック アプリを選択し、ロジック アプリの場所を確認します。

   ![ロジック アプリの選択、場所の確認](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. **[設定]** で、**[統合アカウント]** を選択します。

   ![[統合アカウント] の選択](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. **[統合アカウントを選択してください]** ボックスの一覧からロジック アプリにリンクする統合アカウントを選択します。 リンクを終了するには、**[保存]** を選択します。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

   統合アカウントがロジック アプリにリンクされていることと、統合アカウント内のすべてのアーティファクトがロジック アプリに対して使用できるようになったことを示す通知が表示されます。

   ![ロジック アプリが統合アカウントにリンクされた](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

統合アカウントがロジック アプリにリンクされ、Logic Apps 内で B2B コネクタが使用できるようになります。 一般的な B2B コネクタには、XML の検証、フラット ファイルのエンコードやデコードなどがあります。  

## <a name="delete-your-integration-account"></a>統合アカウントの削除

1. Azure のメイン メニューで、**[すべてのサービス]** を選びます。 検索ボックスに「統合」と入力し、"**統合アカウント**" を選びます。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 削除する統合アカウントを選択します。

    ![削除する統合アカウントの選択](./media/logic-apps-enterprise-integration-accounts/account-5.png)

3. メニューの **[削除]** を選択します。

    ![[削除] の選択](./media/logic-apps-enterprise-integration-accounts/delete.png)

4. 統合アカウントの削除を確認します。

## <a name="move-your-integration-account"></a>統合アカウントの移動

統合アカウントを別の Azure サブスクリプションまたはリソース グループに移動するには、以下の手順に従います。 統合アカウントを移動した後、新しいリソース ID を使うようにすべてのスクリプトを更新する必要があります。

1. Azure のメイン メニューで、**[すべてのサービス]** を選びます。 検索ボックスに「統合」と入力し、"**統合アカウント**" を選びます。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 移動する統合アカウントを選択します。 **[設定]** の **[プロパティ]** を選択します。

   ![移動する統合アカウントの選択。 [設定] の [プロパティ] の選択](./media/logic-apps-enterprise-integration-accounts/move.png)

3. 統合アカウントに関連付けられているリソース グループまたは Azure サブスクリプションを変更します。

   ![リソース グループの変更またはサブスクリプションの変更の選択](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>次の手順

* [契約についての詳細情報](../logic-apps/logic-apps-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  

