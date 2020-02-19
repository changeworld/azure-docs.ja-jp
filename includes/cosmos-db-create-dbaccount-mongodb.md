---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ecf45a692f83689ed56c03bec13f291781508474
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061712"
---
1. 新しいブラウザー ウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。

2. 左側のメニューで、 **[リソースの作成]** を選択します。
   
   ![Azure portal でリソースを作成する](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. **[新規]** ページで、 **[データベース]**  >  **[Azure Cosmos DB]** の順に選択します。
   
   ![Azure Portal の [データベース] ウィンドウ](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. **[Azure Cosmos DB アカウントの作成]** ページで、新しい Azure Cosmos DB アカウントの設定を入力します。 
 
    設定|Value|説明
    ---|---|---
    サブスクリプション|該当するサブスクリプション|この Azure Cosmos DB アカウントに使用する Azure サブスクリプションを選択します。 
    リソース グループ|新規作成<br><br>アカウント名と同じ名前を入力します|**[新規作成]** を選択します。 その後、自分のアカウントの新しいリソース グループの名前を入力します。 簡略化のため、Azure Cosmos DB アカウント名と同じ名前を使用します。 
    アカウント名|一意の名前を入力します|自分の Azure Cosmos DB アカウントを識別するための一意の名前を入力します。 アカウント URI は、一意のアカウント名に *mongo.cosmos.azure.com* が追加されたものになります。<br><br>アカウント名に使用できるのは、小文字、数字、ハイフン (-) のみで、長さは 3 文字から 31 文字の範囲にする必要があります。
    API|Mongo DB 用 Azure Cosmos DB API|API によって、作成するアカウントの種類が決まります。 Azure Cosmos DB には、5 種類の API が用意されています。ドキュメント データベース用の Core(SQL)、グラフ データベース用の Gremlin、ドキュメント データベース用の MongoDB 用 Azure Cosmos DB API、Azure Table、Cassandra です。 現在は、API ごとに別のアカウントを作成する必要があります。 <br><br>このクイックスタートでは MongoDB に対応するコレクションを作成するので、**MongoDB 用 Azure Cosmos DB API** を選択します。<br><br>[MongoDB 用 Azure Cosmos DB API の詳細を確認してください](../articles/cosmos-db/mongodb-introduction.md)。|
    Location|ユーザーに最も近いリージョンを選択|Azure Cosmos DB アカウントをホストする地理的な場所を選択します。 データに最も高速にアクセスできる、ユーザーに最も近い場所を使用します。|

    **[確認および作成]** を選択します。 **[ネットワーク]** セクションと **[タグ]** セクションはスキップできます。 

    ![Azure Cosmos DB の新しいアカウント ページ](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. アカウントの作成には数分かかります。 ポータルに "**MongoDB 用 Azure Cosmos DB API アカウントの準備ができました** ページが表示されるまで待ちます。

    ![Azure Portal の [通知] ウィンドウ](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
