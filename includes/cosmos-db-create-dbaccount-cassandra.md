---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 09/14/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a63937169097bc779dd285957420617f57ee76be
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47002233"
---
1. 新しいブラウザー ウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。

2. **[リソースの作成]** > **[データベース]** > **[Azure Cosmos DB]** を選択します。
   
   ![Azure Portal の [データベース] ウィンドウ](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)

3. **[新しいアカウント]** ページで、新しい Azure Cosmos DB アカウントの設定を入力します。 
 
    Setting|推奨値|説明
    ---|---|---
    ID|<*一意の名前を入力*>|この Azure Cosmos DB アカウントを識別するための一意の名前を入力します。 指定した ID に *cassandra.cosmosdb.azure.com* が付加されてコンタクト ポイントが作成されるので、ID は一意であっても識別可能なものを使用してください。<br><br>ID に含めることができるのは英小文字、数字、ハイフン (-) のみ、3 文字以上で 50 文字以内にする必要があります。
    API|Cassandra|API によって、作成するアカウントの種類が決まります。 Azure Cosmos DB には、アプリケーションのニーズに応じて、SQL (ドキュメント データベース)、Gremlin (グラフ データベース)、MongoDB (ドキュメント データベース)、Azure Table、および Cassandra の 5 つの API が用意されています。現時点では、それぞれ別個のアカウントが必要です。 <br><br>このクイック スタートでは、CQL 構文を使用してクエリ可能なワイドカラム データベースを作成するため、**Cassandra** を選択します。<br><br>[Cassandra API に関する詳細を参照する](../articles/cosmos-db/cassandra-introduction.md)|
    サブスクリプション|*該当するサブスクリプション*|この Azure Cosmos DB アカウントに使用する Azure サブスクリプションを選択します。 
    リソース グループ|新規作成<br><br>*上記の ID で指定したものと同じ一意の名前を入力*|**新規作成**を選択してから、自分のアカウントの新しいリソースグループの名前を入力します。 簡略化のため、ID と同じ名前を使用することができます。 
    Location|<*ユーザーに最も近いリージョンを選択*>|Azure Cosmos DB アカウントをホストする地理的な場所を入力します。 データに最も高速にアクセスできる、ユーザーに最も近い場所を使用します。
    [ダッシュボードにピン留めする] | elect | このボックスを選択すると、新しいデータベース アカウントが、アクセスしやすいようにポータルのダッシュボードに追加されます。

    **[Create]** をクリックします。

    ![Azure Cosmos DB の新しいアカウント ページ](./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png)

4. アカウントの作成には数分かかります。 ポータルに "**設定完了。Azure Cosmos DB アカウントが作成されました**" というメッセージのページが表示されるまで待機します。

