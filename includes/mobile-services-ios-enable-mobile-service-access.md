モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1.  [モバイル サービス iOS SDK][モバイル サービス iOS SDK] をまだインストールしていない場合は、ここでインストールしてください。

2.  Xcode の Project Navigator で、Quickstart フォルダーにある TodoService.m および TodoService.h ファイルを開き、次の import ステートメントを追加します。

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  ToDoService.h ファイルで、次のコメントがあるコード行を見つけます。

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

    このコメントの後ろに次のコード行を追加します。

        @property (nonatomic, strong)   MSClient *client;

    これで、サービスに接続された MSClient を表すプロパティが作成されます。

4.  TodoService.m ファイルで、次のコメントがあるコード行を見つけます。

        // Create an MSTable property for your items. 

    このコメントの後ろに、@interface 宣言の内部に次のコード行を追加します。

        @property (nonatomic, strong)   MSTable *table;

    これで、モバイル サービス テーブルのプロパティ表現が作成されます。

5.  管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

6.  **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

    ![][0]

    これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

7.  Xcode に戻り、TodoService.m ファイルを開いて、次のコメントがあるコード行を見つけます。

        // Initialize the Mobile Service client with your URL and key.

    このコメントの後ろに次のコード行を追加します。

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" withApplicationKey:@"APPKEY"];

    これで、モバイル サービス クライアントのインスタンスが作成されます。

8.  このコードの **APPURL** と **APPKEY** の値を、手順 6 で取得したモバイル サービスの URL とアプリケーション キーで置き換えます。

9.  次のコメントがあるコード行を見つけます。

        // Create an MSTable instance to allow us to work with the TodoItem table.

    このコメントの後ろに次のコード行を追加します。

        self.table = [self.client getTable:@"TodoItem"];

    これで、TodoItem テーブルのインスタンスが作成されます。

10. 次のコメントがあるコード行を見つけます。

        // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    このコメントの後ろに次のコード行を追加します。

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    これで、まだ完了していないタスクをすべて返すクエリが作成されます。

11. 次のコメントがあるコード行を見つけます。

        // Query the TodoItem table and update the items property with the results from the service.

    そのコメントとそれに続く **completion** ブロックの呼び出しを、次のコードに置き換えます。

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWhere:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error)
        {
           self.items = [items mutableCopy];
           completion();
        }]; 

12. **addItem** メソッドを見つけ、メソッドの本文に次のコードを追加します。

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    このコードにより、挿入要求がモバイル サービスに送信されます。

13. **completeItem** メソッドを見つけ、メソッドの本文に次のコードを追加します。

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

    このコードにより、完了マークが付けられた後で TodoItems が削除されます。

バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。

  [モバイル サービス iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [0]: ./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png
