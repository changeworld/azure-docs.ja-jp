---
title: "Azure Cosmos DB アカウントの MongoDB 接続文字列 | Microsoft Docs"
description: "MongoDB 接続文字列を使用して、MongoDB アプリを Azure Cosmos DB アカウントに接続する方法について説明します。"
keywords: "mongodb 接続文字列"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 95fe71c87a3f70174b59cc866d7d399b7e91720c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---

# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Azure Cosmos DB への MongoDB アプリケーションの接続
MongoDB 接続文字列を使用して、MongoDB アプリを Azure Cosmos DB アカウントに接続する方法について説明します。 MongoDB アプリを Azure Cosmos DB データベースに接続することで、Azure Cosmos DB データベースを MongoDB アプリのデータ ストアとして使用できます。 

このチュートリアルでは、接続文字列情報を取得する 2 とおりの方法を説明します。

- [クイック スタートを使う方法](#QuickstartConnection)。.NET、Node.js、MongoDB シェル、Java、Python のドライバー向けの方法です。
- [カスタム接続文字列を使う方法](#GetCustomConnection)。その他のドライバー向けの方法です。

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 Azure アカウントがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)を今すぐ作成できます。 
- Azure Cosmos DB アカウント。 手順については、[MongoDB アプリで使用するための Azure Cosmos DB アカウントの作成](documentdb-create-mongodb-account.md)に関する記事をご覧ください。

## <a id="QuickstartConnection"></a> クイック スタートを使用した MongoDB 接続文字列の取得
1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Cosmos DB]** ブレードで、MongoDB API アカウントを選択します。 
3. [アカウント] ブレードの**左側のナビゲーション** バーで、**[クイック スタート]** をクリックします。 
4. プラットフォームを選択します ("*.NET ドライバー*"、"*Node.js ドライバー*"、"*MongoDB シェル*"、"*Java ドライバー*"、"*Python ドライバー*")。 ドライバーまたはツールの一覧が表示されなくても、接続コード スニペットは継続的に文書化されますので心配はいりません。 表示を希望するものを下部のコメント欄に記入してください。また、独自の接続を行う方法については、[アカウントの接続文字列情報の取得](#GetCustomConnection)に関するセクションを参照してください。
5. コード スニペットを MongoDB アプリにコピーして貼り付ければ、準備は完了です。

    ![[クイック スタート] ブレードのスクリーン ショット](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> MongoDB 接続文字列を取得してカスタマイズする
1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Cosmos DB]** ブレードで、MongoDB API アカウントを選択します。 
3. [アカウント] ブレードの**左側のナビゲーション** バーで、**[接続文字列]** をクリックします。 
4. **[接続文字列情報]** ブレードが表示され、MongoDB のドライバーを使用してこのアカウントに接続するために必要なすべての情報 (あらかじめ構築された接続文字列も含む) が表示されます。

    ![Screen shot of the connection string blade](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>接続文字列の要件
> [!Important]
> Azure Cosmos DB には、厳密なセキュリティ要件と基準があります。 Azure Cosmos DB アカウントでは、**SSL** による認証とセキュリティで保護された通信が要求されます。
>
>

Azure Cosmos DB は、標準の MongoDB 接続文字列の URI 形式をサポートしていますが、固有の要件がいくつかあることに注意してください。たとえば、Azure Cosmos DB アカウントでは、SSL による認証とセキュリティで保護された通信が要求されます。  そのため接続文字列は次の形式となります。

    mongodb://username:password@host:port/[database]?ssl=true

この文字列の値は、先ほど示した [接続文字列] ブレードで確認できます。

* ユーザー名 (必須)
  * Azure Cosmos DB アカウント名
* パスワード (必須)
  * Azure Cosmos DB アカウントのパスワード
* ホスト (必須)
  * Azure Cosmos DB アカウントの FQDN
* ポート (必須)
  * 10250
* データベース (省略可)
  * 対応する接続で使用される既定のデータベース (データベースが指定されていない場合は、既定のデータベースは "test" になります)
* ssl=true (必須)

たとえば、上の [接続文字列情報] に表示されているアカウントの場合、  有効な接続文字列は次のようになります。

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB: MongoDB 用 API アカウントで [MongoChef を使用](documentdb-mongodb-mongochef.md)する方法を確認します。
* Azure Cosmos DB: MongoDB 用 API の[サンプル](documentdb-mongodb-samples.md)を確認します。

