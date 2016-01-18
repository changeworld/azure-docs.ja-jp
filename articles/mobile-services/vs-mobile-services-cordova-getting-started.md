<properties
	pageTitle="Cordova モバイル サービス プロジェクトの概要 (Visual Studio 接続済みサービス) | Microsoft Azure"
	description="Visual Studio 接続済みサービスを使用して Azure Mobile Services に Cordova プロジェクトを接続した後の最初の手順について説明します。"
	services="mobile-services"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="tarcher"/>

# Mobile Services の使用 (Cordova プロジェクト)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

##最初の手順
これらの例で使用されているコードを実行するために行う必要がある最初のステップは、接続しているモバイル サービスの種類によります。

- JavaScript バックエンド モバイル サービスの場合は、TodoItem と呼ばれるテーブルを作成します。テーブルを作成するには、サーバー エクスプローラーの Azure ノード下でモバイル サービスを特定し、そのモバイル サービスのノードを右クリックしてコンテキスト メニューを開き、[**Create Table (テーブルの作成)**] を選択します。テーブル名として「TodoItem」と入力します。

- .NET バックエンド モバイル サービスの場合は、TodoItem テーブルは Visual Studio によって既にデフォルトのプロジェクト テンプレート内に作成されていますが、これを Azure に発行する必要があります。発行するには、ソリューション エクスプローラーでモバイル サービス プロジェクトのコンテキスト メニューを開き、[**Publish Web (Web の発行)**] を選択します。既定値を受け入れ、**[Publish (発行)]** を選択します。

##テーブルへの参照を作成する

次のコードは、TodoItem のデータを含むテーブルへの参照を取得します。この後でデータ テーブルの読み取りと更新の操作を実行する際に、TodoItem のデータを使用できます。モバイル サービスを作成すると、TodoItem テーブルが自動的に作成されます。

    var todoTable = mobileServiceClient.getTable('TodoItem');

これらの例を使用するには、テーブルのアクセス許可を **[アプリケーション キーを持つユーザー]** に設定する必要があります。後で、認証を設定できます。詳細については、「[Add authentication to your Mobile Services app (Mobile Services アプリケーションに認証を追加する)](mobile-services-html-get-started-users.md)」を参照してください。

##テーブルに項目を追加する

新しい項目をデータ テーブルに挿入します。ID (文字列型の GUID) が新しい行のプライマリ キーとして自動的に作成されます。返された **Promise** オブジェクト上の [done](https://msdn.microsoft.com/library/dn802826.aspx) メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            items.push(item)
        });
    };

##テーブルの読み取りまたはクエリを実行する

次のコードは、テーブルに対してテキスト フィールドでソートされたすべての項目を照会します。コードを追加して、success ハンドラーでクエリ結果を処理できます。この場合、項目のローカル配列が更新されます。

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
        });

where メソッドを使用してクエリを変更できます。次の例では、完了した項目を除外します。

    todoTable.where(function () {
            return (this.complete === false);
        })
        .read().done(function (results) {
            items = results.slice();
        });

使用できる他のクエリ例については、「[query オブジェクト](http://msdn.microsoft.com/library/azure/jj613353.aspx)」を参照してください。

##テーブル項目を更新する

データ テーブルの行を更新します。このコードでは、モバイル サービスが応答すると、項目は一覧から削除されます。返された **Promise** オブジェクト上の [done](https://msdn.microsoft.com/library/dn802826.aspx) メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

##テーブル項目を削除する

**del** メソッドを使用してデータ テーブルの行を削除します。返された **Promise** オブジェクト上の [done](https://msdn.microsoft.com/library/dn802826.aspx) メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

[モバイル サービスの詳細を確認する](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0107_2016-->