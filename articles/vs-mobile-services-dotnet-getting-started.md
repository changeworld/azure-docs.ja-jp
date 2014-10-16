<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 処理内容

###### リファレンスの追加

プロジェクトに Azure Mobile Services の Nuget パッケージが追加されました。その結果、次の .NET 参照がプロジェクトに追加されました。Microsoft.WindowsAzure.Mobile、Microsoft.WindowsAzure.Mobile.Ext、Newtonsoft.Json、System.Net.Http.Extensions、System.Net.Http.Primitives

###### Mobile Services 用の接続文字列の値

選択したモバイル サービスのアプリケーション URL とアプリケーション キーを使用して、App.xaml.cs ファイル内に MobileServiceClient オブジェクトが作成されました。

### Mobile Services の使用

###### テーブルへの参照を取得する

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

###### エントリを追加する

新しい項目をデータ テーブルに挿入します。

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

###### テーブルを読み取る/照会する

次のコードは、テーブルのすべての項目を照会します。テーブルは、データの最初のページ (既定で 50 項目) しか返さないことに注意してください。オプション パラメーターを使用して、必要なページ サイズを渡すことができます。

    List<TodoItem> items = await todoTable.ToListAsync();
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToCollectionAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }

###### エントリを更新する

データ テーブルの行を更新します。パラメーター項目は、更新対象の TodoItem オブジェクトです。

    await todoTable.UpdateAsync(item);

###### エントリを削除する

データベースの行を削除します。パラメーター項目は、削除対象の TodoItem オブジェクトです。

    await todoTable.DeleteAsync(item);

  [Add authentication to your Mobile Services app (Mobile Services アプリケーションに認証を追加する)]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
