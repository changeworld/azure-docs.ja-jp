---
title: Azure Cosmos DB の MongoDB 用 API と .NET SDK を使用して Web API を構築する
description: 記載されている .NET のコード サンプルを使用すると、Azure Cosmos DB の MongoDB 用 API を使用して接続とクエリを行うことができます。
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 8/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: b528804385b9b47f6c586b49f2a33a4c688753b2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250200"
---
# <a name="quickstart-build-a-net-web-api-using-azure-cosmos-dbs-api-for-mongodb"></a>クイック スタート: Azure Cosmos DB の MongoDB 用 API を使用して .NET Web API を構築する
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

このクイックスタートでは、次の方法を示します。
1. [MongoDB 用 Azure Cosmos DB API アカウント](mongodb-introduction.md)を作成する 
2. [MongoDB .NET ドライバー](https://docs.mongodb.com/ecosystem/drivers/csharp/)を使用して、製品カタログ Web API を構築する
3. サンプル データをインポートする

## <a name="prerequisites-to-run-the-sample-app"></a>サンプル アプリを実行するための前提条件

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)
* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料の Azure アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 Azure サブスクリプションを必要とせず、課金や契約もなく [Azure Cosmos DB を試す](https://azure.microsoft.com/try/cosmosdb/)ことができます。

まだ Visual Studio を持っていない場合は、 **[ASP.NET および Web の開発]** ワークロードがセットアップと共にインストールされた状態で [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードします。

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

## <a name="install-the-sample-app-template"></a>サンプル アプリ テンプレートをインストールする

このサンプルは、ローカル コピーを作成するためにインストールできる dotnet プロジェクト テンプレートです。 コマンド ウィンドウで次のコマンドを実行します。

```bash
mkdir "C:\cosmos-samples"
cd "C:\cosmos-samples"
dotnet new -i Microsoft.Azure.Cosmos.Templates
dotnet new cosmosmongo-webapi
```

上のコマンドでは以下の操作が行われます。

1. サンプル用の *C:\cosmos-samples* ディレクトリを作成します。 ご利用のオペレーティング システムに適したフォルダーを選択します。
1. 現在のディレクトリを *C:\cosmos-samples* フォルダーに変更します。
1. プロジェクト テンプレートをインストールし、dotnet CLI からグローバルに使用できるようにします。
1. プロジェクト テンプレートを使用してローカル サンプル アプリを作成します。

dotnet CLI を使用しない場合は、[プロジェクト テンプレートを ZIP ファイルとしてダウンロードする](https://github.com/Azure/azure-cosmos-dotnet-templates)こともできます。 このサンプルは、`Templates/APIForMongoDBQuickstart-WebAPI` フォルダーにあります。

## <a name="review-the-code"></a>コードの確認

以下の手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認してください。 そうでない場合は、「[アプリケーションの設定を更新する](#update-the-application-settings)」に進んでください。

### <a name="setup-connection"></a>接続のセットアップ

次のスニペットは、*Services/MongoService.cs* ファイルからの抜粋です。

* 次のクラスはクライアントを表し、.NET フレームワークによって、それを使用するサービスに挿入されます。

    ```cs
        public class MongoService
        {
            private static MongoClient _client;

            public MongoService(IDatabaseSettings settings)
            {
                _client = new MongoClient(settings.MongoConnectionString);
            }

            public MongoClient GetClient()
            {
                return _client;
            }
        }
    ```

### <a name="setup-product-catalog-data-service"></a>製品カタログ データ サービスをセットアップする

次の各スニペットは、*Services/ProductService.cs* ファイルからの抜粋です。

* 次のコードにより、データベースとコレクションが取得され、まだ存在しない場合は作成されます。

    ```csharp
    private readonly IMongoCollection<Product> _products;        

    public ProductService(MongoService mongo, IDatabaseSettings settings)
    {
        var db = mongo.GetClient().GetDatabase(settings.DatabaseName);
        _products = db.GetCollection<Product>(settings.ProductCollectionName);
    }
    ```

* 次のコードにより、SKU (一意の製品識別子) でドキュメントが取得されます。

    ```csharp
    public Task<Product> GetBySkuAsync(string sku)
    {
        return _products.Find(p => p.Sku == sku).FirstOrDefaultAsync();
    }
    ```

* 次のコードにより、製品が作成され、コレクションに挿入されます。

    ```csharp
    public Task CreateAsync(Product product)
    {
        _products.InsertOneAsync(product);
    }
    ```

* 次のコードにより、製品が検索され、更新されます。

    ```csharp
    public Task<Product> UpdateAsync(Product update)
    {
        return _products.FindOneAndReplaceAsync(
            Builders<Product>.Filter.Eq(p => p.Sku, update.Sku), 
            update, 
            new FindOneAndReplaceOptions<Product> { ReturnDocument = ReturnDocument.After });
    }
    ```

    同様に、[collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html) メソッドを使用すると、ドキュメントを削除できます。

## <a name="update-the-application-settings"></a>アプリケーションの設定を更新する

Azure portal から接続文字列情報をコピーします。

1. [Azure portal](https://portal.azure.com/) で、Cosmos DB アカウントを選択し、左側のナビゲーションで **[接続文字列]** を選択して、 **[読み取り/書き込みキー]** を選択します。 次の手順で、画面右側のコピー ボタンを使用して、プライマリ接続文字列を *appsettings.json* ファイルにコピーします。

2. *appsettings.json* ファイルを開きます。

3. ポータルから (コピー ボタンを使用して) **プライマリ接続文字列** の値をコピーし、**appsettings.json** ファイルの **DatabaseSettings.MongoConnectionString** プロパティの値に設定します。

4. **appsettings.json** ファイルの **DatabaseSettings.DatabaseName** プロパティ内の **データベース名** の値を確認します。

5. **appsettings.json** ファイルの **DatabaseSettings.ProductCollectionName** プロパティ内の **コレクション名** の値を確認します。

> [!WARNING]
> パスワードや他の機密データをソース コードに入れないでください。

これで、Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。

## <a name="load-sample-data"></a>サンプル データを読み込む

[mongoimport](https://docs.mongodb.com/database-tools/mongoimport/#mongodb-binary-bin.mongoimport) を[ダウンロードします](https://www.mongodb.com/try/download/database-tools)。これは、少量の JSON、CSV、または TSV データを簡単にインポートする CLI ツールです。 mongoimport を使用して、このプロジェクトの `Data` フォルダーに用意されているサンプル製品データを読み込みます。

Azure portal から接続情報をコピーし、以下のコマンドに入力します。 

```bash
mongoimport --host <HOST>:<PORT> -u <USERNAME> -p <PASSWORD> --db cosmicworks --collection products --ssl --jsonArray --writeConcern="{w:0}" --file Data/products.json
```

1. [Azure portal](https://portal.azure.com/) で、MongoDB 用の Azure Cosmos DB API アカウントを選択し、左側のナビゲーションで **[接続文字列]** を選択して、 **[読み取り/書き込みキー]** を選択します。 

1. ポータルから (コピー ボタンを使用して) **HOST** の値をコピーし、 **\<HOST\>** の代わりに入力します。

1. ポータルから (コピー ボタンを使用して) **PORT** の値をコピーし、 **\<PORT\>** の代わりに入力します。

1. ポータルから (コピー ボタンを使用して) **USERNAME** の値をコピーし、 **\<USERNAME\>** の代わりに入力します。

1. ポータルから (コピー ボタンを使用して) **PASSWORD** の値をコピーし、 **\<PASSWORD\>** の代わりに入力します。

1. **データベース名** の値を確認し、`cosmicworks` 以外のものを作成した場合は更新します。

1. **コレクション名** の値を確認し、`products` 以外のものを作成した場合は更新します。

> [!Note]
> この手順をスキップする場合は、この Web API プロジェクトの一部として提供される POST エンドポイントを使用して、適切なスキーマを持つドキュメントを作成できます。

## <a name="run-the-app"></a>アプリを実行する

Visual Studio から Ctrl + F5 キーを押して、アプリを実行します。 既定のブラウザーがアプリと共に起動されます。

CLI を使用する場合は、コマンド ウィンドウで次のコマンドを実行して、サンプル アプリを起動します。 また、このコマンドを実行すると、プロジェクトの依存関係がインストールされ、プロジェクトがビルドされますが、ブラウザーは自動的に起動されません。

```bash
dotnet run
```

アプリケーションが実行された後、`https://localhost:5001/swagger/index.html` に移動して、Web API の [Swagger ドキュメント](https://swagger.io/)を確認し、サンプルの要求を送信します。

テストする API を選択し、[Try it out]\(試してみる\) を選択します。

:::image type="content" source="./media/create-mongodb-dotnet/try-swagger.png" alt-text="Web API Swagger UI の API エンドポイント試行ページのスクリーンショット。":::

必要なパラメーターを入力し、[Execute]\(実行\) を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイックスタートでは、MongoDB 用 API アカウントを作成し、コードを使用してデータベースとコレクションを作成し、Web API アプリを実行する方法を学習しました。 これで、データベースに追加のデータをインポートできます。 

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB 容量計画ツールを使用した要求ユニットに見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください

> [!div class="nextstepaction"]
> [MongoDB データを Azure Cosmos DB にインポートする](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
