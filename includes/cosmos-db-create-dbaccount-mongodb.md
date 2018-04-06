---
title: Azure Cosmos DB MongoDB API アカウントを作成する
description: Azure Portal で Azure Cosmos DB MongoDB API アカウントを作成する方法について説明します
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
1. 新しいウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。
2. 左側のメニューで、**[リソースの作成]**、**[データベース]** の順にクリックし、**[Azure Cosmos DB]** の下にある **[作成]** をクリックします。
   
   ![その他のサービスと Azure Cosmos DB が強調表示された Azure Portal のスクリーンショット](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. **[新しいアカウント]** ブレードで、API として **MongoDB** を指定し、Azure Cosmos DB アカウントに必要な構成を入力します。
 
    ![新しい Azure Cosmos DB ブレードのスクリーン ショット](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * **[ID]** は、Azure Cosmos DB アカウントを識別するために使用する一意の名前である必要があります。 使用できる文字は、英小文字、数字、"-" のみです。文字数は 3 から 50 文字にする必要があります。
    * **[サブスクリプション]** は、自分の Azure サブスクリプションです。 自動的に入力されます。
    * **[リソース グループ]** は、Azure Cosmos DB アカウントのリソース グループ名です。
    * **[場所]** は、Azure Cosmos DB インスタンスが存在している地理的な場所です。 ユーザーに最も近い場所を選択します。

4. **[作成]** をクリックしてアカウントを作成します。
5. ツール バーの **[通知]** をクリックして、デプロイ プロセスを監視します。

    ![デプロイが開始したという通知](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  デプロイが完了したら、[All Resources] \(すべてのリソース) タイルから、新しいアカウントを開きます。 

    ![[すべてのリソース] タイルの Azure Cosmos DB アカウント](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
