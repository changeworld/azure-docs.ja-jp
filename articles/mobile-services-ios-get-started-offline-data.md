<properties urlDisplayName="Using Offline Data" pageTitle="Mobile Services でのオフライン データ同期の使用 (iOS) | モバイル デベロッパー センター" metaKeywords="" description="Azure Mobile Services を使用して、iOS アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using Offline data sync in Mobile Services" authors="krisragh" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# モバイル サービスでのオフライン データの同期の使用


[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


このチュートリアルでは、iOS 上の Mobile Services のオフライン同期機能について説明します。開発者はこの機能を使用して、エンド ユーザーがネットワークにアクセスできなくても使用可能なアプリケーションを作成できます。

オフライン同期には、いくつかの潜在的な用途があります。

* サーバー データをデバイスにローカルでキャッシュすることにより、アプリケーションの応答性を向上させる。
* 断続的なネットワーク接続に対してアプリケーションに弾力性を持たせる。
* エンド ユーザーがネットワークにアクセスできなくてもデータを作成および変更できるようにすることで、接続がほとんどまたはまったく得られないような状況をサポートする。
* 複数のデバイス間でデータを同期させ、同じレコードが 2 つのデバイスによって変更されたときに競合を検出する。

このチュートリアルでは、「[Mobile Services の使用]」チュートリアルで使用したアプリケーションを更新し、Azure Mobile Services のオフライン機能をサポートできるようにする方法を説明します。その後、切断されたオフラインの状況でデータを追加し、それらの項目をオンライン データベースに同期してから、Azure の管理ポータルにログインして、アプリケーションを実行したときにデータに加えた変更を表示します。

>[WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合、Azure 評価版にサインアップして、最大 10 件の無料モバイル サービスを入手できます。このサービスは評価終了後も使用できます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>を参照してください。

このチュートリアルの目的は、Mobile Services が Windows ストア アプリのデータを Azure に格納および取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。Mobile Services を初めて使用する場合は、最初にチュートリアル「[Mobile Services の使用]」を完了することをお勧めします。

>[WACOM.NOTE] このセクションをスキップして、Getting Started プロジェクトのバージョンをダウンロードすることもできます。このプロジェクトは、既にオフラインをサポートしており、このトピックで説明されているすべての要素が含まれています。  オフライン サポートが有効になっているプロジェクトをダウンロードするには、[iOS 用のオフライン サンプルの使用]に関するページを参照してください。


このチュートリアルでは、次の基本的な手順について説明します。

1. [サンプルのクイック スタート アプリケーションの取得]
2. [Preview SDK のダウンロードとフレームワークの更新]
3. [Core Data の設定]
4. [Core Data モデルの定義]
5. [同期テーブルと同期コンテキストの初期化と使用]
6. [アプリケーションをテストする]

## <a name="get-app"></a>サンプルのクイック スタート アプリケーションの取得

「[Mobile Services の使用]」の手順に従って、クイック スタート プロジェクトをダウンロードします。

## <a name="update-app"></a>Preview SDK のダウンロードとフレームワークの更新

1. アプリケーションにオフライン サポートを追加するために、オフライン同期をサポートしている iOS 用 Mobile Services SDK のバージョンを取得しましょう。これはプレビュー機能としてリリースされているため、まだ正式にダウンロード可能な SDK ではありません。[Preview SDK はここからダウンロードできます]。

2. 次に、Xcode のプロジェクトから既存の **WindowsAzureMobileServices.framework** リファレンスを削除します。リファレンスを選択し、**[Edit]** メニューの [Move to Trash] を選択すると、実際のファイルが削除されます。

      ![][update-framework-1]

3. 新しい Preview SDK を解凍し、以前の SDK の場所に新しい **WindowsAzureMobileServices.framework** SDK をドラッグ アンド ドロップします。[Copy items into destination group's folder (if needed)] が選択されていることを確認します。

      ![][update-framework-2]


## <a name="setup-core-data"></a>Core Data の設定

1. iOS 用 Mobile Services SDK では、**MSSyncContextDataSource** プロトコルに準拠している限り、任意の永続ストアを使用できます。SDK には、[Core Data] に基づいてこのプロトコルを実装するデータ ソースが含まれています。

2. アプリケーションで Core Data を使用するため、**[Targets]** の **[Build Phases]** に移動し、**[Link Binary with Libraries]** に **CoreData.framework** を追加します。

      ![][core-data-1]

      ![][core-data-2]

3. Core Data を追加している Xcode 内の既存のプロジェクトは、まだ Core Data をサポートしていません。そのため、プロジェクトのさまざまな部分に定型コードを追加する必要があります。まず、**QSAppDelegate.h** に次のコードを追加します。

        #import <UIKit/UIKit.h>  
        #import <CoreData/CoreData.h>  

        @interface QSAppDelegate : UIResponder <UIApplicationDelegate>  

        @property (strong, nonatomic) UIWindow *window;  

        @property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;  
        @property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;  
        @property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;  

        - (void)saveContext;  
        - (NSURL *)applicationDocumentsDirectory;  

        @end

4. 次に、**QSAppDelegate.m** の内容を以下のコードに置き換えます。このコードは、Xcode で新しいアプリケーションを作成し、[Use Core Data] チェック ボックスをオンにしたときに取得されるコードとほぼ同じです。**_managedObjectContext** の初期化でコンカレント タイプのプライベート キューを使用している点だけが異なります。この変更により、Core Data を使用する準備がほぼ整いましたが、まだ Core Data を使用して何もしていません。

        #import "QSAppDelegate.h"

        @implementation QSAppDelegate

        @synthesize managedObjectContext = _managedObjectContext;
        @synthesize managedObjectModel = _managedObjectModel;
        @synthesize persistentStoreCoordinator = _persistentStoreCoordinator;

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            return YES;
        }

        - (void)saveContext
        {
            NSError *error = nil;
            NSManagedObjectContext *managedObjectContext = self.managedObjectContext;
            if (managedObjectContext != nil) {
                if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
                    // Replace this implementation with code to handle the error appropriately.
                    // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                    NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                    abort();
                }
            }
        }

        #pragma mark - Core Data stack

        // Returns the managed object context for the application.
        // If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
        - (NSManagedObjectContext *)managedObjectContext
        {
            if (_managedObjectContext != nil) {
                return _managedObjectContext;
            }

            NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
            if (coordinator != nil) {
                _managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSPrivateQueueConcurrencyType];
                [_managedObjectContext setPersistentStoreCoordinator:coordinator];
            }
            return _managedObjectContext;
        }

        // Returns the managed object model for the application.
        // If the model doesn't already exist, it is created from the application's model.
        - (NSManagedObjectModel *)managedObjectModel
        {
            if (_managedObjectModel != nil) {
                return _managedObjectModel;
            }
            NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"QSTodoDataModel" withExtension:@"momd"];
            _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
            return _managedObjectModel;
        }

        // Returns the persistent store coordinator for the application.
        // If the coordinator doesn't already exist, it is created and the application's store added to it.
        - (NSPersistentStoreCoordinator *)persistentStoreCoordinator
        {
            if (_persistentStoreCoordinator != nil) {
                return _persistentStoreCoordinator;
            }

            NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"qstodoitem.sqlite"];

            NSError *error = nil;
            _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
            if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
                /*
                 Replace this implementation with code to handle the error appropriately.

                 abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.

                 Typical reasons for an error here include:
                 * The persistent store is not accessible;
                 * The schema for the persistent store is incompatible with current managed object model.
                 Check the error message to determine what the actual problem was.

                 If the persistent store is not accessible, there is typically something wrong with the file path. Often, a file URL is pointing into the application's resources directory instead of a writeable directory.

                 If you encounter schema incompatibility errors during development, you can reduce their frequency by:
                 * Simply deleting the existing store:
                 [[NSFileManager defaultManager] removeItemAtURL:storeURL error:nil]

                 * Performing automatic lightweight migration by passing the following dictionary as the options parameter:
                 @{NSMigratePersistentStoresAutomaticallyOption:@YES, NSInferMappingModelAutomaticallyOption:@YES}

                 Lightweight migration will only work for a limited set of schema changes; consult "Core Data Model Versioning and Data Migration Programming Guide" for details.

                 */

                NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                abort();
            }

            return _persistentStoreCoordinator;
        }

        #pragma mark - Application's Documents directory

        // Returns the URL to the application's Documents directory.
        - (NSURL *)applicationDocumentsDirectory
        {
            return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
        }

        @end

## <a name="defining-core-data"></a>Core Data モデルの定義

1. Core Data を使用したアプリケーション設定の続きとして、データ モデルを定義しましょう。すぐにこのデータ モデルの使用を開始できるわけではありません。最初に、Core Data のモデル (スキーマ) を定義します。この定義を行うには、**[File]**、**[New File]** を順にクリックし、**[Core Data]** セクションで **[Data Model]** を選択します。ファイル名を入力するように求められたら、「**QSTodoDataModel.xcdatamodeld**」と入力します。

      ![][defining-core-data-main-screen]

2. 次に、必要な実際のエンティティ (テーブル) を定義します。ここでは、Core Data Model Editor を使用して 3 つのテーブル (エンティティ) を作成します。詳細については、[Core Data Model Editor のヘルプ]を参照してください。

  * TodoItem: 項目自体の格納用
  * MS_TableOperations: サーバーと同期する必要がある項目の追跡用 (オフライン機能の動作に必要)
  * MS_TableOperationErrors: オフライン同期中に発生したすべてのエラーの追跡用 (オフライン機能の動作に必要)

      ![][defining-core-data-model-editor]

3. 次に示すように、3 つのエンティティを定義します。モデルを保存し、プロジェクトをビルドして、問題がないことを確認します。これで、Core Data を使用するためのアプリケーションの設定が完了しましたが、アプリケーションはまだ Core Data を使用していません。

      ![][defining-core-data-todoitem-entity]

      ![][defining-core-data-tableoperations-entity]

      ![][defining-core-data-tableoperationerrors-entity]


    **TodoItem**

    | 属性  |  型   |
    |----------- |  ------ |
    | id         | 文字列  |
    | complete   | ブール値 |
    | text       | 文字列  |
    | ms_version | 文字列  |

    **MS_TableOperations**

    | 属性  |    型     |
    |----------- |   ------    |
    | id         | 整数 64  |
    | properties | バイナリ データ |
    | itemId     | 文字列      |
    | table      | 文字列      |

    **MS_TableOperationErrors**

    | 属性  |    型     |
    |----------- |   ------    |
    | id         | 文字列      |
    | properties | バイナリ データ |

## <a name="setup-sync"></a> 同期テーブルと同期コンテキストの初期化と使用

1. オフラインのデータ キャッシュを開始するために、モバイル サービスにアクセスする際に **MSTable** の代わりに **MSSyncTable** を使用しましょう。通常の **MSTable** とは異なり、同期テーブルは論理テーブルのように、ローカルで加えられた変更をリモート テーブルにプッシュしたり、ローカルに変更をプルしたりすることができます。**QSTodoService.h** で、**table** プロパティの定義を削除します。

        @property (nonatomic, strong)   MSTable *table;

    Add a new line to define the **syncTable** property:

        @property (nonatomic, strong)   MSTable *syncTable;

2. Add the following import statement at the top of **QSTodoService.m**:

        #import "QSAppDelegate.h"

3. **QSTodoService.m** で、**init** の次の 2 行を削除します。

        // Create an MSTable instance to allow us to work with the TodoItem table
        self.table = [_client tableWithName:@"TodoItem"];

    Instead, add these two new lines in its place:

        // Create an MSSyncTable instance to allow us to work with the TodoItem table
        self.syncTable = [self.client syncTableWithName:@"TodoItem"];

4. 次に、**QSTodoService.m** に戻り、先ほど実装した Core Data ベースのデータ ストアを使用して、**MSClient** 内の同期コンテキストを初期化します。このコンテキストは、ローカルでどの項目が変更されたかを追跡し、プッシュ操作が開始されたときにそれらをサーバーに送信する役目を果たします。コンテキストを初期化するには、データ ソース (プロトコルの **MSCoreDataStore** 実装) とオプションの **MSSyncContextDelegate** 実装が必要です。先ほど挿入した 2 行の直前に、以下の行を挿入します。

        QSAppDelegate *delegate = (QSAppDelegate *)[[UIApplication sharedApplication] delegate];
        NSManagedObjectContext *context = delegate.managedObjectContext;
        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

5. 次に、通常のテーブルの代わりに同期テーブルを使用するように、**QSTodoService.m** の操作を更新します。まず、**refreshDataOnSuccess** を次の実装に置き換えます。これによってサービスからデータが取得されます。同期テーブルを使用するように更新したら、条件と一致する項目のみを取得するよう同期テーブルに指示し、ローカルの同期テーブルからサービスの **items** プロパティへのデータの読み込みを開始します。このコードでは、  **refreshDataOnSuccess** がリモート テーブルからローカル (同期) テーブルにデータをプルします。通常は、必要でないかもしれない情報でクライアントに過剰な負荷をかけないように、テーブルのサブセットのみをプルする必要があります。

    この操作とこれ以降で実行する残りの操作では、完了ブロックの呼び出しをメイン スレッドの **dispatch_async** 呼び出しにラップしています。同期コンテキストを初期化する際にコールバック パラメーターを渡していないため、フレームワークによって既定のシリアル キューが作成され、すべての syncTable 操作の結果がバックグラウンド スレッドにディスパッチされます。UI コンポーネントを変更する際は、コードを UI スレッドにディスパッチする必要があります。

          -(void) refreshDataOnSuccess:(QSCompletionBlock)completion
          {
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              [query orderByAscending:@"text"];
              [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
                  [self logErrorIfNotNil:error];

                  self.items = [result.items mutableCopy];

                  // Let the caller know that we finished
                  dispatch_async(dispatch_get_main_queue(), ^{
                      completion();
                  });
              }];
          }

6. 次に、**QSTodoService.m** 内の **addItem** を次のように置き換えます。この変更により、操作がキューに追加されます。操作が実行されると、変更がリモート サービスにプッシュされ、すべてのユーザーに表示されるようになります。

        -(void)addItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.syncTable insert:item completion:^(NSDictionary *result, NSError *error)
             {
                 [self logErrorIfNotNil:error];

                 NSUInteger index = [items count];
                 [(NSMutableArray *)items insertObject:result atIndex:index];

                 // Let the caller know that we finished
                 dispatch_async(dispatch_get_main_queue(), ^{
                     completion(index);
                 });
             }];
        }

7. **QSTodoService.m** 内の **completeItem** を次のように更新します。**MSTable** とは異なり、**MSSyncTable** に対する **update** 操作の完了ブロックには更新された項目が含まれません。**MSTable** の場合は、更新対象の項目がサーバーで変更され、その変更がクライアントに反映されます。**MSSyncTable** の場合は、更新対象の項目は変更されず、完了ブロックにパラメーターはありません。

        -(void) completeItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@YES forKey:@"complete"];

            // Replace the original in the items array
            NSUInteger index = [items indexOfObjectIdenticalTo:item];
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.syncTable update:mutable completion:^(NSError *error) {

                [self logErrorIfNotNil:error];

                NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
                if (index != NSNotFound)
                {
                    [mutableItems removeObjectAtIndex:index];
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });

            }];
        }

8. 以下の **syncData** 操作宣言を **QSTodoService.h** に追加します。

        - (void)syncData:(QSCompletionBlock)completion;

     Add the corresponding implementation of **syncData** to **QSTodoService.m**. We're adding this operation to update the sync table with remote changes.

          -(void)syncData:(QSCompletionBlock)completion
           {
              // Create a predicate that finds items where complete is false
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              // Pulls data from the remote server into the local table. We're only
              // pulling the items which we want to display (complete == NO).
              [self.syncTable pullWithQuery:query completion:^(NSError *error) {
                  [self logErrorIfNotNil:error];
                  [self refreshDataOnSuccess:completion];
           }];
          }

9. Back in **QSTodoListViewController.m**, change the implementation of **refresh** to call **syncData** instead of **refreshDataOnSuccess**:

        -(void) refresh
        {
            [self.refreshControl beginRefreshing];
            [self.todoService syncData:^
             {
                  [self.refreshControl endRefreshing];
                  [self.tableView reloadData];
             }];
        }

10. **QSTodoListViewController.m** に戻り、**viewDidLoad** 操作の最後にある **[self refresh]** の呼び出しを次のコードに置き換えます。

        // load the local data, but don't pull from server
        [self.todoService refreshDataOnSuccess:^
         {
             [self.refreshControl endRefreshing];
             [self.tableView reloadData];
         }];

11. では、アプリケーションをオフラインで実際にテストしてみましょう。アプリケーションにいくつかの項目を追加し、Azure の管理ポータルにアクセスして、アプリケーションの **[データ]** タブを確認してください。まだ項目が追加されていないことがわかります。

12. 次に、上部からドラッグしてアプリの更新ジェスチャを実行します。再度 Azure の管理ポータルにアクセスし、**[データ]** タブを確認してください。今度はデータがクラウドに保存されていることがわかります。項目を追加した後で (または、アプリケーションで項目を編集する機能が有効になっている場合は、項目を編集した後で) アプリケーションを閉じることもできます。アプリケーションを再度起動すると、アプリケーションがサーバーと同期し、変更が保存されます。

13. クライアントがローカルで項目に変更を加えると、それらの変更はサーバーに送信されるために同期コンテキストに格納されます。追跡された変更が *push* 操作によってリモート サーバーに送信されますが、ここではサーバーへの push 呼び出しはありません。ただし、*一般的には、プルが実行される前に保留中の操作がサーバーにプッシュされるため、*競合を避けるためにプッシュが自動的に実行されます。このアプリケーションに *push* の明示的な呼び出しがないのはそのためです。

## <a name="test-app"></a>アプリケーションをテストする

最後に、アプリケーションをオフラインでテストしてみましょう。アプリケーションに項目をいくつか追加します。ポータルに移動し、データを参照します (または PostMan や Fiddler などのネットワーク ツールを使用して、このテーブルに直接クエリを実行します)。

まだ項目がサービスに追加されていないことがわかります。上部からドラッグして、アプリケーション インスタンスの更新ジェスチャを実行します。今度はデータがクラウドに保存されていることがわかります。項目をいくつか追加した後でアプリケーションを閉じることもできます。アプリケーションを再度起動すると、サーバーと同期し、変更が保存されます。

## 次のステップ

* [モバイル サービスのオフライン サポートでの競合を処理する]

<!-- URLs. -->

[サンプルのクイック スタート アプリケーションの取得]: #get-app
[Preview SDK のダウンロードとフレームワークの更新]: #update-app
[Core Data の設定]: #setup-core-data
[Core Data モデルの定義]: #defining-core-data
[同期テーブルと同期コンテキストの初期化と使用]: #setup-sync
[アプリケーションをテストする]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png




[Core Data Model Editor のヘルプ]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[コンセント接続の作成]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[ユーザー インターフェイスの構築]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Storyboard のシーン間へのセグエの追加]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Storyboard へのシーンの追加]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Preview SDK はここからダウンロードできます]: http://aka.ms/Gc6fex
[iOS 用 Mobile Services クライアント ライブラリの使用方法]: /ja-jp/documentation/articles/mobile-services-ios-how-to-use-client-library/
[iOS 用のオフライン サンプルの使用]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611


[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started-data/
[モバイル サービスのオフライン サポートでの競合を処理する]: /ja-jp/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
