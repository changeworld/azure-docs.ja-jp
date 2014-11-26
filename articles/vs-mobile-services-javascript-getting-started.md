<properties title="Mobile Services の使用" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Getting Started (概要)][Getting Started (概要)]
> -   [変更内容][変更内容]

## Mobile Services の使用

これらの例で使用されているコードを実行するために行う必要がある最初のステップは、接続しているモバイル サービスの種類によります。

JavaScript バックエンド モバイル サービスの場合は、TodoItem と呼ばれるテーブルを作成します。テーブルを作成するには、サーバー エクスプローラーの Azure ノード下でモバイル サービスを特定し、そのモバイル サービスのノードを右クリックしてコンテキスト メニューを開き、[**Create Table (テーブルの作成)**] を選択します。テーブル名として「TodoItem」と入力します。

.NET バックエンド モバイル サービスの場合は、TodoItem テーブルは Visual Studio によって既にデフォルトのプロジェクト テンプレート内に作成されていますが、これを Azure に発行する必要があります。発行するには、ソリューション エクスプローラーでモバイル サービス プロジェクトのコンテキスト メニューを開き、[**Publish Web (Web の発行)**] を選択します。既定値を受け入れ、**[Publish (発行)]** を選択します。

##### テーブルへの参照を取得する

クライアント オブジェクトは既にプロジェクトに追加されています。このオブジェクトは、モバイル サービスの名前に "Client" を追加した名前になります。次のコードは、TodoItem のデータを含むテーブルへの参照を取得します。この後でデータ テーブルの読み取りと更新の操作を実行する際に、TodoItem のデータを使用できます。

    var todoTable = yourMobileServiceClient.getTable('TodoItem');

##### エントリを追加する

新しい項目をデータ テーブルに挿入します。ID (文字列型の GUID) が新しい行のプライマリ キーとして自動的に作成されます。Mobile Services のインフラストラクチャが ID 列を使用するため、ID 列の型を変更しないでください。

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

##### テーブルを読み取る/照会する

次のコードは、テーブルのすべての項目を照会し、ローカル コレクションを更新して、その結果を UI 要素の listItems にバインドします。

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

where メソッドを使用してクエリを変更できます。次の例では、完了した項目を除外します。

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

使用できる他のクエリ例については、「[query オブジェクト][query オブジェクト]」を参照してください。

##### エントリを更新する

データ テーブルの行を更新します。この例では、todoItem は更新済みの項目です。また、項目は、モバイル サービスから返されるものと同じ項目です。モバイル サービスが応答するときに、[splice][splice] メソッドを使用して、ローカルの todoItems リスト内の項目を更新します。返された [Promise][Promise] オブジェクト上の [done][Promise] メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

##### エントリを削除する

データ テーブルの行を削除します。返された [Promise][Promise] オブジェクト上の [done][Promise] メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

    todoTable.delete(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
    }

[モバイル サービスの詳細を確認する][モバイル サービスの詳細を確認する]

  [Getting Started (概要)]: /documentation/articles/vs-mobile-services-javascript-getting-started/
  [変更内容]: /documentation/articles/vs-mobile-services-javascript-what-happened/
  [query オブジェクト]: http://msdn.microsoft.com/library/azure/jj613353.aspx
  [splice]: http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx
  [Promise]: 
  [モバイル サービスの詳細を確認する]: http://azure.microsoft.com/documentation/services/mobile-services/
