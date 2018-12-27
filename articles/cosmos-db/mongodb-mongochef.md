---
title: Azure Cosmos DB での Studio 3T (MongoChef) の使用 | Microsoft Docs
description: Azure Cosmos DB MongoDB API アカウントで Studio 3T を使用する方法について説明します。
keywords: MongoChef、Studio 3T
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2018
ms.author: sclyon
ms.openlocfilehash: af66f413055c01d9a4c3e94c7d3b9c5f1d5917b0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038372"
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Azure Cosmos DB: MongoDB API アカウントでの Studio 3T の使用

Azure Cosmos DB MongoDB API アカウントに接続するには、以下を実行する必要があります。

* [Studio 3T](https://studio3t.com/) (以前の名称は MongoChef) をダウンロードしてインストールする
* MongoDB アカウントの Azure Cosmos DB [接続文字列](connect-mongodb-account.md)情報を取得する

## <a name="create-the-connection-in-studio-3t"></a>Studio 3T で接続を作成する
Studio 3T 接続マネージャーに Azure Cosmos DB アカウントを追加するには、次の手順を実行します。

1. 「[Connect a MongoDB application to Azure Cosmos DB (MongoDB アプリケーションを Azure Cosmos DB に接続する)](connect-mongodb-account.md)」の記事にある手順を使用して、MongoDB API アカウントの Azure Cosmos DB 接続情報を取得します。

    ![接続文字列ページのスクリーンショット](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. **[Connect (接続)]** をクリックして接続マネージャーを開き、**[New Connection (新しい接続)]** をクリックします。

    ![Studio 3T 接続マネージャーのスクリーンショット](./media/mongodb-mongochef/ConnectionManager.png)
3. **[New Connection] (新しい接続)** ウィンドウの **[Server] (サーバー)** タブで、Azure Cosmos DB アカウントのホスト (FQDN) とポートを入力します。

    ![Studio 3T 接続マネージャーの [Server] (サーバー) タブのスクリーンショット](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. **[New Connection] (新しい接続)** ウィンドウの **[認証]** タブで、**[Basic (MONGODB-CR or SCARM-SHA-1)] (基本 (MONGODB-CR または SCARM-SHA-1))** の認証モードを選択し、ユーザー名とパスワードを入力します。  既定の認証データベース (admin) をそのまま使用するか、または独自の値を指定します。

    ![Studio 3T 接続マネージャーの [認証] タブのスクリーンショット](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. **[New Connection (新しい接続)]** ウィンドウの **[SSL]** タブで、**[Use SSL protocol to connect (SSL プロトコルを使用して接続する)]** チェック ボックスをオンにし、**[Accept server self-signed SSL certificates (サーバー自己署名 SSL 証明書を受け付ける)]** オプション ボタンをオンにします。

    ![Studio 3T 接続マネージャーの [SSL] タブのスクリーンショット](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. **[Test Connection (接続のテスト)]** をクリックして接続情報を検証し、**[OK]** をクリックして [New Connection (新しい接続)] ウィンドウに戻り、**[Save (保存)]** をクリックします。

    ![Studio 3T の [テスト接続] ウィンドウのスクリーンショット](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Studio 3T を使用してデータベース、コレクション、およびドキュメントを作成する
Studio 3T を使用してデータベース、コレクション、およびドキュメントを作成するには、次の手順を実行します。

1. **接続マネージャー**で目的の接続を強調表示し、**[Connect (接続)]** をクリックします。

    ![Studio 3T 接続マネージャーのスクリーンショット](./media/mongodb-mongochef/ConnectToAccount.png)
2. ホストを右クリックし、**[データベースの追加]** を選択します。  データベースの名前を指定し、 **[OK]** をクリックします。

    ![Studio 3T の [データベースの追加] オプションのスクリーンショット](./media/mongodb-mongochef/AddDatabase1.png)
3. データベースを右クリックし、**[コレクションの追加]** を選択します。  コレクションの名前を指定し、 **[Create (作成)]** をクリックします。

    ![Studio 3T の [コレクションの追加] オプションのスクリーンショット](./media/mongodb-mongochef/AddCollection.png)
4. **[Collection (コレクション)]** メニュー項目をクリックし、**[Add Document (ドキュメントの追加)]** をクリックします。

    ![Studio 3T の [ドキュメントの追加] メニュー項目のスクリーンショット](./media/mongodb-mongochef/AddDocument1.png)
5. [Add Document (ドキュメントの追加)] ダイアログに次の情報を貼り付けて **[Add Document (ドキュメントの追加)]** をクリックします。

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. 今度は次の内容を含む別のドキュメントを追加します。

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. サンプル クエリを実行します。 たとえば姓が "Andersen" である世帯を検索し、parents フィールドと state フィールドを取得します。

    ![Screen shot of Mongo Chef query results](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>次の手順
* Azure Cosmos DB MongoDB API の[サンプル](mongodb-samples.md)を確認します。
