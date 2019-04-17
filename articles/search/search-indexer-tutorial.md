---
title: チュートリアル:C# サンプル コードで Azure SQL データベースのデータにインデックスを付ける - Azure Search
description: C# コード例で、Azure SQL データベースに接続し、検索可能なデータを抽出して、それを Azure Search インデックスに読み込む方法を示します。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 401ad90f1ae4ffb4915a0b51aea41430e7045aa9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270465"
---
# <a name="tutorial-in-c-crawl-an-azure-sql-database-using-azure-search-indexers"></a>C# のチュートリアル: Azure Search インデクサーを使用して Azure SQL データベースをクロールする

検索可能なデータをサンプル Azure SQL データベースから抽出するためのインデクサーを構成する方法について説明します。 [インデクサー](search-indexer-overview.md)は、外部データ ソースをクロールして[検索インデックス](search-what-is-an-index.md)にデータを投入する Azure Search のコンポーネントです。 Azure SQL Database のインデクサーは、すべてのインデクサーの中で最も広く使用されています。 

インデクサーを構成することによって、記述すべきコードや保守すべきコードの量が少なくて済むため、そのスキルは身に付けておいて損はありません。 スキーマに準拠した JSON データセットを作成して投入する代わりに、インデクサーをデータ ソースにアタッチし、データをインデクサーで抽出してインデックスに挿入することができるほか、必要に応じてインデクサーを定期実行し、基になるソースの変更を反映することもできます。

このチュートリアルでは、[Azure Search .NET クライアント ライブラリ](https://aka.ms/search-sdk)と .NET Core コンソール アプリケーションを使用して次のタスクを実行します。

> [!div class="checklist"]
> * Search サービスの情報をアプリケーション設定に追加する
> * 外部データセットを Azure SQL Database に用意する 
> * サンプル コードでインデックスとインデクサーの定義を確認する
> * インデクサーのコードを実行してデータをインポートする
> * インデックスを検索する
> * インデクサーの構成をポータルで確認する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のサービス、ツール、およびデータを使用します。 

[Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) は、インデクサーによって使用される外部データ ソースを格納します。 サンプル ソリューションでは、SQL データ ファイルを入力することにってテーブルを作成しています。 このチュートリアルでは、サービスとデータベースを作成するための手順を説明しています。

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)。サンプル ソリューションを実行するために、任意のエディションを使用できます。 サンプル コードと手順については、無料の Community エディションでテストされています。

Azure サンプル GitHub リポジトリの [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) に、サンプル ソリューションが用意されています。 そのソリューションをダウンロードして展開します。 既定では、ソリューションは読み取り専用です。 ソリューションを右クリックし、読み取り専用属性をオフにして、ファイルを変更できるようにします。

> [!Note]
> 無料の Azure Search サービスを使用している場合、インデックス、インデクサー、データ ソースの数は、いずれも 3 つまでに制限されます。 このチュートリアルでは、それぞれ 1 つずつ作成します。 ご利用のサービスに、新しいリソースを作成できるだけの空き領域があることを確認してください。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1.. **[設定]** > **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

![HTTP エンドポイントとアクセス キーを取得する](media/search-fiddler/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="set-up-connections"></a>接続を設定する
必要なサービスへの接続情報は、ソリューションの **appsettings.json** ファイルで指定します。 

1. Visual Studio で、**DotNetHowToIndexers.sln** ファイルを開きます。

1. 各設定を指定できるように、ソリューション エクスプローラーで **appsettings.json** を開きます。  

最初の 2 つのエントリは、Azure Search サービスの URL と管理者キーを使用して、今すぐ指定できます。 `https://mydemo.search.windows.net` のエンドポイントの場合、指定するサービス名は `mydemo` です。

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

最後のエントリには、既存のデータベースを指定する必要があります。 これは、次の手順で作成します。

## <a name="prepare-sample-data"></a>サンプル データの準備

この手順では、インデクサーがクロールできる外部データ ソースを作成します。 Azure Portal とサンプルの *hotels.sql* ファイルを使用して、Azure SQL Database にデータセットを作成することができます。 Azure Search で使用されるのは、ビューやクエリから生成されるようなフラット化された行セットです。 サンプル ソリューションの SQL ファイルでは、単一のテーブルを作成してデータを投入します。

次の演習では、サーバーもデータベースも存在していないことを想定しています。どちらも手順 2. で作成することになります。 既にリソースがある場合には、hotels テーブルをそこに追加して、手順 4. から始めることができます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。 

2. データベース、サーバー、およびリソース グループを作成する **Azure SQL Database** を見つけるか作成します。 既定値および一番低い価格レベルを使用してかまいません。 サーバーを作成する利点は、後の手順でテーブルを作成して読み込むために必要な管理者ユーザー名とパスワードを指定できることです。

   ![[新しいデータベース] ページ](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. **[作成]** をクリックして、新しいサーバーとデータベースをデプロイします。 サーバーとデータベースがデプロイされるまで待ちます。

4. 新しいデータベースの [SQL Database] ページをまだ開いていない場合は開きます。 リソース名は *SQL Server* ではなく "*SQL データベース*" になっている必要があります。

   ![[SQL Database] ページ](./media/search-indexer-tutorial/hotels-db.png)

4. コマンド バーの **[ツール]** > **[クエリ エディター]** をクリックします。

5. **[ログイン]** をクリックし、サーバー管理者のユーザー名とパスワードを入力します。

6. **[クエリを開く]** をクリックし、*hotels.sql* の場所に移動します。 

7. ファイルを選択し、**[開く]** をクリックします。 このスクリプトは次のスクリーンショットのようになります。

   ![SQL スクリプト](./media/search-indexer-tutorial/sql-script.png)

8. **[実行]** をクリックしてクエリを実行します。 3 つの行について、クエリが正常に実行されたことを示すメッセージが [結果] ウィンドウに表示されます。

9. このテーブルから行セットが返されるようにするには、検証ステップとして次のクエリを実行します。

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    クエリ エディターでは、典型的なクエリである `SELECT * FROM Hotels` が正しく動作しません。 サンプル データでは、Location フィールドに地理座標が格納されていますが、このフィールドが現時点のエディターでは処理されません。 クエリ対象の他の列の一覧については、次のステートメントを実行できます:  `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. これで外部データセットが揃ったので、データベースの ADO.NET 接続文字列をコピーします。 データベースの [SQL Database] ページで **[設定]** > **[接続文字列]** に移動し、ADO.NET 接続文字列をコピーします。
 
    ADO.NET 接続文字列は次のようになります。有効なデータベース名、ユーザー名、パスワードに置き換えて使用してください。

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Visual Studio で **appsettings.json** ファイルの 3 つ目のエントリとして、接続文字列を "AzureSqlConnectionString" に貼り付けます。

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>コードの理解

以上でコードをビルドして実行する準備が整いました。 その前に、このサンプルで使用するインデックスとインデクサーの定義を詳しく見てみましょう。 関連するコードは次の 2 つのファイルにあります。

  + **hotel.cs**: インデックスを定義するスキーマが含まれています。
  + **Program.cs**: サービスの構造を作成したり管理したりするための関数が含まれています。

### <a name="in-hotelcs"></a>hotel.cs の内容

インデックス スキーマは、フィールドのコレクションを定義します。この定義には、次の HotelName のフィールドの定義に見られるように、フィールドがフルテキスト検索可能かどうか、フィルター処理可能かどうか、並べ替え可能かどうかなど、許可される操作を指定する属性も含まれています。 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

その他にも、スキーマにはさまざまな要素が存在します。検索スコアを高めるためのスコア付けプロファイルやカスタム アナライザーなど、各種コンストラクトがその例です。 ただし、このドキュメントの目的上、スキーマに含まれている定義はごくわずかで、サンプル データセットに含まれているフィールドのみがその構成要素となります。

このチュートリアルでは、インデクサーで単一のデータ ソースからデータをプルしています。 実際には、同じインデックスに複数のインデクサーをアタッチして、さまざまなデータ ソースとインデクサーから統合された検索可能なインデックスを作成することができます。 必要に応じて柔軟に、データ ソースだけを変えながら同じインデックス/インデクサー ペアを使用したり、インデクサーとデータ ソースの組み合わせを変えながら 1 つのインデックスを使用したりすることができます。

### <a name="in-programcs"></a>Program.cs の内容

メイン プログラムには、3 つの代表的なデータ ソースすべての関数が含まれています。 Azure SQL Database にのみ注目すると、次のオブジェクトが存在します。

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

Azure Search では、個別に表示、構成、または削除できるオブジェクトとして、インデックス、インデクサー、データ ソース (それぞれ *hotels*、*azure-sql-indexer*、*azure-sql*) があります。 

*AzureSqlHighWaterMarkColumnName* 列には、特に注目してください。この列には変更検出情報が格納されます。インデクサーは、この情報を使用して、前回のインデックス作成ワークロード以降に行が変更されているかどうかを判断します。 [変更検出ポリシー](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)はインデクサーでのみサポートされ、データ ソースによって異なります。 Azure SQL Database では、データベースの要件に応じて 2 つのポリシーから選ぶことができます。

次のコードは、Program.cs 内のメソッドを示しています。データ ソースとインデクサーを作成する際に使用されます。 このコードは、読者がこのプログラムを繰り返し実行する可能性を考慮し、同じ名前のリソースが既に存在しているかどうかを調べて、削除します。

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

インデクサーの API 呼び出しは、[DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) (呼び出すクローラーの種類を指定する) を除き、プラットフォームに依存しないことに注目してください。

## <a name="run-the-indexer"></a>インデクサーを実行する

この手順では、プログラムをコンパイルして実行します。 

1. ソリューション エクスプローラーで **[DotNetHowToIndexers]** を右クリックし、**[ビルド]** を選択します。
2. もう一度 **[DotNetHowToIndexers]** を右クリックし、**[デバッグ]** > **[新しいインスタンスを開始]** の順に選択します。

プログラムがデバッグ モードで実行されます。 コンソール ウィンドウで各操作の状態が報告されます。

  ![SQL スクリプト](./media/search-indexer-tutorial/console-output.png)

コードは、Visual Studio からローカルで実行され、Azure 上の Search サービスに接続します。次に、このサービスが接続文字列を使用して Azure SQL Database に接続し、データセットを取得します。 このように多くの操作が伴うため、障害点となり得る箇所は複数存在しますが、エラーが発生した場合は、まず次の状況を確認してください。

+ 指定する Search サービスの接続情報は、このチュートリアルのサービス名に限定されます。 完全な URL を入力した場合、インデックスの作成時に接続エラーで操作が停止します。

+ **appsettings.json** のデータベース接続情報。 これは、ポータルから取得した ADO.NET 接続文字列に、実際のデータベースの有効なユーザー名とパスワードを反映したものであることが必要です。 ユーザー アカウントには、データを取得するためのアクセス許可が必要です。

+ リソース制限。 既に述べたように、共有 (無料) のサービスは、インデックス、インデクサー、データ ソースがいずれも 3 つまでに制限されています。 上限に達した場合、新しいオブジェクトを作成できなくなります。

## <a name="search-the-index"></a>インデックスを検索する 

Azure Portal にアクセスし、Search サービスの [概要] ページで、一番上の **[Search エクスプローラー]** をクリックし、新しいインデックスに対するクエリをいくつか送信します。

1. 一番上にある **[インデックスの変更]** をクリックして *hotels* インデックスを選択します。

2. **[検索]** ボタンをクリックして空の検索を実行します。 

   インデックスの 3 つのエントリが JSON ドキュメントとして返されます。 Search エクスプローラーは、構造全体が見えるようにドキュメントを JSON 形式で返します。

3. 次に、検索文字列として「`search=river&$count=true`」を入力します。 

   このクエリは、`river` という語についてフルテキスト検索を呼び出すもので、その結果には、一致したドキュメントの件数が含まれます。 一致したドキュメントの件数は、インデックスが大きく数千から数百万のドキュメントが含まれている場合のテストで役立ちます。 今回のケースで、このクエリに一致するドキュメントは 1 件だけです。

4. 最後に、JSON 出力を必要なフィールドに限定するため、検索文字列として「`search=river&$count=true&$select=hotelId, baseRate, description`」を入力します。 

   クエリの応答が選択フィールドに制限され、より簡潔な出力内容が得られます。

## <a name="view-indexer-configuration"></a>インデクサーの構成を確認する

ポータルには、先ほどプログラムで作成したものも含むすべてのインデクサーが一覧表示されます。 インデクサーの定義を開いてそのデータ ソースを確認したり、新たに追加された行や変更された行を取得するための更新スケジュールを構成したりすることができます。

1. Azure Search サービスの [サービスの概要] ページを開きます。
2. 下へスクロールして **[インデクサー]** と **[データ ソース]** のタイルを探します。
3. タイルをクリックして各リソースの一覧を表示します。 個々のインデクサーまたはデータ ソースを選択して、構成設定を表示したり変更したりすることができます。

   ![インデクサーとデータ ソースのタイル](./media/search-indexer-tutorial/tiles-portal.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの後で最も速くクリーンアップする方法は、Azure Search サービスが含まれているリソース グループを削除することです。 リソース グループを削除することで、そのすべての内容を完全に削除することができます。 ポータルでは、リソース グループ名は Azure Search サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次の手順

インデクサー パイプラインには、AI を活用したアルゴリズムをアタッチすることができます。 引き続き次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Blob Storage 内のドキュメントのインデックスを作成する](search-howto-indexing-azure-blob-storage.md)