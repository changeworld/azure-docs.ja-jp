<properties
	pageTitle="Mobile Services でのオフライン データ同期の使用 (iOS) | Microsoft Azure"
	description="Azure Mobile Services を使用して、iOS アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="krisragh;donnam"/>

# Mobile Services でのオフライン データの同期の使用

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

オフラインの同期を使用すれば、ネットワーク接続が得られない状況でも、モバイル アプリケーションでデータを表示、追加、または変更することができます。このチュートリアルでは、アプリケーションで変更内容を自動的にオフライン データベースに格納し、オンラインに戻るたびにそれらの変更内容を同期させる方法について説明します。

オフライン同期には、次のような複数の利点があります。

* サーバー データをデバイスにローカルでキャッシュすることにより、アプリケーションの応答性を向上させる。
* 断続的なネットワーク接続に対してアプリケーションに弾力性を持たせる。
* 接続がほとんどまたはまったく得られない状況でもデータを作成し変更できる。
* 複数のデバイス間でデータを同期させる。
* 同じレコードが 2 つのデバイスによって変更されたときに競合を検出する。

> [AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、[無料 Mobile Services を入手できます。このサービスは評価終了後も使用できます](http://azure.microsoft.com/pricing/details/mobile-services/)。詳細については、「Azure の無料試用版サイト」(http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28 target=”\_blank”) を参照してください。

最初に、このチュートリアルの基になっている [Mobile Services のクイック スタート チュートリアル]を完了しておく必要があります。まず、クイック スタートで説明したオフラインの同期に関連するコードを確認してみましょう。

## <a name="review-sync"></a>Mobile Services 同期コードのレビュー

ネットワークにアクセスできない場合、エンドユーザーは Azure Mobile Services スのオフライン同期により、ローカル データベースとやり取りできるようになります。アプリケーションでこれらの機能を使用するには、`MSClient` の同期コンテキストを初期化して、ローカル ストアを参照します。その後、`MSSyncTable` インターフェイスを使用してテーブルを参照します。

* **QSTodoService.m** で、メンバー `syncTable` の種類が `MSSyncTable` であることを確認します。オフラインの同期では、これを、`MSTable` の代わりに使用します。同期テーブルが使用されると、すべての操作はローカル ストアを参照し、明示的なプッシュ操作とプル操作を使用したリモート サービスとのみ同期されます。

```
		@property (nonatomic, strong)   MSSyncTable *syncTable;
```

同期テーブルへの参照を取得するには、メソッド `syncTableWithName` を使用します。オフライン同期機能を解除するには、`tableWithName` を使用します。

* **QSTodoService.m** では、テーブルの操作が実行される前に、`QSTodoService.init` でローカル ストアが初期化されます。

```
		MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
		self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
```

これにより、`MSCoreDataStore` インターフェイスを使用してローカル ストアが作成されます。`MSSyncContextDataSource` プロトコルを実装することで、別のローカル ストアを提供することもできます。

`initWithDelegate` の最初のパラメーターでは、競合のハンドラーを指定します。しかし、`nil` を渡したので、取得されるのは既定の競合ハンドラーとなります。この場合、競合が発生すると失敗します。カスタム競合ハンドラーを実装する方法の詳細についてには、[Mobile Services のオフライン サポートでの競合の処理]を参照してください。

* **QSTodoService.m** で、`syncData` は新しい変更をプッシュしてから、`pullData` を呼び出すことでリモート サービスからデータを取得します。`syncData` で、最初に同期コンテキストで `pushWithCompletion` を呼び出します。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブル自体ではなく `MSSyncContext` のメンバーです。なんらかの方法で (作成、更新、または削除操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。`syncData` の最後で、ヘルパー `pullData` を呼び出します。

この例では、プッシュ操作は必ずしも必要ありません。プッシュ操作を行っているテーブルの同期コンテキストで保留中の変更がある場合、プルは必ず最初にプッシュを発行します。ただし、同期テーブルが複数ある場合は、テーブル間で一貫性が維持されるようにプッシュを明示的に呼び出します。

```
      -(void)syncData:(QSCompletionBlock)completion
      {
          // push all changes in the sync context, then pull new data
          [self.client.syncContext pushWithCompletion:^(NSError *error) {
              [self logErrorIfNotNil:error];
              [self pullData:completion];
          }];
      }

```

* 次に、**QSTodoService.m** で、`pullData` はクエリに一致する新しいデータを取得します。`pullData` は `MSSyncTable.pullWithQuery` を呼び出してリモートのデータを取得し、それをローカルに保存します。`pullWithQuery` を使用することで、取得するレコードをフィルター処理するクエリを指定することもできます。この例では、クエリは、リモートの `TodoItem` テーブルのレコードをすべて取得するだけです。

`pullWithQuery` に対する 2 番目のパラメーターは、_増分同期_に使用するクエリ ID です。増分同期では、前回の同期以降に変更されたレコードのみを、レコードの `UpdatedAt` タイムスタンプ (ローカル ストアでは `ms_updatedAt` と呼ばれます) を使用して取得します。クエリ ID は、アプリケーション内の各論理クエリに対して一意の、わかりやすい文字列です。増分同期を解除するには、`nil` をクエリ ID として渡します。これは、どのプル操作でもすべてのレコードを取得するため、効率的なありません。

```
      -(void)pullData:(QSCompletionBlock)completion
      {
          MSQuery *query = [self.syncTable query];

          // Pulls data from the remote server into the local table.
          // We're pulling all items and filtering in the view
          // query ID is used for incremental sync
          [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
              [self logErrorIfNotNil:error];

              // Let the caller know that we have finished
              if (completion != nil) {
                  dispatch_async(dispatch_get_main_queue(), completion);
              }
          }];
      }
```


>[AZURE.NOTE]モバイル サービス データベースでレコードが削除されたときに、デバイスのローカル ストアからレコードを削除するには、[論理的な削除]を有効にします。有効にしない場合は、アプリで定期的に `MSSyncTable.purgeWithQuery` を呼び出して、ローカル ストアを消去する必要があります。


* **QSTodoService.m** で、`addItem` メソッドと `completeItem` メソッドは、データを変更した後で `syncData` を呼び出します。 **QSTodoListViewController.m** では、`refresh` メソッドも `syncData` を呼び出します。これにより、更新時および起動時に最新のデータが UI に表示されます (`init` は `refresh` を呼び出す)。

データを変更するたびにアプリケーションによって `syncData` が呼び出されるので、データの編集時にはいつもオンラインであるとアプリケーションは見なします。

## <a name="review-core-data"></a>Core Data モデルの確認

Core Data オフライン ストアを使用するときは、データ モデルで特定のテーブルとフィールドを定義する必要があります。サンプル アプリケーションには、あらかじめ適切な形式でデータ モデルが含まれています。このセクションでは、これらのテーブルとその使用方法を説明します。

- **QSDataModel.xcdatamodeld** を開きます。4 つのテーブルが定義済みです。3 つは SDK で使用し、1 つはそれ自体が Todo 項目になっています。

      * MS\_TableOperations: サーバーと同期する項目の追跡用
      * MS\_TableOperationErrors: オフライン同期中に発生するエラーの追跡用
      * MS\_TableConfig: すべてのプル操作に対する最後の同期操作の最終更新時刻の追跡用
      * TodoItem: Todo 項目の格納用。システム列 **ms\_createdAt**、**ms\_updatedAt**、および **ms\_version** は省略可能なシステム プロパティです。

>[AZURE.NOTE]Mobile Services SDK では、"**`ms_`**" が付く列名を予約しています。このプレフィックスは、システム列以外のものに使用しないでください。そうしないと、リモート サービスの使用時に列名が変更されます。

- オフライン同期機能を使用する場合は、次のようにシステム テーブルを定義する必要があります。

    ### システム テーブル

    #### MS\_TableOperations

    | 属性 | 種類 |
    |-------------- |   ------    |
    | id (必須) | Integer 64 |
    | itemId | String |
    | プロパティ | Binary Data |
    | テーブル | String |
    | tableKind | Integer 16 |

    #### MS\_TableOperationErrors

    | 属性 | 種類 |
    |-------------- | ----------  |
    | id (必須) | String |
    | operationId | Integer 64 |
    | プロパティ | Binary Data |
    | tableKind | Integer 16 |

    #### MS\_TableConfig


    | 属性 | 種類 |
    |-------------- | ----------  |
    | id (必須) | String |
    | key | String |
    | keyType | Integer 64 |
    | テーブル | String |
    | 値 | String |

    ### データ テーブル

    #### TodoItem

    | 属性 | 型 | 注 | 
    |-------------- |  ------ | -------------------------------------------------------|
    | id (必須) | String | リモート ストア内のプライマリ キー (必須) |
    | 完了 | Boolean | Todo 項目フィールド |
    | テキスト | String | Todo 項目フィールド |
    | ms\_createdAt | 日付 | (省略可能) \_\_createdAt システム プロパティにマップ | | ms\_updatedAt | Date | (省略可能) \_\_updatedAt システム プロパティにマップ | | ms\_version | String | (省略可能) 競合の検出に使用され、\_\_version にマップ |



## <a name="setup-sync"></a>アプリの同期動作の変更

このセクションでは、アプリケーションの起動時または項目の挿入や更新時ではなく、[ジェスチャの更新] の実行時にだけ、アプリケーションが同期するようアプリケーションを変更します。

* **QSTodoListViewController.m** で、`viewDidLoad` を変更して、メソッドの最後にある `[self refresh]` の呼び出しを削除します。これで、アプリケーションの起動時にデータはサーバーと同期されなくなり、データはローカルに格納されるだけとなります。

* **QSTodoService.m** で、項目の挿入後に同期しないように `addItem` を変更します。`self syncData` ブロックを削除し、代わりに次を配置します。

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

* 同様に、再度 **QSTodoService.m** で、`completeItem` にある `self syncData` のブロックを削除し、代わりに次を配置します。

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

## <a name="test-app"></a>アプリケーションをテストする

このセクションでは、シミュレーターの Wi-Fi をオフにして、オフライン シナリオを作成します。データ項目を追加すると、ローカル Core Data ストアに保持されますが、モバイル サービスとは同期されません。

1. Mac のインターネット接続を無効にします。ホストの Mac のインターネット接続がシミュレーターに使用される可能性があるので、iOS シミュレーターの Wi-Fi のみを無効にしても効果がないことがあるので、コンピューター自体のインターネットを無効にしてください。これは、オフライン シナリオをシミュレートします。

2. いくつかの Todo 項目を追加するか、一部の項目を完了します。シミュレーターを終了し (またはアプリケーションを強制的に閉じて)、再起動します。変更内容が保存されていることを確認します。データ項目がまだ表示されるのは、データ項目がローカルの Core Data ストアに保持されているためです。

3. リモートの TodoItem テーブルの内容を表示します。新しい項目が、サーバーと同期_されなかった_ことを確認します。

   - JavaScript バックエンドの場合は、管理ポータルで [データ] タブをクリックして、`TodoItem` テーブルの内容を表示します。
   - .NET バックエンドの場合は、SQL Server Management Studio のような SQL ツールまたは Fiddler や Postman のような REST クライアントを使用して、テーブルの内容を表示します。

4. iOS シミュレーターの Wi-Fi をオンにします。次に、項目の一覧をプルダウンして更新操作を実行します。進行状況を示すスピナーとテキスト "同期中..." が表示されます。

5. TodoItem データをもう一度表示します。新しく変更した TodoItems が表示されます。

## 概要

モバイル サービスのオフライン機能をサポートするために、`MSSyncTable` インターフェイスを使用して、ローカル ストアにより `MSClient.syncContext` を初期化しました。この例では、ローカル ストアは、Core Data に基づいたデータベースでした。

Core Data ローカル ストアを使用する場合は、[正しいシステム プロパティ][Review the Core Data model]を使用して、複数のテーブルを定義します。モバイル サービスに対する通常の操作は、まるでアプリケーションが接続されているかのように機能しますが、すべての操作はローカル ストアに対して実施されます。

ローカル ストアをサーバーに同期させるために、`MSSyncTable.pullWithQuery` と `MSClient.syncContext.pushWithCompletion` を次のように使用しました。

		* To push changes to the server, you called `pushWithCompletion`. This method is in `MSSyncContext` instead of the sync table because it will push changes across all tables. Only records that are modified in some way locally (through CUD operations) are be sent to the server.

		* To pull data from a table on the server to the app, you called `MSSyncTable.pullWithQuery`. A pull always issues a push first. This is to ensure all tables in the local store along with relationships remain consistent. `pullWithQuery` can also be used to filter the data that is stored on the client, by customizing the `query` parameter.

## 次のステップ

* [Mobile Services のオフライン サポートでの競合を処理する]

* [Mobile Services での論理削除の使用方法][Soft Delete]

## その他のリソース

* [Cloud Cover: Azure Mobile Services でのオフライン同期]

* [Azure Friday: Azure Mobile Services のオフライン対応アプリケーション] (注: デモは Windows 向けですが、機能の説明はすべてのプラットフォームに適用されます)

<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Mobile Services のオフライン サポートでの競合の処理]: mobile-services-ios-handling-conflicts-offline-data.md
[Mobile Services のオフライン サポートでの競合を処理する]: mobile-services-ios-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[論理的な削除]: mobile-services-using-soft-delete.md

[Cloud Cover: Azure Mobile Services でのオフライン同期]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Azure Mobile Services のオフライン対応アプリケーション]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[Mobile Services のクイック スタート チュートリアル]: mobile-services-ios-get-started.md

<!---HONumber=August15_HO7-->