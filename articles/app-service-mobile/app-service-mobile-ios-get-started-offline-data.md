<properties
	pageTitle="Azure Mobile App (iOS) に対するオフライン同期の有効化"
	description="App Service Mobile Apps を使用して、iOS アプリケーション内のオフライン データをキャッシュおよび同期する方法を説明します。"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="krisragh"/>

# iOS モバイル アプリのオフライン同期を有効にする

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## 概要

このチュートリアルでは、Azure Mobile Apps for iOS 向けのモバイル アプリのオフライン同期機能について説明します。オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリとやり取りできます。変更はローカル データベースに格納され、デバイスが再びオンラインになると、これらの変更がリモート バックエンドと同期されます。

Azure Mobile Apps を初めて使用する場合は、最初に [iOS アプリの作成] に関するチュートリアルを完了してください。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

オフラインの同期機能の詳細については、トピック「[Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

## <a name="review-sync"></a>クライアント同期コードの確認

チュートリアル「[iOS アプリの作成]」でダウンロードしたクライアント プロジェクトには、ローカルのコア データに基づくデータベースを使用したオフライン同期をサポートするコードが既に含まれています。このセクションでは、チュートリアルのコードに既に含まれているものの概要を示します。機能の概念的な概要については、「[Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

Azure Mobile Apps のオフライン データ同期機能を使用すると、ネットワークにアクセスできない場合でもエンド ユーザーはローカル データベースとやり取りできるようになります。アプリケーションでこれらの機能を使用するには、`MSClient` の同期コンテキストを初期化して、ローカル ストアを参照します。その後、`MSSyncTable` インターフェイスを使用してテーブルを参照します。

1. **QSTodoService.m** で、メンバー `syncTable` の種類が `MSSyncTable` であることを確認します。オフライン同期では、`MSTable` の代わりにこの同期テーブル インターフェイスを使用します。同期テーブルが使用されると、すべての操作はローカル ストアを参照し、明示的なプッシュ操作とプル操作を使用するリモート バックエンドのみが同期されます。

    同期テーブルへの参照を取得するには、メソッド `syncTableWithName` を使用します。オフライン同期機能を解除するには、代わりに `tableWithName` を使用します。

2. テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。これは、`QSTodoService.init` メソッド内の関連コードです。

        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

    このコードでは、Mobile Apps SDK で提供されるインターフェイス `MSCoreDataStore` を使用して、ローカル ストアを作成します。代わりに `MSSyncContextDataSource` プロトコルを実装することで、別のローカル ストアを指定することもできます。

    `initWithDelegate` の最初のパラメーターは、競合ハンドラーを指定するために使用します。`nil` を渡したため、既定の競合ハンドラーが取得されます。この競合ハンドラーは、競合が発生すると失敗します。

	<!-- For details on how to implement a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services]. -->

3. メソッド `pullData` と `syncData` は、実際の同期操作を実行します。まず `syncData` が新しい変更をプッシュし、次にリモート バックエンドからデータを取得するために `pullData` を呼び出します。

        -(void)syncData:(QSCompletionBlock)completion
        {
            // push all changes in the sync context, then pull new data
            [self.client.syncContext pushWithCompletion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                [self pullData:completion];
            }];
        }

    そして、メソッド `pullData` がクエリに一致する新しいデータを取得します。

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

    `syncData` では、同期コンテキストの最初に `pushWithCompletion` を呼び出します。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブル自体ではなく `MSSyncContext` のメンバーです。何らかの方法で (CUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。次に、ヘルパー `pullData` が呼び出されます。これは、`MSSyncTable.pullWithQuery` を呼び出してリモート データを取得し、ローカル データベースに格納します。

    この例では、プッシュ操作は必ずしも必要ありません。プッシュ操作を行っているテーブルの同期コンテキストで保留中の変更がある場合は、プルは必ず最初にプッシュを発行します。ただし、複数の同期テーブルを使用している場合は、関連するテーブル全体で一貫性を保つよう、明示的にプッシュを呼び出す方法が最も適切です。

    メソッド `pullWithQuery` を使用すると、取得するレコードをフィルター処理するクエリを指定できます。この例でのクエリは、単にリモートの `TodoItem` テーブルのレコードをすべて取得します。

    `pullWithQuery` に対する 2 番目のパラメーターは、*増分同期*に使用するクエリ ID です。増分同期では、前回の同期以降に変更されたレコードのみを、レコードの `UpdatedAt` タイムスタンプ (ローカル ストアでは `updatedAt` と呼ばれます) を使用して取得します。クエリ ID は、アプリ内の各論理クエリに対して一意の、わかりやすい文字列にする必要があります。増分同期を解除するには、`nil` をクエリ ID として渡します。これは、プル操作のたびにすべてのレコードを取得するため、非効率になる場合があります。

5. クラス `QSTodoService` では、メソッド `syncData` はデータ変更操作である `addItem` や `completeItem` の後に呼び出されます。また、`QSTodoListViewController.refresh` からも呼び出されるため、ユーザーが更新ジェスチャを実行するたびに最新のデータが取得されます。`QSTodoListViewController.init` は `refresh` を呼び出すため、アプリケーションは起動時に同期も実行します。

    データが変更されるたびに `syncData` が呼び出されることから、このアプリケーションは、データの編集時には常にユーザーがオンラインであると想定していることになります。別のセクションでは、アプリケーションを更新して、ユーザーがオフラインのときにも編集できるようにします。

## <a name="review-core-data"></a>Core Data モデルの確認

Core Data オフライン ストアを使用するときは、データ モデルで特定のテーブルとフィールドを定義する必要があります。サンプル アプリケーションには、あらかじめ適切な形式でデータ モデルが含まれています。このセクションでは、これらのテーブルとその使用方法を説明します。

- **QSDataModel.xcdatamodeld** を開きます。4 つのテーブルが定義済みです。3 つは SDK で使用し、1 つはそれ自体が Todo 項目になっています。
      * MS\_TableOperations: サーバーと同期する必要がある項目の追跡用
      * MS\_TableOperationErrors: オフライン同期中に発生するエラーの追跡用
      * MS\_TableConfig: すべてのプル操作に対する最後の同期操作の最終更新時刻の追跡用
      * TodoItem: Todo 項目の格納用。システム列 **createdAt**、**updatedAt**、および **version** は省略可能なシステム プロパティです。

>[AZURE.NOTE] Azure Mobile Apps SDK では、"**``**" が付く列名が予約されています。システム列以外でこのプレフィックスを使用しないでください。使用した場合、リモート バックエンドを使用するときに列名が変更されます。

- オフライン同期機能を使用する場合は、次のようにシステム テーブルを定義する必要があります。

    ### システム テーブル

    **MS\_TableOperations**

    ![][defining-core-data-tableoperations-entity]

    | 属性 | 型 |
    |----------- |   ------    |
    | id | Integer 64 |
    | itemId | String |
    | プロパティ | Binary Data |
    | テーブル | String |
    | tableKind | Integer 16 |

    <br>**MS\_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

    | 属性 | 型 |
    |----------- |   ------    |
    | id | String |
    | operationId | Integer 64 |
    | プロパティ | Binary Data |
    | tableKind | Integer 16 |

    <br>**MS\_TableConfig**

    ![][defining-core-data-tableconfig-entity]

    | 属性 | 型 |
    |----------- |   ------    |
    | id | String |
    | key | String |
    | keyType | Integer 64 |
    | テーブル | String |
    | 値 | String |

    ### データ テーブル

    **TodoItem**

    | 属性 | 型 | 注 |
    |-----------   |  ------ | -------------------------------------------------------|
    | id | 文字列、必須のマーク | リモート ストア内のプライマリ キー |
    | 完了 | Boolean | Todo 項目フィールド |
    | テキスト | String | Todo 項目フィールド |
    | createdAt | 日付 | (省略可能) createdAt システム プロパティにマップします。 |
    | updatedAt | 日付 | (省略可能) updatedAt システム プロパティにマップします。 |
    | version | String | (省略可能) 競合の検出に使用され、バージョンにマップします |


## <a name="setup-sync"></a>アプリケーションの同期動作を変更する

このセクションでは、アプリケーションの起動時または項目の挿入や更新時ではなく、[ジェスチャの更新] ボタンの実行時にだけ、アプリケーションが同期するようアプリケーションを変更します。

1. **QSTodoListViewController.m** で **viewDidLoad** メソッドを変更して、メソッドの最後にある `[self refresh]` への呼び出しを削除します。これで、アプリケーションの起動時にデータはサーバーと同期されなくなりますが、代わりにデータはローカル ストアのコンテンツになります。

2. **QSTodoService.m** で `addItem` の定義を変更して、項目の挿入後に同期しないようにします。`self syncData` ブロックを削除して、次のように置き換えます。

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. 同様に、`completeItem` の定義を変更します。`self syncData` のブロックを削除して、次のように置き換えます。

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

## <a name="test-app"></a>アプリケーションをテストする

ここでは、無効な URL に接続してオフライン シナリオをシミュレートします。データ項目を追加すると、ローカル Core Data ストアに保持されますが、モバイル バックエンドとは同期されません。

1. **QSTodoService.m** のモバイル アプリ URL を無効な URL に変更し、アプリを再実行します。

        self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];

2. いくつかの Todo 項目を追加するか、一部の項目を完了します。シミュレーターを終了し (またはアプリケーションを強制的に閉じて)、再起動します。変更内容が保存されていることを確認します。

3. リモートの TodoItem テーブルの内容を表示します。

    + Node.js バックエンドの場合は、[Azure ポータル](https://portal.azure.com/)に移動し、Mobile App バックエンドで **[簡易テーブル]**、**[TodoItem]** の順にクリックして、`TodoItem` テーブルの内容を表示します。
   	+ .NET バックエンドの場合は、SQL Server Management Studio などの SQL ツール、または Fiddler や Postman などの REST クライアントを使用して、テーブルの内容を表示します。

    新しい項目が、サーバーと同期*されなかった*ことを確認します。

4. **QSTodoService.m** の URL を正しい URL に変更し、アプリを再実行します。項目の一覧をプルダウンして更新操作を実行します。進行状況を示すスピナーとテキスト "同期中..." が表示されます。

5. TodoItem データをもう一度表示します。新しく変更した TodoItems が表示されます。

## 概要

オフライン同期機能をサポートするため、`MSSyncTable` インターフェイスを使用し、ローカル ストアで `MSClient.syncContext` を初期化しました。この例では、ローカル ストアは、Core Data に基づいたデータベースでした。

Core Data ローカル ストアを使用する場合は、[正しいシステム プロパティ](#review-core-data)を使用して、複数のテーブルを定義する必要があります。

Azure Mobile Apps に対する通常の CRUD 操作は、アプリケーションはまだ接続されているが、すべての操作はローカル ストアに対して発生したかのように動作します。

ローカル ストアをサーバーと同期しようとする場合は、`MSSyncTable.pullWithQuery` と `MSClient.syncContext.pushWithCompletion` の各メソッドを使用しました。

*  変更内容をサーバーにプッシュするために、`pushWithCompletion` を呼び出しました。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブルではなく `MSSyncContext` のメンバーです。

    何らかの方法で (CUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。

* サーバー上のテーブルからアプリケーションにデータをプルするために、`MSSyncTable.pullWithQuery` を呼び出しました。

    プルは必ず、最初にプッシュを実行します。これは、ローカル ストアのすべてのテーブルとリレーションシップの一貫性を確実に保つためです。

    `query` パラメーターをカスタマイズすることにより、`pullWithQuery` を使用して、クライアントに格納されたデータをフィルター処理できます。

* 増分同期を有効にするには、クエリ ID を `pullWithQuery` に渡します。クエリ ID は、最後のプル操作の結果の最後に更新されたタイムスタンプを格納するのに使用されます。クエリ ID は、アプリ内の各論理クエリに対して一意の、わかりやすい文字列にする必要があります。クエリにパラメーターがある場合は、同じパラメーター値をクエリ ID の一部に含める必要があります。

    増分同期を無効にする場合は、`nil` をクエリ ID として渡します。この場合、`pullWithQuery` への呼び出しごとにすべてのレコードが再取得されるため、場合によっては非効率となります。


## その他のリソース

* [Azure Mobile Apps でのオフライン データ同期]

* [Cloud Cover: Azure Mobile Services でのオフライン同期] \(注: このビデオは Mobile Services に関するものですが、オフライン同期は Azure Mobile Apps でも同様に機能します)

<!-- URLs. -->


[iOS アプリの作成]: ../app-service-mobile-ios-get-started.md
[Azure Mobile Apps でのオフライン データ同期]: ../app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Azure Mobile Services でのオフライン同期]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0706_2016-->