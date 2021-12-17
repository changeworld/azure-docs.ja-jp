---
title: .NET V4 SDK を使用して Azure Cosmos DB SQL API リソースを管理する
description: .NET V4 SDK を使用して Azure Cosmos DB SQL API アカウント リソースを管理するコンソール アプリを構築するには、このクイックスタートをご使用ください。
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/26/2021
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.openlocfilehash: 7468f2a615c52c6d18f93eebead58b9b28f6fb08
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117963"
---
# <a name="quickstart-build-a-console-app-by-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>クイックスタート: .NET V4 SDK (プレビュー) を使用して Azure Cosmos DB SQL API アカウント リソースを管理するコンソール アプリを構築する
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3 コネクタ](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

.NET 用の Azure Cosmos DB SQL API クライアント ライブラリの概要について説明します。 この記事の手順に従って、.NET V4 (Azure.Cosmos) パッケージをインストールし、アプリをビルドしてください。 その後、Azure Cosmos DB の格納データに対する基本的な CRUD (Create、Read、Update、Delete) 操作のコード例を試します。

> [!IMPORTANT]
> Azure Cosmos DB 用の .NET V4 SDK は現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することは推奨されません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Cosmos DB は、あらゆる規模に対応する、オープン API を備えた Microsoft の高速 NoSQL データベースです。 Azure Cosmos DB を使用すると、キー/値データベース、ドキュメント データベース、およびグラフ データベースをすばやく作成し、クエリを実行できます。 .NET 用の Azure Cosmos DB SQL API クライアント ライブラリを使用して、次のことを行います。

* Azure Cosmos データベースとコンテナーを作成する。
* コンテナーにサンプル データを追加する。
* データにクエリを実行する。 
* データベースを削除します。

[ライブラリのソース コード](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 [無料で作成できます](https://azure.microsoft.com/free/)。 Azure サブスクリプションを必要とせず、課金や契約もなく [Azure Cosmos DB を試す](https://azure.microsoft.com/try/cosmosdb/)ことができます。 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core)。 `dotnet --version` を実行すると、お使いの環境で使用可能なバージョンを確認できます。

## <a name="set-up"></a>設定

このセクションでは、Azure Cosmos アカウントを作成し、.NET 用 Azure Cosmos DB SQL API クライアント ライブラリを使用してリソースを管理するプロジェクトを設定する手順について説明します。 

この記事で説明するコード例では `FamilyDatabase` データベースを作成し、そのデータベース内にファミリー メンバーを作成します。 各ファミリー メンバーは項目になっていて、`Id`、`FamilyName`、`FirstName`、`LastName`、`Parents`、`Children`、`Address` などのプロパティを備えています。 `LastName` プロパティは、コンテナーのパーティション キーとして使用されます。 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Azure Cosmos アカウントを作成する

[[Azure Cosmos DB を無料で試す]](https://azure.microsoft.com/try/cosmosdb/) オプションを使用して Azure Cosmos アカウントを作成する場合は、種類が **SQL API** の Azure Cosmos アカウントを作成する必要があります。 Azure Cosmos テスト アカウントは既に作成されています。 アカウントを明示的に作成する必要はないため、このセクションをスキップして次のセクションに進むことができます。

独自の Azure サブスクリプションを所有しているか、または無料のサブスクリプションを作成した場合は、Azure Cosmos アカウントを明示的に作成する必要があります。 次のコードでは、セッションの整合性を持つ Azure Cosmos アカウントを作成します。 アカウントは `South Central US` と `North Central US` でレプリケートされます。  

Azure Cloud Shell を使用して、Azure Cosmos アカウントを作成できます。 Azure Cloud Shell は、Azure リソースを管理するための、ブラウザーでアクセスできる対話形式の認証されたシェルです。 Bash または PowerShell どちらかのシェル エクスペリエンスを作業方法に合わせて柔軟に選択できます。 

このクイックスタートでは Bash を使用します。 Azure Cloud Shell ではストレージ アカウントも必要です。 これはプロンプトが表示されたら作成できます。

1. 次のコードの横にある **[試してみる]** ボタンを選択し、 **[Bash]** モードを選択します。 **[ストレージ アカウントを作成する]** を選択して Cloud Shell にサインインします。 

1. 次のコードをコピーして Azure Cloud Shell に貼り付けて実行します。 Azure Cosmos アカウント名はグローバルに一意である必要があります。必ず `mysqlapicosmosdb` 値を更新してからコマンドを実行してください。

   ```azurecli-interactive

   # Set variables for the new SQL API account, database, and container
   resourceGroupName='myResourceGroup'
   location='southcentralus'

   # The Azure Cosmos account name must be globally unique, so be sure to update the `mysqlapicosmosdb` value before you run the command
   accountName='mysqlapicosmosdb'

   # Create a resource group
   az group create \
       --name $resourceGroupName \
       --location $location

   # Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
   az cosmosdb create \
       --resource-group $resourceGroupName \
       --name $accountName \
       --kind GlobalDocumentDB \
       --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
       --default-consistency-level "Session" \
       --enable-multiple-write-locations true

   ```

Azure Cosmos アカウントの作成にはしばらく時間がかかります。 操作に成功すると、確認出力が表示されます。 [Azure portal](https://portal.azure.com/) にサインインし、指定した名前の Azure Cosmos アカウントが存在することを確認します。 リソースの作成後に、[Azure Cloud Shell] ウィンドウを閉じることができます。 

### <a name="create-a-net-app"></a><a id="create-dotnet-core-app"></a>.NET アプリを作成する

好みのエディターまたは IDE で、.NET アプリケーションを作成します。 ローカル コンピューターから Windows コマンド プロンプトまたはターミナル ウィンドウを開きます。 次のセクションではコマンド プロンプトまたはターミナルからすべてのコマンドを実行します。  

次の `dotnet new` コマンドを実行して、`todo` という名前のアプリを作成します。 `--langVersion` パラメーターによって、作成されたプロジェクト ファイル内に `LangVersion` プロパティが設定されます。

   ```bash
   dotnet new console --langVersion:8 -n todo
   ```

次のコマンドを使用して、新しく作成したアプリ フォルダーにディレクトリを変更し、アプリケーションをビルドします。

   ```bash
   cd todo
   dotnet build
   ```

ビルドからの予想される出力は、次のようになります。

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Azure Cosmos DB パッケージをインストールする

アプリケーション ディレクトリ内で、`dotnet add package` コマンドを使用して .NET Core 用の Azure Cosmos DB クライアント ライブラリをインストールします。

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Azure portal から Azure Cosmos アカウントの資格情報をコピーする

サンプル アプリケーションは、Azure Cosmos アカウントに対する認証を行う必要があります。 認証するには、Azure Cosmos アカウントの資格情報をアプリケーションに渡します。 次の手順に従って、Azure Cosmos アカウントの資格情報を取得します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 自分の Azure Cosmos アカウントにアクセスします。

1. **[キー]** ペインを開き、アカウントの **[URI]** と **[プライマリ キー]** の値をコピーします。 次の手順では、URI とキーの値を環境変数に追加します。

## <a name="learn-the-object-model"></a><a id="object-model"></a>オブジェクト モデルについて学習する

アプリケーションのビルドを続行する前に、Azure Cosmos DB のリソースの階層と、これらのリソースの作成とアクセスに使用されるオブジェクト モデルについて説明します。 Azure Cosmos DB によって、次の順序でリソースが作成されます。

* Azure Cosmos アカウント 
* データベース 
* Containers 
* アイテム

エンティティの階層について詳しくは、「[Azure Cosmos DB リソース モデル](../account-databases-containers-items.md)」の記事を参照してください。 これらのリソースとやり取りするには、以下の .NET クラスを使用します。

* `CosmosClient`. このクラスは、Azure Cosmos DB サービスのクライアント側の論理表現を提供します。 このクライアント オブジェクトは、サービスに対する要求の構成と実行に使用されます。
* `CreateDatabaseIfNotExistsAsync`. このメソッドによって、非同期操作としてデータベース リソースが (存在しない場合は) 作成されるか、(既に存在する場合は) 取得されます。 
* `CreateContainerIfNotExistsAsync`. このメソッドによって、非同期操作としてコンテナーが (存在しない場合は) 作成されるか、(既に存在する場合は) 取得されます。 応答から状態コードを確認して、コンテナーが新しく作成された (201) か、既存のコンテナーが返された (200) かを判断できます。 
* `CreateItemAsync`. このメソッドによって、コンテナー内に項目が作成されます。
* `UpsertItemAsync`. このメソッドでは、項目がまだ存在しない場合、コンテナー内に項目が作成され、既に存在する場合、項目が置換されます。 
* `GetItemQueryIterator`. このメソッドにより、SQL ステートメントとパラメーター化された値を利用して Azure Cosmos データベースのコンテナーの下で項目に対するクエリが作成されます。 
* `DeleteAsync`. このメソッドは、指定されたデータベースを Azure Cosmos アカウントから削除します。

 ## <a name="configure-code-examples"></a><a id="code-examples"></a>コード例を構成する

この記事で説明されているサンプル コードでは、Azure Cosmos DB でファミリ データベースを作成します。 ファミリー データベースには、名前、住所、場所、親、子供、ペットなどのファミリーの詳細が含まれています。 

データを Azure Cosmos アカウントに設定する前に、ファミリ項目のプロパティを定義します。 サンプル アプリケーションのルート レベルに `Family.cs` という名前の新しいクラスを作成し、次のコードを追加します。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives-and-define-the-client-object"></a>using ディレクティブを追加してクライアント オブジェクトを定義する

プロジェクト ディレクトリからエディターで *Program.cs* ファイルを開き、アプリケーションの先頭に次の `using` ディレクティブを追加します。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


`Program` クラスに、次のグローバル変数を追加します。 これらの変数には、エンドポイント キーと承認キー、データベースの名前、作成するコンテナーが格納されます。 環境に応じて、エンドポイント キーと承認キーの値を必ず置き換えてください。 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

最後に、`Main` メソッドを置き換えます。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>データベースを作成する 

`program.cs` クラス内に `CreateDatabaseAsync` メソッドを定義します。 このメソッドによって、`FamilyDatabase` データベースが作成されます (まだ存在しない場合)。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>コンテナーを作成する

`Program` クラス内に `CreateContainerAsync` メソッドを定義します。 このメソッドによって、`FamilyContainer` コンテナーが作成されます (まだ存在しない場合)。 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>項目を作成する

次のコードを使用して、`AddItemsToContainerAsync` メソッドを追加してファミリ項目を作成します。 `CreateItemAsync` または `UpsertItemAsync` のメソッドを使用し、項目を作成できます。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>項目に対してクエリを実行する

項目を挿入した後、クエリを実行して Andersen ファミリの詳細を取得できます。 次のコードは、SQL クエリを直接使用してクエリを実行する方法を示しています。 Andersen ファミリーの詳細を取得する SQL クエリは `SELECT * FROM c WHERE c.LastName = 'Andersen'` です。 `Program` クラス内で `QueryItemsAsync` メソッドを定義し、次のコードを追加します。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>項目を置換する 

次のコードで `ReplaceFamilyItemAsync` メソッドを追加して、ファミリー項目を読み取り、それを更新します。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>項目を削除する 

次のコードで `DeleteFamilyItemAsync` メソッドを追加して、ファミリー項目を削除します。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>データベースを削除する 

次のコードを含んだ `DeleteDatabaseAndCleanupAsync` メソッドを追加することで、データベースを削除できます。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

必要なメソッドをすべて追加したら、*Program.cs* ファイルを保存します。 

## <a name="run-the-code"></a>コードの実行

アプリケーションを実行して、Azure Cosmos DB リソースを作成します。

   ```bash
   dotnet run
   ```

アプリケーションを実行すると、次の出力が生成されます。

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

データが作成されたことを確認するには、Azure portal にサインインし、Azure Cosmos アカウントで必要な項目を確認します。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Cosmos アカウントとそれに対応するリソース グループが不要になったら、Azure CLI または Azure PowerShell を使用してそれらを削除できます。 次のコマンドは、Azure CLI を使用してリソース グループを削除する方法を示しています。

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos アカウントを作成し、.NET Core アプリを使用してデータベースとコンテナーを作成する方法を説明しました。 これで、次の記事の指示に従って Azure Cosmos アカウントに追加のデータをインポートできるようになりました。 

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](../import-data.md)
