<properties 
	pageTitle="Mobile Services でのオフライン データ同期の使用 (iOS) | モバイル デベロッパー センター" 
	description="Azure Mobile Services を使用して、iOS アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="01/16/2015" 
	ms.author="krisragh,donnam"/>

# モバイル サービスでのオフライン データの同期の使用

[AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

このチュートリアルでは、iOS での Mobile Services のオフライン同期機能について説明します。オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。変更は、ローカル データベースに格納されます。デバイスがオンラインに戻ると、これらの変更は、リモート サービスと同期されます。

オフライン同期には、いくつかの潜在的な用途があります。

* サーバー データをデバイスにローカルでキャッシュすることにより、アプリケーションの応答性を向上させる。
* 断続的なネットワーク接続に対してアプリケーションに弾力性を持たせる。
* エンド ユーザーがネットワークにアクセスできなくてもデータを作成および変更できるようにすることで、接続がほとんどまたはまったく得られないような状況をサポートする。
* 複数のデバイス間でデータを同期させ、同じレコードが 2 つのデバイスによって変更されたときに競合を検出する。

>[AZURE.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料モバイル サービスを入手できます。このサービスは評価終了後も使用できます。詳細については、<a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>を参照してください。

Mobile Services を初めて使用する場合は、最初にチュートリアル「[Mobile Services の使用]」を完了することをお勧めします。

このチュートリアルでは、次の基本的な手順について説明します。

1. [サンプル アプリケーションの取得]
2. [Mobile Services 同期コードのレビュー]
3. [Core Data モデルのレビュー]
4. [アプリケーションの同期動作の変更]
5. [アプリケーションをテストする]

## <a name="get-app"></a>オフラインの ToDo サンプル アプリケーションの取得

[GitHub の Mobile Services サンプル リポジトリ]で、リポジトリを複製し、プロジェクト [オフライン iOS サンプル]を Xcode で開きます。

### ベータ SDK
オフライン サポートを既存のアプリケーションに追加するには、最新の[ベータ iOS SDK](http://aka.ms/gc6fex) を取得します。

## <a name="review-sync"></a>Mobile Services 同期コードのレビュー

Azure Mobile Services のオフライン同期を使用すると、ネットワークにアクセスできない場合でも、エンド ユーザーはローカル データベースとのやり取りができます。アプリケーションでこれらの機能を使用するには、 `MSClient` の同期コンテキストを初期化して、ローカル ストアを参照します。その後、 `MSSyncTable` インターフェイスを使用してテーブルを参照します。

このセクションでは、サンプルのオフライン同期に関連するコードについて説明します。

1. **QSTodoService.m** で、メンバーの種類  `syncTable` が  `MSSyncTable` であることを確認します。オフライン同期では、 `MSTable` の代わりにこの同期インターフェイスを使用します。同期テーブルが使用されると、すべての操作はローカル ストアを参照し、明示的なプッシュ操作とプル操作を使用したリモート サービスとのみ同期されます。

    同期テーブルへの参照を取得するには、メソッド  `syncTableWithName` を使用します。オフラインの同期機能を削除するには、代わりに  `tableWithName` を使用します。

3. テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。これは、 `QSTodoService.init` メソッドの関連するコードです。

        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
        
        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

    これにより、Mobile Services SDK で提供されるインターフェイス  `MSCoreDataStore` を使用して、ローカル ストアを作成します。代わりに、 `MSSyncContextDataSource` プロトコルを実装することで、別のローカル ストアを提供できます。

     `initWithDelegate` の最初のパラメーターは、競合ハンドラーを指定するために使用します。 `nil` を渡したため、既定の競合ハンドラーを取得します。この競合ハンドラーは、競合が発生すると失敗します。カスタム競合ハンドラーを実装する方法の詳細についてには、[Mobile Services のオフライン サポートでの競合の処理]に関するチュートリアルを参照してください。

4. メソッド  `pullData` と  `syncData` は、実際の同期操作を実行します。最初に  `syncData` は新しい変更をプッシュしてから、 `pullData` を呼び出してリモート サービスからデータを取得します。

        -(void)syncData:(QSCompletionBlock)completion
        {
            // push all changes in the sync context, then pull new data
            [self.client.syncContext pushWithCompletion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                [self pullData:completion];
            }];
        }

    同様に、メソッド `pullData` がクエリに一致する新しいデータを取得します。

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

    `syncData` で、最初に同期コンテキストで `pushWithCompletion` を呼び出します。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブル自体ではなく `MSSyncContext` のメンバーです。何らかの方法で (CUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。次にヘルパー `pullData` が呼び出されます。これは、`MSSyncTable.pullWithQuery` を呼び出してリモート データを取得し、ローカル データベースに格納します。 

    この例では、プッシュ操作は必ずしも必要ありません。プッシュ操作を行っているテーブルの同期コンテキストで保留中の変更がある場合は、プルは必ず最初にプッシュを発行します。ただし、複数の同期テーブルを使用している場合は、関連するテーブル全体で一貫性を保つよう、明示的にプッシュを呼び出す方法が最も適切です。

    メソッド `pullWithQuery` を使用すると、取得するレコードをフィルターするクエリを指定できます。この例では、クエリは、リモートの `TodoItem` テーブルのレコードをすべて取得するだけです。

    `pullWithQuery` に対する 2 番目のパラメーターは、*増分同期*に使用するクエリ ID です。増分同期では、前回の同期以降に変更されたレコードだけを、レコードの  `UpdatedAt` タイムスタンプ (ローカル ストアでは  `ms_updatedAt` といいます) を使用して取得します。クエリ ID は、アプリケーションの論理クエリごとに一意のわかりやすい文字列にする必要があります。増分同期を解除するには、`nil` をクエリ ID として渡します。これは、プル操作のたびにすべてのレコードを取得するため、非効率になる場合があります。

    >[AZURE.NOTE] モバイル サービスのデータベースからレコードが削除された場合に、デバイスのローカル ストアからレコードを削除するには、[論理削除]を有効にする必要があります。有効にしない場合、アプリケーションは定期的に  `MSSyncTable.purgeWithQuery` を呼び出し、ローカル ストアを削除します。

5. クラス `QSTodoService` では、 `syncData` メソッドは、データを変更する操作である `addItem` と `completeItem`の後に呼び出されます。また、 `QSTodoListViewController.refresh` からも呼び出されるため、ユーザーは、更新ジェスチャを実行するたびに最新のデータを取得します。 `QSTodoListViewController.init` は  `refresh` を呼び出すため、アプリケーションは起動時に同期も実行します。

    データが変更されるたびに `syncData` が呼び出されることから、このアプリケーションは、データが編集されるたびにユーザーがオンラインであると想定します。別のセクションでは、アプリケーションを更新して、ユーザーがオフラインのときにも編集できるようにします。

## <a name="review-core-data"></a>Core Data モデルのレビュー

Core Data オフライン ストアを使用するときは、データ モデルで特定のテーブルとフィールドを定義する必要があります。サンプル アプリケーションには、あらかじめ適切な形式でデータ モデルが含まれています。このセクションでは、これらのテーブルとその使用方法を説明します。

- **QSDataModel.xcdatamodeld** を開きます。4 つのテーブルが定義済みです。3 つは SDK で使用し、1 つはそれ自体が Todo 項目になっています。
      * MS_TableOperations:サーバーと同期する必要がある項目の追跡用
      * MS_TableOperationErrors:オフライン同期中に発生するエラーの追跡用 
      * MS_TableConfig:すべてのプル操作に対する最後の同期操作の最終更新時刻の追跡用
      * TodoItem:Todo 項目の格納用。システム列 **ms_createdAt**、**ms_updatedAt**、および **ms_version** は省略可能なシステム プロパティです。 

>[AZURE.NOTE] Mobile Services SDK では、"**`ms_`**" が付く列名を予約しています。システム列以外でこのプレフィックスを使用しないでください。使用すると、リモート サービスを使用する場合に、列名が変更されます。

- オフライン同期機能を使用する場合は、次のようにシステム テーブルを定義する必要があります。

    ### システム テーブル

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

    | 属性  |    型     |
    |----------- |   ------    |
    | id         | 整数 64  |
    | itemId     | 文字列      |
    | properties | バイナリ データ |
    | table      | 文字列      |
    | tableKind  | Integer 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

    | 属性  |    型     |
    |----------- |   ------    |
    | id         | 文字列      |
    | operationID | 整数 64 |
    | properties | バイナリ データ |
    | tableKind  | Integer 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

    | 属性  |    型     |
    |----------- |   ------    |
    | id         | 整数 64  |
    | key        | 文字列      |
    | keyType    | 整数 64  |
    | table      | 文字列      |
    | value      | 文字列      |

    ### データ テーブル

    ![][defining-core-data-todoitem-entity]

    **TodoItem**

    | 属性    |  型   | 注                                                       | 
    |-----------   |  ------ | -----------------------------------------------------------|
    | id           | 文字列  | リモート ストア内のプライマリ キー                                |
    | complete     | ブール値 | Todo 項目のフィールド                                            |
    | text         | 文字列  | Todo 項目のフィールド                                            |
    | ms_createdAt | 日付    | *(省略可能)* `__createdAt` システム プロパティにマプ         |
    | ms_updatedAt | 日付    | *(省略可能)* `__updatedAt` システム プロパティにマプ        |
    | ms_version   | 文字列  | *(省略可能)* 競合の検出に使用、`__version` にマップ |


## <a name="setup-sync"></a>アプリケーションの同期動作の変更

このセクションでは、アプリケーションの起動時または項目の挿入や更新時ではなく、[ジェスチャの更新] ボタンの実行時にだけ、アプリケーションが同期するようアプリケーションを変更します。 

1. **QSTodoListViewController.m** で、**viewDidLoad** メソッドを変更し、メソッドの最後にある `[self refresh]` への呼び出しを削除します。これで、アプリケーションの起動時にデータはサーバーと同期されなくなりますが、代わりにデータはローカル ストアのコンテンツになります。

2. **QSTodoService.m** で、 `addItem` 定義を変更して、項目の挿入後に同期しないようにします。 `self syncData` ブロックを削除して、次に置き換えます。

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. 上のように  `completeItem` の定義を変更し、 `self syncData` のブロックを削除して、次に置き換えます。

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

## <a name="test-app"></a>アプリケーションをテストする

このセクションでは、シミュレーターの Wi-Fi をオフにして、オフライン シナリオを作成します。データ項目を追加すると、ローカル Core Data ストアに保持されますが、モバイル サービスとは同期されません。

1. iOS シミュレーターの Wi-Fi をオフにします。

2. いくつかの Todo 項目を追加するか、一部の項目を完了します。シミュレーターを終了し (またはアプリケーションを強制的に閉じて)、再起動します。変更内容が保存されていることを確認します。

3. リモートの TodoItem テーブルの内容を表示します。
   - JavaScript バックエンドの場合は、管理ポータルで [データ] タブをクリックし、 `TodoItem` テーブルの内容を表示します。
   - .NET バックエンドの場合は、SQL Server Management Studio のような SQL ツールまたは Fiddler や Postman のような REST クライアントを使用して、テーブルの内容を表示します。

    新しい項目が、サーバーと同期 *されなかった*ことを確認します。

4. iOS シミュレーターで Wi-Fi をオンにし、項目の一覧をプルダウンして更新ジェスチャを実行します。進行状況を示すスピナーとテキスト "同期中..." が表示されます。

5. TodoItem データをもう一度表示します。新しく変更した TodoItems が表示されます。

## まとめ

モバイル サービスのオフライン機能をサポートするために、 `MSSyncTable` インターフェイスを使用して、ローカル ストアにより  `MSClient.syncContext` を初期化しました。この例では、ローカル ストアは、Core Data に基づいたデータベースでした。 

Core Data ローカル ストアを使用する場合、[正しいシステム プロパティ][Core Data モデルのレビュー]を使用して、複数のテーブルを定義する必要があります。

モバイル サービスに対する通常の CRUD 操作は、まるでアプリケーションが接続されているかのように機能しますが、すべての操作はローカル ストアに対して実施されます。

ローカル ストアをサーバーと同期する場合は、 `MSSyncTable.pullWithQuery` メソッドと  `MSClient.syncContext.pushWithCompletion` メソッドを使用しました。

*  サーバーに変更をプッシュするために、`Core Data モデルのレビュー`を呼び出しました。このメソッドは、すべてのテーブルに対して変更をプッシュするため、同期テーブルではなく  `MSSyncContext` のメンバーです。

    何らかの方法で (CUD 操作により) ローカルで変更されたレコードだけが、サーバー宛てに送信されます。
   
* サーバー上のテーブルからアプリケーションにデータをプルするために、 `MSSyncTable.pullWithQuery` を呼び出しました。

    プルは必ず、最初にプッシュを実行します。これは、ローカル ストアのすべてのテーブルとリレーションシップの一貫性を確実に保つためです。

     `query` パラメーターをカスタマイズすると、 `pullWithQuery` を使用して、クライアントに格納されたデータをフィルターできます。 

* 増分同期を有効にするには、 `pullWithQuery` にクエリ ID を渡します。クエリ ID を使用して、最後のプル操作の結果から、最後に更新されたタイムスタンプを格納します。クエリ ID は、アプリケーションの論理クエリごとに一意のわかりやすい文字列にする必要があります。クエリにパラメーターがある場合、同じパラメーター値をクエリ ID の一部にする必要があります。

    増分同期を除外する場合は、 `nill` をクエリ ID として渡します。その場合は、 `pullWithQuery` を呼び出すたびにすべてのレコードが取得されます。よって、非効率となる可能性があります。

* モバイル サービスのデータベースからレコードが削除された場合に、デバイスのローカル ストアからレコードを削除するには、[論理削除]を有効にする必要があります。そのようにしない場合は、リモート サービスでレコードが削除されていたら、アプリケーションで定期的に  `MSSyncTable.purgeWithQuery` を呼び出し、ローカル データベースからレコードを削除する必要があります。


## 次のステップ

* [Mobile Services のオフライン サポートでの競合の処理]に関するページ

* [Mobile Services での論理削除の使用方法][論理削除]

## その他のリソース

* [Cloud Cover:Azure Mobile Services でのオフラインの同期]に関するページ

* [Azure Friday:Azure Mobile Services のオフライン対応アプリケーション]に関するページ \(注: デモは、Windows 向けですが、機能の説明はすべてのプラットフォームに対して適用されます\)

<!-- URLs. -->

[サンプル アプリケーションの取得]: #get-app
[Core Data モデルのレビュー]: #review-core-data
[Mobile Services 同期コードのレビュー]: #review-sync
[アプリケーションの同期動作の変更]: #setup-sync
[アプリケーションをテストする]: #test-app

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

[コア データ モデル エディターのヘルプ]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[コンセント接続の作成]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[ユーザー インターフェイスの作成]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[ストーリーボード内のシーン間へのセグエの追加]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[シーンのストーリーボードへの追加]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[中核となるデータ]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Preview SDK をここからダウンロード]: http://aka.ms/Gc6fex
[IOS 向けモバイル サービスのクライアント ライブラリを使用する方法]: /ja-jp/documentation/articles/mobile-services-ios-how-to-use-client-library/
[オフライン iOS サンプル]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[GitHub の Mobile Services サンプル リポジトリ]: https://github.com/Azure/mobile-services-samples

[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started-data/
[Mobile Services のオフライン サポートでの競合の処理]に関するページ: /ja-jp/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
[論理削除]: /ja-jp/documentation/articles/mobile-services-using-soft-delete/

[Cloud Cover:Azure Mobile Services でのオフラインの同期]に関するページ: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday:Azure Mobile Services のオフライン対応アプリケーション]に関するページ: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/



<!--HONumber=42-->
