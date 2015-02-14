
<h2><a name="update-app"></a>カスタム API を呼び出すようにアプリケーションを更新する</h2>

1. カスタム API を呼び出すボタンを作成します。**[Utilities]** ウィンドウの下部にある **[Object Library]** から **[Round Rect Button]** をドラッグし、テキスト フィールドの下または横に配置します。ダブルクリックし、「**All**」というテキストを追加します。 

	新しいボタン **[All]** が追加されます。

2. **QSTodoService.m** コード ファイルを開き、 `refreshDataOnSuccess` メソッドを見つけて、次のコードが含まれていることを確認します。

		- (void)refreshDataOnSuccess:(QSCompletionBlock)completion
		{		   
		    // Create a predicate that finds items where complete is false
		    NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
		    
		    // Query the TodoItem table and update the items property with the results from the service
		    [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		    {
		        [self logErrorIfNotNil:error];
		        
		        items = [results mutableCopy];
		        
		        // Let the caller know that we finished
		        completion();
		    }];		    		    		    
		}

	ここでは、完了済みの項目が返されないように、項目をフィルターで処理しています。

3. 次に、このオブジェクトをビュー コントローラーのソース コードに接続します。**新しい **[All]** ボタンを Ctrl キーを押しながらクリック**し、**QSTodoListViewController.h** の `@end` 行の前にマウスをドラッグします。**QSTodoListViewController** 内の  `onCompleteAll` という名前の新しい**アクション**にオブジェクトを接続します。Xcode により、次の行が `@end` 行の前に自動的に挿入されます。

		   - (IBAction)onCompleteAll:(id)sender;

4. この  `onCompleteAll` メソッドの目的は、新しいボタンの Click イベントを処理することです。これにより、カスタム クラスに追加する新しい  `completeAll` メソッドが呼び出され、新しいカスタム API に POST 要求が送信されます。カスタム API から返された結果は、メッセージ ダイアログに表示されます。エラーが発生した場合はそれらも表示されます。**QSTodoListViewController.m** を編集し、次の実装を `@end` 行の前に追加します。

		   - (IBAction)onCompleteAll:(id)sender {
		    [self.todoService completeAll:^(id result, NSHTTPURLResponse* response, NSError* error)
		     {
		         if (error)
		         {
		             NSString* errorMessage = @"There was a problem! ";
		             errorMessage = [errorMessage stringByAppendingString:[error localizedDescription]];
		             UIAlertView* myAlert = [[UIAlertView alloc]
		                                     initWithTitle:@"Error!"
		                                     message:errorMessage
		                                     delegate:nil
		                                     cancelButtonTitle:@"Okay"
		                                     otherButtonTitles:nil];
		             [myAlert show];
		             [self refresh];
		         } else {
		             NSString* successMessage = [NSString stringWithFormat:@"%d items marked as complete", [[result objectForKey:@"count"] integerValue]];					 
		             UIAlertView* myAlert = [[UIAlertView alloc]
		                                     initWithTitle:@"Success!"
		                                     message:successMessage
		                                     delegate:nil
		                                     cancelButtonTitle:@"Okay"
		                                     otherButtonTitles:nil];
		             [myAlert show];
		             [self refresh];
		         }
		     }];
  		   }

5. 前のコードで、**QSTodoService** でまだ定義されていない新しいメソッド  `completeAll` を参照していることに注意してください。**QSTodoService.h** を編集し、次の行を `@end` 行の前に追加します。

		- (void) completeAll:(MSAPIBlock)completion;

6. **QSTodoService.m** の `@end` 行の前に、 `completeAll` の対応する実装を追加します。iOS は、任意の型の JSON シリアル化をサポートしない点で JavaScript に似ています。そのため、カスタム API を呼び出すための API は非常にシンプルで、 `invokeAPI` メソッドで構成されています。 

		- (void) completeAll:(MSAPIBlock)completion
		{
		    [self.client
		     invokeAPI:@"completeall"
		     body:nil
		     HTTPMethod:@"POST"
		     parameters:nil
		     headers:nil
		     completion:completion ];
		}

## <a name="test-app"></a>アプリケーションをテストする

1. Xcode で、iPhone または iPad に展開するエミュレーターを選択し、**[Run]** ボタン (または **Command + R** キー) を押してプロジェクトをリビルドし、アプリケーションを起動します。これにより、モバイル サービスから項目にクエリを行う Windows Azure のモバイル サービス クライアントが、iOS SDK でビルドされ、実行されます。

2. テキスト フィールドにテキストを入力し、**[+]** をクリックします。これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. 前の手順を繰り返して、複数の項目をリストに追加します。

4. **[All]** ボタンをタップします。完了としてマークされた項目の数を示す警告ボックスが表示され、フィルター処理済みのクエリが再度実行されて、すべての項目がリストから消去されます。

  	![](./media/mobile-services-ios-call-custom-api/mobile-custom-api-ios-completed.png)
<!--HONumber=42-->
