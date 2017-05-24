---
title: "Table API を使用した Azure Cosmos DB .NET アプリケーションの構築 | Microsoft Docs"
description: ".NET を使用した Azure Cosmos DB の Table API の概要"
services: cosmosdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 73b9448153ec520d77afd1bdb65b9694e7bf7b9e
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: Table API を使用した .NET アプリケーションの構築

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。 

このクイック スタートでは、Azure Cosmos DB アカウントを作成する方法と、Azure Portal を使用してそのアカウント内にテーブルを作成する方法を説明します。 さらに、エンティティを挿入、更新、および削除するコードと、いくつかのクエリを実行するコードを作成します。 [Azure Storage Preview SDK](https://aka.ms/premiumtablenuget) は NuGet からダウンロードできます。これには、公開されている [Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage) と同じクラスおよびメソッド シグネチャが含まれます。ただし、[Table API](table-introduction.md) (プレビュー) を使用して Azure Cosmos DB アカウントに接続する機能もあります。 

## <a name="prerequisites"></a>前提条件

まだ Visual Studio 2017 をインストールしていない場合は、**無料**の [Visual Studio 2017 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmosdb-create-dbaccount-table.md)]

## <a name="add-a-table"></a>テーブルの追加

[!INCLUDE [cosmosdb-create-table](../../includes/cosmosdb-create-table.md)]

## <a name="add-sample-data"></a>サンプル データの追加

これで、データ エクスプローラーを使用して、新しいテーブルにデータを追加できます。

1. データ エクスプローラーで、**sample-database**、**sample-table** の順に展開し、**[エンティティ]**、**[Add Entity] (エンティティの追加)** の順にクリックします。
2. PartitionKey 値ボックスと RowKey 値ボックスにデータを追加し、**[Add Entity] (エンティティの追加)** をクリックします。

   ![Azure Portal のデータ エクスプローラーで新しいドキュメントを作成する](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
    これで、テーブルにさらにエンティティを追加したり、エンティティを編集したり、データ エクスプローラーでデータをクエリしたりできます。 データ エクスプローラーでは、スループットを拡張し、ストアド プロシージャ、ユーザー定義関数、およびトリガーをテーブルに追加することもできます。

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

github から DocumentDB API アプリを複製し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. git ターミナル ウィンドウ (git bash など) を開き、`cd` を実行して作業ディレクトリに移動します。  

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. 次に、Visual Studio でソリューション ファイルを開きます。 

## <a name="review-the-code"></a>コードの確認

アプリで何が行われているかを簡単に確認してみましょう。 DocumentDBRepository.cs ファイルを開くと、これらのコード行によって、Azure Cosmos DB リソースが作成されることがわかります。 

* DocumentClient が初期化されます。

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* 新しいデータベースが作成されます。

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* 新しいグラフ コンテナーが作成されます。

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```

## <a name="update-your-connection-string"></a>接続文字列の更新

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure Portal](http://portal.azure.com/) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[キー]** をクリックし、**[読み取り/書き込みキー]** をクリックします。 次の手順では、画面の右側のコピー ボタンを使用して、URI とプライマリ キーを app.config ファイルにコピーします。

    ![Azure Portal の [キー] ブレードでアクセス キーを表示およびコピーする](./media/create-documentdb-dotnet-core/keys.png)

2. Visual Studio で app.config ファイルを開きます。 

3. ポータルから Azure Cosmos DB アカウント名をコピーして、app.config 内の PremiumStorageConnection 文字列値の AccountName の値に設定します。 上記のスクリーンショットでは、アカウント名は cosmos-db-quickstart です。 自分のアカウント名はポータルの上部に表示されます。

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMODB.documents.azure.com" />`

4. 次に、ポータルから PRIMARY KEY 値をコピーし、PremiumStorageConnectionString の AccountKey の値に設定します。 

    `AccountKey=AUTHKEY`

5. 最後に、ポータルの [キー] ページから (コピー ボタンを使用して) URI 値をコピーし、PremiumStorageConnectionString の TableEndpoint の値に設定します。

    `TableEndpoint=https://COSMODB.documents.azure.com`

    StandardStorageConnectionString はそのまま使用できます。

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

## <a name="run-the-web-app"></a>Web アプリの実行

1. Visual Studio の**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 

2. NuGet の**[Browse] (参照)** ボックスに、「*WindowsAzure.Storage*」と入力し、**[Includes prerelease] (プレリリース版を含める)** ボックスをオンにします。 

3. 結果から、**WindowsAzure.Storage** ライブラリをインストールします。 これにより、プレビュー Azure Cosmos DB Table API パッケージとすべての依存関係がインストールされます。

4. Ctrl + F5 キーを押してアプリケーションを実行します。

    コンソール ウィンドウには、テーブルに追加されているデータが表示されます。 スクリプトが完了したら、コンソール ウィンドウを閉じます。 

これで、データ エクスプローラーに戻って、この新しいデータの表示、クエリ、変更、操作を行うことができます。 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、Azure Portal でこのクイック スタートで作成したすべてのリソースを削除してください。 

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックし、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してテーブルを作成し、アプリを実行する方法を説明しました。  これで、Table API を使用して、データをクエリできます。  

> [!div class="nextstepaction"]
> [Table API を使用したクエリ](tutorial-query-table.md)


