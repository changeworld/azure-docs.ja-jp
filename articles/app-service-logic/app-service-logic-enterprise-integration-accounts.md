<properties 
	pageTitle="Enterprise Integration Pack の統合アカウントの作成 | Microsoft Azure App Service" 
	description="Enterprise Integration Pack の統合アカウントの作成" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# Enterprise Integration Pack の統合アカウントの作成

統合アカウントは、B2B の機能を備えた Logic Apps の作成に使用するアーティファクトのコンテナーです。

1. **[参照]** を選択します。![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]**を選択します。![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)
3. ページ上部のメニューから *[追加]* を選択します。![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)
4. **名前**を入力 し、使用する**サブスクリプション**を選択します。新しい**リソース グループ**を作成するか、または既存のリソース グループを選択します。統合アカウントがホストされる**場所**を選択し、 **価格レベル**を選択して、 **[作成]** ボタンを選択します。

  この時点で、統合アカウントは選択した場所にプロビジョニングされます。これは、1 分以内に完了する必要があります。![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)
5. ページを更新します。新しい統合アカウントがリストされていることを確認します。お疲れさまでした。![](./media/app-service-logic-enterprise-integration-accounts/account-5.png)

<!---HONumber=AcomDC_0706_2016-->