<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [作業の開始](/documentation/articles/vs-mobile-services-cordova-getting-started/)
> - [変更内容](/documentation/articles/vs-mobile-services-cordova-what-happened/)

## Mobile Services の使用 (Cordova プロジェクト)

これらの例で使用されているコードを実行するために行う必要がある最初のステップは、接続しているモバイル サービスの種類によります。

JavaScript バックエンド モバイル サービスの場合は、TodoItem と呼ばれるテーブルを作成します。テーブルを作成するには、サーバー エクスプローラーの Azure ノード下でモバイル サービスを特定し、そのモバイル サービスのノードを右クリックしてコンテキスト メニューを開き、**[Create Table (テーブルの作成)]** を選択します。テーブル名として「TodoItem」と入力します。

.NET バックエンド モバイル サービスの場合は、TodoItem テーブルは Visual Studio によって既に既定のプロジェクト テンプレート内に作成されていますが、これを Azure に発行する必要があります。発行するには、ソリューション エクスプローラーでモバイル サービス プロジェクトのコンテキスト メニューを開き、**[Publish Web (Web の発行)]** を選択します。既定値を受け入れ、**[Publish (発行)]** を選択します。
  
>[AZURE.NOTE]**Visual Studio 2015 Preview を使用して構築されている Cordova プロジェクトでは、この[回避策](http://go.microsoft.com/fwlink/?LinkId=518765)を使用して Azure Mobile Services を実行します。Visual Studio 2015 の以降のバージョンを使用するプロジェクトでは、回避策は必要ありません。**

#####テーブルへの参照を取得する

次のコードは、TodoItem のデータを含むテーブルへの参照を取得します。この後でデータ テーブルの読み取りと更新の操作を実行する際に、TodoItem のデータを使用できます。モバイル サービスを作成すると、TodoItem テーブルが自動的に作成されます。

	var todoTable = mobileServiceClient.getTable('TodoItem');

これらの例を使用するには、テーブルのアクセス許可を **[アプリケーション キーを持つユーザー]** に設定する必要があります。後で、認証を設定できます。詳細については、「[Add authentication to your Mobile Services app (Mobile Services アプリケーションに認証を追加する)](http://azure.microsoft.com/documentation/articles/mobile-services-html-get-started-users/)」をご覧ください。

#####エントリを追加する 

新しい項目をデータ テーブルに挿入します。ID (文字列型の GUID) が新しい行のプライマリ キーとして自動的に作成されます。返された [Promise]() オブジェクトの [done]() メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

#####テーブルを読み取る/照会する 

次のコードは、テーブルに対してテキスト フィールドでソートされたすべての項目を照会します。コードを追加して、success ハンドラーでクエリ結果を処理できます。この場合、項目のローカル配列が更新されます。

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
            });
        });

where メソッドを使用してクエリを変更できます。次の例では、完了した項目を除外します。

	todoTable.where(function () {
                 return (this.complete === false);
              })
             .read().done(function (results) {
                items = results.slice();
             });

使用できる他のクエリ例については、「[query]((http://msdn.microsoft.com/library/azure/jj613353.aspx)) オブジェクト」をご覧ください。

#####エントリを更新する

データ テーブルの行を更新します。このコードでは、モバイル サービスが応答すると、項目は一覧から削除されます。返された [Promise]() オブジェクトの [done]() メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

#####エントリを削除する

**del** メソッドを使用してデータ テーブルから行を削除します。返された [Promise]() オブジェクトの [done]() メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

	todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
	});

[モバイル サービスの詳細を確認する](http://azure.microsoft.com/documentation/services/mobile-services/)

<!--HONumber=42-->
