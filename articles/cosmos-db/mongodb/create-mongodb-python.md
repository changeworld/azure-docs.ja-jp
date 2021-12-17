---
title: MongoDB と Python 用の Azure Cosmos DB API を使ってみる
description: Azure Cosmos DB の MongoDB 用 API を使用して接続とクエリを行うことができる Python のコード サンプルについて説明します。
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 10/22/2021
ms.openlocfilehash: af59ce8dc6fa3fc4a2f62e3bc00e185cb8be1bbf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271307"
---
# <a name="quickstart-get-started-using-azure-cosmos-db-api-for-mongodb-and-python"></a>クイックスタート: MongoDB と Python 用の Azure Cosmos DB API を使ってみる
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

この[クイックスタート](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started)では、次の方法を示します。
1. [MongoDB 用 Azure Cosmos DB API アカウント](mongodb-introduction.md)を作成する 
2. PyMongo を使用してアカウントに接続する
3. サンプル データベースとコレクションを作成する
4. サンプル コレクションで CRUD 操作を実行する

## <a name="prerequisites-to-run-the-sample-app"></a>サンプル アプリを実行するための前提条件

* [Python](https://www.python.org/downloads/) 3.9 以上 (この記事で説明されている[サンプル コード](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started)を、この推奨バージョンを使用して実行することをお勧めします。 ただし、古いバージョンの Python 3 で動作する可能性があります。)
* ご自分のコンピューターにインストールされた [PyMongo](https://pypi.org/project/pymongo/)

<a id="create-account"></a>
## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount](../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="learn-the-object-model"></a>オブジェクト モデルについて学習する

アプリケーションのビルドを続行する前に、MongoDB 用 API のリソースの階層と、これらのリソースの作成とアクセスに使用されるオブジェクト モデルについて説明します。 MongoDB 用 API によって、次の順序でリソースが作成されます。

* MongoDB 用 Azure Cosmos DB API アカウント
* データベース 
* コレクション 
* ドキュメント

エンティティの階層について詳しくは、「[Azure Cosmos DB リソース モデル](../account-databases-containers-items.md)」の記事を参照してください。

## <a name="get-the-code"></a>コードを取得する

[リポジトリから](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started)サンプル Python コードをダウンロードするか、git clone を使用します。

```shell
git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-python-getting-started
```

## <a name="retrieve-your-connection-string"></a>接続文字列の取得

サンプル コードを実行する場合は、MongoDB 用 API アカウントの接続文字列を入力する必要があります。 次の手順に従って、検索します。

1. [Azure portal](https://portal.azure.com/) で、自分の Cosmos DB アカウントを選択します。

2. 左側のナビゲーションで **[接続文字列]** を選択して、 **[読み取り/書き込みキー]** を選択します。 画面の右側にある [コピー] ボタンを使ってプライマリ接続文字列をコピーします。

> [!WARNING]
> パスワードや他の機密データをソース コードに入れないでください。


## <a name="run-the-code"></a>コードの実行

```shell
python run.py
```

## <a name="understand-how-it-works"></a>動作を理解する

### <a name="connecting"></a>接続

次のコードでは、ユーザーに接続文字列の入力を求めるメッセージが表示されます。 接続文字列をコードに含めるのは、それを使用するすべてのユーザーがデータベースに対して読み書きを行えるので、決して良い方法ではありません。

```python
CONNECTION_STRING = getpass.getpass(prompt='Enter your primary connection string: ') # Prompts user for connection string
```

次のコードでは、MongoDB 用 API のクライアント接続を作成し、それが有効かテストします。

```python
client = pymongo.MongoClient(CONNECTION_STRING)
try:
    client.server_info() # validate connection string
except pymongo.errors.ServerSelectionTimeoutError:
    raise TimeoutError("Invalid API for MongoDB connection string or timed out when attempting to connect")
```

### <a name="resource-creation"></a>リソースの作成
次のコードでは、CRUD 操作の実行に使用されるサンプル データベースとコレクションを作成します。 プログラムを使用してリソースを作成する場合は、MongoDB 用 API 拡張機能コマンド (以下を参照) を使用することをお勧めします。これらのコマンドには、リソース スループット (RU/秒) を設定し、シャーディングを構成する機能があるためです。 

リソースの暗黙的な作成は機能しますが、既定ではスループットの推奨値に設定され、シャード化されません。

```python
# Database with 400 RU throughput that can be shared across the DB's collections
db.command({'customAction': "CreateDatabase", 'offerThroughput': 400})
```

```python
 # Creates a unsharded collection that uses the DBs shared throughput
db.command({'customAction': "CreateCollection", 'collection': UNSHARDED_COLLECTION_NAME})
```

### <a name="writing-a-document"></a>ドキュメントの記述
次の例では、サンプル全体で引き続き使用するサンプル ドキュメントを挿入します。 後続の操作でクエリするために、その固有の _id field 値を取得します。

```python
"""Insert a sample document and return the contents of its _id field"""
document_id = collection.insert_one({SAMPLE_FIELD_NAME: randint(50, 500)}).inserted_id
```

### <a name="readingupdating-a-document"></a>ドキュメントの読み取りまたは更新
次では、以前に挿入したドキュメントに対してクエリ、更新、そして再度クエリを実行しています。

```python
print("Found a document with _id {}: {}".format(document_id, collection.find_one({"_id": document_id})))

collection.update_one({"_id": document_id}, {"$set":{SAMPLE_FIELD_NAME: "Updated!"}})
print("Updated document with _id {}: {}".format(document_id, collection.find_one({"_id": document_id})))
```

### <a name="deleting-a-document"></a>ドキュメントを削除する
最後に、作成したドキュメントをコレクションから削除します。
```python
"""Delete the document containing document_id from the collection"""
collection.delete_one({"_id": document_id})
```

## <a name="next-steps"></a>次のステップ
このクイックスタートでは、MongoDB 用 API アカウントを作成し、コードを使用してデータベースとコレクションを作成し、CRUD 操作を実行する方法を学習しました。 

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB 容量計画ツールを使用した要求ユニットに見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください

> [!div class="nextstepaction"]
> [MongoDB データを Azure Cosmos DB にインポートする](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
