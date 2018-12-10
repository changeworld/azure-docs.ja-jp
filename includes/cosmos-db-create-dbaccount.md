---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a144433e1ff6c0f7ca4e0ed72704d339150f2d56
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643419"
---
1. 新しいブラウザー ウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** > **[データベース]** > **[Azure Cosmos DB]** を選択します。
   
   ![Azure Portal の [データベース] ウィンドウ](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. **[新しいアカウント]** ページで、新しい Azure Cosmos DB アカウントの設定を入力します。 
 
    Setting|値|説明
    ---|---|---
    ID|一意の名前を入力します|自分の Azure Cosmos DB アカウントを識別するための一意の名前を入力します。 指定した ID に *documents.azure.com* が付加されて URI が作成されるので、一意の ID を使用してください。<br><br>ID に使用できるのは、英小文字、数字、ハイフン (-) のみです。 長さは 3 ～ 50 文字にする必要があります。
    API|SQL|API によって、作成するアカウントの種類が決まります。 Azure Cosmos DB では、ドキュメント データベース用の SQL、グラフ データベース用の Gremlin、ドキュメント データベース用の MongoDB、Table API、Cassandra API の 5 種類の API が提供されています。 現在は、API ごとに別のアカウントを作成する必要があります。 <br><br>この記事では、ドキュメント データベースを作成し、SQL 構文を使用してクエリを実行するため、**[SQL]** を選択します。 <br><br>[SQL API について詳しくは、こちらをご覧ください](../articles/cosmos-db/documentdb-introduction.md)。|
    サブスクリプション|該当するサブスクリプション|この Azure Cosmos DB アカウントに使用する Azure サブスクリプションを選択します。 
    リソース グループ|新規作成<br><br>その後、ID で指定したものと同じ一意の名前を入力|**[新規作成]** を選択します。 その後、自分のアカウントの新しいリソースグループの名前を入力します。 簡略化するには、ID と同じ名前を使用します。 
    Location|ユーザーに最も近いリージョンを選択|Azure Cosmos DB アカウントをホストする地理的な場所を選択します。 データに最も高速にアクセスできる、ユーザーに最も近い場所を使用します。
    Geo 冗長の有効化| 空白 | このオプションでは、データベースのレプリケート バージョンが 2 番目 (ペア) のリージョンに作成されます。 このチェック ボックスはオフのままにします。 

    **作成**を選択します。

    ![Azure Cosmos DB の新しいアカウント ページ](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. アカウントの作成には数分かかります。 ポータルに "**Azure Cosmos DB アカウントが作成されました**" ページが表示されるまで待機します。

    ![Azure Portal の [通知] ウィンドウ](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

