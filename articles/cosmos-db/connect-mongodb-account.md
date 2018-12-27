---
title: Azure Cosmos DB アカウントの MongoDB 接続文字列 | Microsoft Docs
description: MongoDB 接続文字列を使用して、MongoDB アプリを Azure Cosmos DB アカウントに接続する方法について説明します。
keywords: mongodb 接続文字列
services: cosmos-db
author: slyons
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 12/19/2017
ms.author: sclyon
ms.openlocfilehash: ad8d6fe36c289c4c9e37689e1c7d755dc3bf9048
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038668"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Azure Cosmos DB への MongoDB アプリケーションの接続
MongoDB 接続文字列を使用して、MongoDB アプリを Azure Cosmos DB アカウントに接続する方法について説明します。 MongoDB アプリのデータ ストアとして Azure Cosmos DB データベースを使用できるようになります。 

このチュートリアルでは、接続文字列情報を取得する 2 とおりの方法を説明します。

- [クイック スタートを使う方法](#QuickstartConnection)。\.NET、Node.js、MongoDB シェル、Java、Python のドライバー向けの方法です
- [カスタム接続文字列を使う方法](#GetCustomConnection)。その他のドライバー向けの方法です

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 Azure アカウントがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)を今すぐ作成できます。 
- Azure Cosmos DB アカウント。 手順については、「[.NET と Azure Portal による MongoDB API Web アプリの構築](create-mongodb-dotnet.md)」を参照してください。

## <a id="QuickstartConnection"></a> クイック スタートを使用した MongoDB 接続文字列の取得
1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Cosmos DB]** ブレードで、MongoDB アカウントの API を選択します。 
3. [アカウント] ブレードの左側のウィンドウで、**[クイック スタート]** をクリックします。 
4. プラットフォームを選択します (**.NET**、 **Node.js**、 **MongoDB シェル**、 **Java**、 **Python**)。 ドライバーまたはツールの一覧が表示されなくても、接続コード スニペットは継続的に文書化されますので心配はいりません。 表示を希望するものを下部のコメント欄に記入してください。 また、独自の接続を行う方法については、[アカウントの接続文字列情報の取得](#GetCustomConnection)に関するセクションを参照してください。
5. コード スニペットを MongoDB アプリにコピーして貼り付けます。

    ![クイック スタート ブレード](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> MongoDB 接続文字列を取得してカスタマイズする
1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Cosmos DB]** ブレードで、MongoDB アカウントの API を選択します。 
3. [アカウント] ブレードの左側のウィンドウで、**[接続文字列]** をクリックします。 
4. **[接続文字列]** ブレードが開きます。 MongoDB のドライバーを使用してこのアカウントに接続するために必要なすべての情報 (あらかじめ構築された接続文字列も含む) が表示されます。

    ![[接続文字列] ブレード](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>接続文字列の要件
> [!Important]
> Azure Cosmos DB には、厳密なセキュリティ要件と基準があります。 Azure Cosmos DB アカウントでは、*SSL* による認証とセキュリティで保護された通信が要求されます。 
>
>

Azure Cosmos DB は、標準の MongoDB 接続文字列の URI 形式をサポートしていますが、固有の要件がいくつかあります。たとえば、Azure Cosmos DB アカウントでは、SSL による認証とセキュリティで保護された通信が要求されます。 そのため接続文字列は次の形式となります。

    mongodb://username:password@host:port/[database]?ssl=true

この文字列の値は、先ほど示した **[接続文字列]** ブレードで確認できます。

* ユーザー名 (必須) : Azure Cosmos DB アカウント名。
* パスワード (必須) : Azure Cosmos DB アカウントのパスワード。
* ホスト (必須) : Azure Cosmos DB アカウントの FQDN。
* ポート (必須) : 10255。
* データベース (省略可) : 接続で使用されるデータベース。 データベースを指定しない場合、既定のデータベースは "test" です。
* ssl=true (必須)

たとえば、**[接続文字列]** ブレードに表示されているアカウントでは次のようになります。 有効な接続文字列は次のようになります。

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>次の手順
* MongoDB アカウント用の Azure Cosmos DB API で [ Studio 3T (MongoChef) を使用する](mongodb-mongochef.md)方法を説明します。
* [サンプル](mongodb-samples.md)を表示して MongoDB 用 の Azure Cosmos DB API を調べます。
