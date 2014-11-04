<properties title="Mobile Services の使用" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Getting Started (概要)][Getting Started (概要)]
> -   [変更内容][変更内容]

## Mobile Services の使用 (.NET プロジェクト)

これらの例で使用されているコードを実行するために行う必要がある最初のステップは、接続しているモバイル サービスの種類によります。

JavaScript バックエンド モバイル サービスの場合は、TodoItem と呼ばれるテーブルを作成します。テーブルを作成するには、サーバー エクスプローラーの Azure ノード下でモバイル サービスを特定し、そのモバイル サービスのノードを右クリックしてコンテキスト メニューを開き、[**Create Table (テーブルの作成)**] を選択します。テーブル名として「TodoItem」と入力します。

.NET バックエンド モバイル サービスの場合は、TodoItem テーブルは Visual Studio によって既にデフォルトのプロジェクト テンプレート内に作成されていますが、これを Azure に発行する必要があります。発行するには、ソリューション エクスプローラーでモバイル サービス プロジェクトのコンテキスト メニューを開き、[**Publish Web (Web の発行)**] を選択します。既定値を受け入れ、**[Publish (発行)]** を選択します。

##### テーブルへの参照を取得する

次のコードは、TodoItem のデータを含むテーブルへの参照を取得します。この後でデータ テーブルの読み取りと更新の操作を実行する際に、TodoItem のデータを使用できます。TodoItem クラスに属性を設定して、モバイル サービスがクエリへの応答として送信する JSON を解釈できるようにする必要があります。

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

    IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

このコードは、**アプリケーション キーを持つユーザー**に対するアクセス許可がテーブルに設定されている場合に機能します。モバイル サービスの安全を確保するためにアクセス許可を変更する場合、ユーザー認証サポートを追加する必要があります。詳細については、「[Add authentication to your Mobile Services app (Mobile Services アプリケーションに認証を追加する)][Add authentication to your Mobile Services app (Mobile Services アプリケーションに認証を追加する)]」を参照してください。

##### エントリを追加する

新しい項目をデータ テーブルに挿入します。

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

##### テーブルを読み取る/照会する

次のコードは、テーブルのすべての項目を照会します。テーブルは、データの最初のページ (既定で 50 項目) しか返さないことに注意してください。オプション パラメーターを使用して、必要なページ サイズを渡すことができます。

    List<TodoItem> items;
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToListAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }

##### エントリを更新する

データ テーブルの行を更新します。パラメーター項目は、更新対象の TodoItem オブジェクトです。

    await todoTable.UpdateAsync(item);

##### エントリを削除する

データベースの行を削除します。パラメーター項目は、削除対象の TodoItem オブジェクトです。

    await todoTable.DeleteAsync(item);

[モバイル サービスの詳細を確認する][モバイル サービスの詳細を確認する]

  [Getting Started (概要)]: /documentation/articles/vs-mobile-services-dotnet-getting-started/
  [変更内容]: /documentation/articles/vs-mobile-services-dotnet-what-happened/
  [Add authentication to your Mobile Services app (Mobile Services アプリケーションに認証を追加する)]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
  [モバイル サービスの詳細を確認する]: http://azure.microsoft.com/documentation/services/mobile-services/
