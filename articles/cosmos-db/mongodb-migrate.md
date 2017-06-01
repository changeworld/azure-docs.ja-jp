---
title: "Azure Cosmos DB の MongoDB 用 API で mongoimport と mongorestore を使用する | Microsoft Docs"
description: "mongoimport と mongorestore を使用して、MongoDB 用 API アカウントにデータをインポートする方法について説明します"
keywords: mongoimport, mongorestore
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: ab4fb39f8784a2d6aa956056a30649aab1d08487
ms.contentlocale: ja-jp
ms.lasthandoff: 05/31/2017


---

# <a name="azure-cosmos-db-how-to-import-mongodb-data"></a>Azure Cosmos DB: MongoDB データをインポートする方法 

MongoDB API で使用するために MongoDB から Azure Cosmos DB アカウントにデータを移行するには、次の操作を行う必要があります。

* *mongoimport.exe* または *mongorestore.exe* のいずれかを [MongoDB Download Center](https://www.mongodb.com/download-center) からダウンロードします。
* [MongoDB 用 API 接続文字列](connect-mongodb-account.md)を取得します。

MongoDB からデータをインポートして DocumentDB API で使用する予定の場合は、データ移行ツールを使用してデータをインポートする必要があります。 詳細については、[データ移行ツール](import-data.md)に関するページを参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * 接続文字列の取得
> * mongoimport を使用した MongoDB データのインポート
> * mongorestore を使用した MongoDB データのインポート

## <a name="prerequisites"></a>前提条件

* スループットを上げる: データの移行にかかる時間は、コレクションに対して設定したスループットの量に依存します。 大規模なデータ移行では、スループットが上がっていることを確認します。 移行が完了したら、コストを節約するためにスループットを下げます。 [Azure Portal](https://portal.azure.com) でスループットを上げることの詳細については、[Azure Cosmos DB のパフォーマンス レベルと価格レベル](performance-levels.md)に関するページを参照してください。

* SSL を有効にする: Azure Cosmos DB には、厳密なセキュリティ要件と基準が存在します。 アカウントを操作するときは、SSL が有効になっていることを確認してください。 この記事で説明する手順の中に、*mongoimport* と *mongorestore*で SSL を有効にする方法が含まれています。

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>接続文字列情報 (ホスト、ポート、ユーザー名、およびパスワード) を見つける

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**[Azure Cosmos DB]** エントリをクリックします。
2. **[サブスクリプション]** ウィンドウで、自分のアカウント名を選択します。
3. **[接続文字列]** ブレードで、**[接続文字列]** をクリックします。  
右側のウィンドウに、自分のアカウントに正常に接続するために必要なすべての情報が表示されます。

    ![[接続文字列] ブレード](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-api-for-mongodb-with-mongoimport"></a>mongoimport を使用した MongoDB 用 API へのデータのインポート

Azure Cosmos DB アカウントにデータをインポートするには、次のテンプレートを使用してインポートを実行します。 *ホスト*、*ユーザー名*および*パスワード*には、自分のアカウントに固有の値を入力します。  

テンプレート:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

例:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-api-for-mongodb-with-mongorestore"></a>mongorestore を使用した MongoDB 用 API へのデータのインポート

MongoDB 用 API アカウントにデータを復元するには、次のテンプレートを使用してインポートを実行します。 *ホスト*、*ユーザー名*および*パスワード*には、自分のアカウントに固有の値を入力します。

テンプレート:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

例:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * 接続文字列の取得
> * mongoimport を使用した MongoDB データのインポート
> * mongorestore を使用した MongoDB データのインポート

次のチュートリアルに進み、Azure Cosmos DB を使用して MongoDB データにクエリを実行する方法を学ぶことができます。 

> [!div class="nextstepaction"]
>[MongoDB データにクエリを実行する方法](../cosmos-db/tutorial-query-mongodb.md)

