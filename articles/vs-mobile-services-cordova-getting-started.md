<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 処理内容

### リファレンスの追加

すべてのマルチデバイス ハイブリッド アプリケーションに含まれる Azure Mobile Service のクライアント プラグインが有効になりました。

### Mobile Services 用の接続文字列の値

services\\mobileServices\\settings に、MobileServiceClient を利用する新しい JavaScript (.js) ファイルが生成されました。選択したモバイル サービスのアプリケーション URL とアプリケーション キーが含まれています。このファイルには、次のようなモバイル サービス クライアント オブジェクトの初期化コードが含まれています。

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );

### Mobile Services の使用

###### テーブルへの参照を取得する

次のコードは、TodoItem のデータを含むテーブルへの参照を取得します。この後でデータ テーブルの読み取りと更新の操作を実行する際に、TodoItem のデータを使用できます。モバイル サービスを作成すると、TodoItem テーブルが自動的に作成されます。

    var todoTable = mobileServiceClient.getTable('TodoItem');

これらの例を使用するには、テーブルのアクセス許可を **[アプリケーション キーを持つユーザー]** に設定する必要があります。後で、認証を設定できます。詳細については、「[Add authentication to your Mobile Services app (Mobile Services アプリケーションに認証を追加する)][Add authentication to your Mobile Services app (Mobile Services アプリケーションに認証を追加する)]」を参照してください。

###### エントリを追加する

新しい項目をデータ テーブルに挿入します。ID (文字列型の GUID) が新しい行のプライマリ キーとして自動的に作成されます。返された [Promise][Promise] オブジェクト上の [done][Promise] メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

###### テーブルを読み取る/照会する

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

使用できる他のクエリ例については、「[query オブジェクト][query オブジェクト]」を参照してください。

###### エントリを更新する

データ テーブルの行を更新します。このコードでは、モバイル サービスが応答すると、項目は一覧から削除されます。返された [Promise][Promise] オブジェクト上の [done][Promise] メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

###### エントリを削除する

del メソッドを使用してデータ テーブルを削除します。返された [Promise]() オブジェクト上の [done]() メソッドを呼び出し、挿入されたオブジェクトのコピーを取得して、エラーがあれば処理します。

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

  [Add authentication to your Mobile Services app (Mobile Services アプリケーションに認証を追加する)]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-html-get-started-users/
  
  [query オブジェクト]: (http://msdn.microsoft.com/library/azure/jj613353.aspx)
