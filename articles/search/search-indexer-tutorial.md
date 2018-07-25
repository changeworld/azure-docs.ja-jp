---
title: Azure Search で Azure SQL データベースのインデックスを作成するチュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure SQL データベースをクロールして検索可能なデータを抽出し、Azure Search インデックスを作成します。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: b40d3a74904d6814eb01b5d41d10632e8c9af5be
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988797"
---
# <a name="tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>チュートリアル: Azure Search インデクサーを使用して Azure SQL データベースをクロールする

このチュートリアルでは、検索可能なデータをサンプル Azure SQL データベースから抽出するためのインデクサーを構成する方法について説明します。 [インデクサー](search-indexer-overview.md)は、外部データ ソースをクロールして[検索インデックス](search-what-is-an-index.md)にデータを投入する Azure Search のコンポーネントです。 Azure SQL データベースのインデクサーは、すべてのインデクサーの中で最も広く使用されています。 

インデクサーを構成することによって、記述すべきコードや保守すべきコードの量が少なくて済むため、そのスキルは身に付けておいて損はありません。 スキーマに準拠した JSON データセットを作成して投入する代わりに、インデクサーをデータ ソースにアタッチし、データをインデクサーで抽出してインデックスに挿入することができるほか、必要に応じてインデクサーを定期実行し、基になるソースの変更を反映することもできます。

このチュートリアルでは、[Azure Search .NET クライアント ライブラリ](https://aka.ms/search-sdk)と .NET Core コンソール アプリケーションを使用して次のタスクを実行します。

> [!div class="checklist"]
> * ソリューションをダウンロードして構成する
> * Search サービスの情報をアプリケーション設定に追加する
> * 外部データセットを Azure SQL Database に用意する 
> * サンプル コードでインデックスとインデクサーの定義を確認する
> * インデクサーのコードを実行してデータをインポートする
> * インデックスを検索する
> * インデクサーの構成をポータルで確認する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure Search サービス。 その設定方法については、[Search サービスの作成](search-create-service-portal.md)に関するページを参照してください。

* Azure SQL データベース。インデクサーの外部データ ソースとして使用されます。 サンプル ソリューションでは、SQL データ ファイルを入力することにってテーブルを作成しています。

* Visual Studio 2017。 無料の [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) を使用できます。 

> [!Note]
> 無料の Azure Search サービスを使用している場合、インデックス、インデクサー、データ ソースの数は、いずれも 3 つまでに制限されます。 このチュートリアルでは、それぞれ 1 つずつ作成します。 ご利用のサービスに、新しいリソースを作成できるだけの空き領域があることを確認してください。

## <a name="download-the-solution"></a>ソリューションをダウンロードする

このチュートリアルで使用するインデクサー ソリューションは、1 つのマスター ダウンロードで提供される Azure Search サンプルのコレクションに含まれるものです。 このチュートリアルで使用するソリューションは *DotNetHowToIndexers* です。

1. Azure サンプル GitHub リポジトリの [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started) にアクセスします。

2. **[Clone or Download]\(複製またはダウンロード\)** > **[Download ZIP]\(ZIP のダウンロード\)** をクリックします。 既定では、ダウンロード フォルダーにファイルが格納されます。

3. **エクスプローラー** > **[ダウンロード]** でファイルを右クリックし、**[すべて展開]** を選択します。

4. 読み取り専用アクセス許可をオフにします。 フォルダー名を右クリックし、**[プロパティ]** > **[全般]** の順に選択して、現在のフォルダー、サブフォルダー、およびファイルに対する **[読み取り専用]** 属性をオフにします。

5. **Visual Studio 2017** で、*DotNetHowToIndexers.sln* ソリューションを開きます。

6. **ソリューション エクスプローラー**で、最上位ノードの親ソリューションを右クリックし、**[NuGet パッケージの復元]** を選択します。

## <a name="set-up-connections"></a>接続を設定する
必要なサービスへの接続情報は、ソリューションの **appsettings.json** ファイルで指定します。 

このチュートリアルの手順に従って各設定を入力できるよう、ソリューション エクスプローラーで **appsettings.json** を開いてください。  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>Search サービスの名前と管理者の API キーを取得する

Search サービスのエンドポイントとキーは、ポータルから入手できます。 サービスの操作を行うには、キーが必要となります。 インデックスやインデクサーといった、サービス内のオブジェクトを作成したり削除したりするうえで必要な書き込みアクセスは管理者キーによって得られます。

1. [Azure Portal](https://portal.azure.com/) にサインインし、[ご自分のサブスクリプションの Search サービス](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)を探します。

2. サービス ページを開きます。

3. 最上部のメイン ページでサービス名を確認します。 次のスクリーンショットでは、*azs-tutorial* となっています。

   ![サービス名](./media/search-indexer-tutorial/service-name.png)

4. これをコピーし、Visual Studio で **appsettings.json** の 1 つ目のエントリとして貼り付けます。

  > [!Note]
  > サービス名は、search.windows.net を含んだエンドポイントの構成要素となります。 興味がある方は、[概要] ページの **[要点]** で完全な URL を確認できます。 この URL は、https://your-service-name.search.windows.net のようになります。

5. 左側の **[設定]** > **[キー]** で、いずれかの管理者キーをコピーし、**appsettings.json** に 2 つ目のエントリとして貼り付けます。 キーは、プロビジョニング時にサービスに対して生成される英数字の文字列で、サービス操作への承認済みアクセスに必要となります。 

  両方の設定が追加されたファイルは、次の例のようになります。

  ```json
  {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
  }
  ```

## <a name="prepare-an-external-data-source"></a>外部データ ソースを準備する

この手順では、インデクサーがクロールできる外部データ ソースを作成します。 このチュートリアルのデータ ファイルは、\DotNetHowToIndexers ソリューション フォルダーに格納されている *hotels.sql* です。 

### <a name="azure-sql-database"></a>Azure SQL Database

Azure Portal とサンプルの *hotels.sql* ファイルを使用して、Azure SQL Database にデータセットを作成することができます。 Azure Search で使用されるのは、ビューやクエリから生成されるようなフラット化された行セットです。 サンプル ソリューションの SQL ファイルでは、単一のテーブルを作成してデータを投入します。

次の演習では、サーバーもデータベースも存在していないことを想定しています。どちらも手順 2. で作成することになります。 既にリソースがある場合には、hotels テーブルをそこに追加して、手順 4. から始めることができます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。 

2. **[リソースの作成]** > **[SQL Database]** の順にクリックして、データベース、サーバー、およびリソース グループを作成します。 既定値および一番低い価格レベルを使用してかまいません。 サーバーを作成する利点は、後の手順でテーブルを作成して読み込むために必要な管理者ユーザー名とパスワードを指定できることです。

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
   クエリ エディターでは、典型的なクエリである `SELECT * FROM Hotels` が正しく動作しません。 サンプル データでは、Location フィールドに地理座標が格納されていますが、このフィールドが現時点のエディターでは処理されません。 その他の一連の列を照会するには、`SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')` ステートメントを実行できます。

10. これで外部データセットが揃ったので、データベースの ADO.NET 接続文字列をコピーします。 データベースの [SQL Database] ページで **[設定]** > **[接続文字列]** に移動し、ADO.NET 接続文字列をコピーします。
 
  ADO.NET 接続文字列は次のようになります。有効なデータベース名、ユーザー名、パスワードに置き換えて使用してください。

  ```sql
  Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  ```
11. Visual Studio で **appsettings.json** ファイルの 3 つ目のエントリとして、接続文字列を "AzureSqlConnectionString" に貼り付けます。

    ```json
    {
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>インデックスとインデクサーのコードを理解する

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
> [Azure Blob Storage 内ドキュメントのインデックスを Azure Search で作成する](search-howto-indexing-azure-blob-storage.md)