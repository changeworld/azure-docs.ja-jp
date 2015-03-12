
モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1. [モバイル サービス iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533) をまだインストールしていない場合は、ここでインストールしてください。インストールしたら、WindowsAzureMobileServices.framework ディレクトリをコピーし、ダウンロードしたプロジェクトに含まれる WindowsAzureMobileServices.framework ディレクトリを上書きします。この方法で、最新の Azure Mobile Services SDK を使用します。

2. TodoService.h ファイルで、次のコメントがあるコード行を見つけます。

        // TODO - create an MSClient proeprty

   	このコメントの後ろに次のコード行を追加します。

        @property (nonatomic, strong)   MSClient *client;

   	これで、サービスに接続された MSClient を表すプロパティが作成されます。

3. TodoService.m ファイルで、次のコメントがあるコード行を見つけます。

        // TODO - create an MSTable property for your items

   	このコメントの後ろに、@interface 宣言の内部に次のコード行を追加します。

        @property (nonatomic, strong)   MSTable *table;

   	これで、モバイル サービス テーブルのプロパティ表現が作成されます。

4. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

5. **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

   	![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

  	これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

6. Xcode に戻り、TodoService.m ファイルを開いて、次のコメントがあるコード行を見つけます。

        // Initialize the Mobile Service client with your URL and key.

    このコメントの後ろに次のコード行を追加します。

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    これで、モバイル サービス クライアントのインスタンスが作成されます。

7. このコードの **APPURL** と **APPKEY** の値を、手順 6 で取得したモバイル サービスの URL とアプリケーション キーで置き換えます。

8. 次のコメントがあるコード行を見つけます。

        // Create an MSTable instance to allow us to work with the TodoItem table.

    このコメントの後ろに次のコード行を追加します。

        self.table = [self.client tableWithName:@"TodoItem"];

    これで、TodoItem テーブルのインスタンスが作成されます。

9. 次のコメントがあるコード行を見つけます。

 	    // Create a predicate that finds items where complete is false

    このコメントの後ろに次のコード行を追加します。

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    これで、まだ完了していないタスクをすべて返すクエリが作成されます。

10. 次のコメントがあるコード行を見つけます。

        // Query the TodoItem table and update the items property with the results from the service

   	そのコメントとそれに続く **completion** ブロックの呼び出しを、次のコードに置き換えます。

        [self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		{
		   self.items = [results mutableCopy];
           completion();
        }];

11. **addItem** メソッドを見つけ、メソッドの本体を次のコードに置き換えます。

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    このコードにより、挿入要求がモバイル サービスに送信されます。

12. **completeItem** メソッドを探し、次のコメントがあるコード行を見つけます。

        // Update the item in the TodoItem table and remove from the items array on completion

    メソッド本文のその位置から末尾までを次のコードに置き換えます。

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
	    }];

   	このコードにより、完了マークが付けられた後で TodoItems が削除されます。

13. TodoListController.m で、**onAdd** メソッドを見つけ、次のコードで上書きします。

      - (IBAction)onAdd:(id)sender
      {
          if (itemText.text.length  == 0)
          {
              return;
          }

          NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
          UITableView *view = self.tableView;
          [self.todoService addItem:item completion:^(NSUInteger index)
          {
              NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
              [view insertRowsAtIndexPaths:@[ indexPath ]
                          withRowAnimation:UITableViewRowAnimationTop];
          }];

          itemText.text = @"";
      }


バックエンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、モバイル サービスに対してアプリケーションをテストします。
<!--HONumber=42-->
