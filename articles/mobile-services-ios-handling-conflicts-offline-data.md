<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Handle Conflicts with offline data in Mobile Services におけるオフライン データとの競合の処理 (iOS) | モバイル デベロッパー センター" metaKeywords="" description="Azure Mobile Services を使用して、iOS アプリケーションのオフライン データの同期時に生じる競合を処理する方法を説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Mobile Services のオフライン データとの競合の処理" authors="krisragh" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="dotnet" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# モバイル サービスでのオフライン データの同期との競合の処理

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows ストア C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS" class="current">iOS</a>
</div>

このトピックでは、Azure Mobile Services のオフライン機能を使用しているときに、データを同期し、競合を処理する方法について説明します。このチュートリアルは、前の「[オフライン データの使用][オフライン データの使用]」チュートリアルの手順およびサンプル アプリを基に作成されています。このチュートリアルを開始する前に、「[オフライン データの使用][オフライン データの使用]」チュートリアルを完了している必要があります。

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

## 「オフライン データの使用」チュートリアルの完了

「[オフライン データの使用][オフライン データの使用]」チュートリアルの手順に従って、そのプロジェクトを完了します。そのチュートリアルで完了したプロジェクトを、このチュートリアルの起点として使用します。

## アプリ プロジェクトを編集可能に更新

「[オフライン データの使用][オフライン データの使用]」で完了したプロジェクトを更新し、項目を編集可能にします。現時点では、この同じアプリを 2 台の電話で稼働し、両方の電話で同じ項目をローカルに変更して、変更内容をサーバーにプッシュすると、競合が生じて失敗します。

SDK のオフライン同期機能を使用すると、コードを介してこのような競合を扱い、競合している項目への対処方法を動的に判断できます。クイックスタート プロジェクトを変更することで、この機能を体験できます。

### Todo リスト ビュー コントローラーの更新

1.  iPhone ストーリーボードを更新します。iPad で作業している場合は、iPad ストーリーボードで同じ手順に従ってください。

2.  Xcode プロジェクト ナビゲーターで **MainStoryboard\_iPhone.storyboard** を選択し、次に、**[Todo リスト ビュー コントローラー]** を選択します。最上部のメニューで、**[エディタ]、[埋め込み]、[ナビゲーション コントローラ]** の順にクリックします。

    ![][0]

3.  **[Todo リスト ビュー コントローラー]** で、テーブル ビュー セルを選択して、そのアクセサリ モードを**ディスクロージャー インジケーター**に設定します。ディスクロージャー インジケーターは、関連付けられたテーブル ビュー コントローラーをタップすると、新しいビューが表示されることをユーザーに示します。ディスクロージャー インジケーターは、イベントを生成しません。

    ![][1]

4.  **TodoListViewController.m** で、次の操作とそのコンテンツを一緒に削除します。それらは必要ありません。

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### Todo 項目ビュー コントローラー

1.  **UIViewController** から派生した **QSTodoItemViewController** という新しい Objective-C クラスをプロジェクトに追加します。

    ![][2]

    ![][3]

2.  **QSTodoItemViewController.h** で、変更する項目を保持するためのプロパティを追加します。

        @property (nonatomic, weak) NSMutableDictionary *item;

3.  **QSTodoItemViewController.m** で、編集する 2 つの todo 項目のフィールド (todo 項目自体の完了ステータスとテキスト) に対応した 2 つのプライベート プロパティを追加します。

        @interface QSTodoItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

4.  **QSTodoItemViewController.m** で、**viewDidLoad** のスタブ実装を次のコードに更新します。

        - (void)viewDidLoad
        {
            [super viewDidLoad];

            UINavigationItem *nav = [self navigationItem];
            [nav setTitle:@"Todo Item"];

            NSDictionary *theItem = [self item];
            [self.itemText setText:[theItem objectForKey:@"text"]];

            BOOL isComplete = [[theItem objectForKey:@"complete"] boolValue];
            [self.itemComplete setSelectedSegmentIndex:(isComplete ? 0 : 1)];

            [self.itemComplete addTarget:self
                                  action:@selector(completedValueChanged:)
                        forControlEvents:UIControlEventValueChanged];
        }

5.  **QSTodoItemViewController.m** で、複数のイベントを処理するための 4 つのメソッドを追加します。

        - (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }

        - (BOOL)textFieldShouldReturn:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }


        - (void)completedValueChanged:(id)sender {
            [[self view] endEditing:YES];
        }

        - (void)viewWillDisappear:(BOOL)animated {
            [self.item setValue:[self.itemText text] forKey:@"text"];
            [self.item setValue:[NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0] forKey:@"complete"];
        }

### Todo 項目ビュー コントローラーとセグエのストーリーボードへの追加

1.  プロジェクト ナビゲーターを使用して、**MainStoryboard\_iPhone.storyboard** ファイルに戻ります。

2.  Todo 項目の新しいビュー コントローラーをストーリーボードの既存の **Todo リスト ビュー コントローラー**の右に追加します。この新しいビュー コントローラーのカスタム クラスを **QSTodoItemViewController** に設定します。詳細については、「[Adding a Scene to a Storyboard (シーンのストーリーボードへの追加)][Adding a Scene to a Storyboard (シーンのストーリーボードへの追加)]」を参照してください。

3.  プッシュ セグエを **Todo リスト ビュー コントローラー**から **Todo 項目ビュー コントローラー**に追加し、セグエに **detailSegue** という名前を付けます。詳細については、「[Adding a Segue Between Scenes in a Storyboard (ストーリーボード内のシーン間へのセグエの追加)][Adding a Segue Between Scenes in a Storyboard (ストーリーボード内のシーン間へのセグエの追加)]」を参照してください。このセグエは、元のビュー コントローラーのセルまたはボタンからは作成しないでください。代わりに、ストーリーボード インターフェイスの **Todo リスト ビュー コントローラー**の下にあるビュー コントローラー アイコンを Ctrl キーを押しながら、**Todo 項目ビュー コントローラー**へドラッグします。間違ってセルからセグエを作成した場合は、アプリの実行時にセグエが 2 回トリガーされ、次のエラーが表示されます。

        Nested push animation can result in corrupted navigation bar

4.  項目テキスト用のテキスト フィールドと完了ステータス用のセグメント化コントロールを、ラベルと共に新しい **Todo 項目ビュー コントローラー**に追加します。セグメント化コントロールで、**セグメント 0** のタイトルを **Yes** に設定し、**セグメント 1** のタイトルを **No** に設定します。これらの新しいフィールドをコードのアウトレットに接続します。詳細については、「[Build a User Interface (ユーザー インターフェイスの作成)][Build a User Interface (ユーザー インターフェイスの作成)]」および「[Segmented Controls (セグメント化コントロール)][Segmented Controls (セグメント化コントロール)]」を参照してください。

    ![][4]

5.  これらの新しいフィールドを、既に **QSTodoItemViewController.m** に追加されている対応するアウトレットに接続します。項目テキスト フィールドを **itemText** アウトレットに接続し、完了ステータス セグメント化コントロールを **itemComplete** アウトレットに接続します。詳細については、「[Creating an Outlet Connection (アウトレット接続の作成)][Creating an Outlet Connection (アウトレット接続の作成)]」を参照してください。

6.  テキスト フィールドのデリゲートをビュー コントローラーに設定します。これにより、最終的に項目を編集し、Return キーを押したときに、テキスト フィールドが撤退します。テキスト フィールドからストーリーボード インターフェイスの **Todo 項目ビュー コントローラー**の下にあるビュー コントローラー アイコンまで Ctrl キーを押しながらドラッグし、デリゲート アウトレットを選択します。この操作はストーリーボードに、このテキスト フィールドのデリゲートがこのビュー コントローラーであることを示します。

7.  ここまでで加えたすべての変更を反映してアプリが動作することを確認します。シミュレーターでアプリを実行します。項目を todo リストに追加し、それらをクリックします。項目ビュー コントローラー (現在は空) が表示されます。

    ![][5]

    ![][6]

### 項目の詳細の Todo 項目ビュー コントローラーへの追加

1.  **QSTodoListViewController.m** から **QSTodoItemViewController** を参照します。そのため、**QSTodoListViewController.m** で、**QSTodoItemViewController.h** をインポートするための行を追加します。

        #import "QSTodoItemViewController.h"

2.  **QSTodoListViewController.m** で、編集する項目を格納するための 2 つの新しいプロパティを **QSTodoListViewController** に追加します。

        @property (nonatomic)           NSInteger       editedItemIndex;
        @property (strong, nonatomic)   NSMutableDictionary *editedItem;

3.  編集した項目を保存してから、セグエを呼び出して詳細ビューを表示するために、**QSTodoListViewController.m** で **tableView:didSelectRowAtIndexPath:** を実装します。

          - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
              self.editedItemIndex = [indexPath row];
              self.editedItem = [[self.todoService.items objectAtIndex:[indexPath row]] mutableCopy];

              [self performSegueWithIdentifier:@"detailSegue" sender:self];
          }

4.  **Todo 項目ビュー コントローラー**に項目を渡すため、**QSTodoListViewController.m** で **prepareForSegue:sender:** を実装します。

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSTodoItemViewController *ivc = (QSTodoItemViewController *)[segue destinationViewController];
                ivc.item = self.editedItem;
            }
        }

5.  ここまでで加えたすべての変更を反映してアプリが動作することを確認します。シミュレーターでアプリを実行します。項目を todo リストに追加し、それらをクリックします。空でない項目ビュー コントローラーが表示され、todo 項目の詳細が示されます。

    ![][7]

### 編集内容を保存するためのサポートの追加

1.  ナビゲーション ビューで [戻る] ボタンをクリックすると、編集内容は失われます。データは詳細ビューに送信しましたが、データはマスター ビューに送信されていません。既にポインターを項目のコピーに渡しているため、そのポインターを使用して、項目に加えられた更新の一覧を取得し、それを使用してサーバーを更新できます。開始するには、最初に、**QSTodoService.m** のサーバー ラッパー クラスの **QSTodoService** で、**completeItem** 操作を削除し、新しい **updateItem** 操作を追加して更新します。operation.これは、**completeItem** が完了として項目をマークするのみのため、代わりに、**updateItem** によって項目を更新します。

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Replace the original in the items array
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:item completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                NSInteger index = -1;
                if (!error) {
                    BOOL isComplete = [[item objectForKey:@"complete"] boolValue];
                    NSString *remoteId = [item objectForKey:@"id"];
                    index = [items indexOfObjectPassingTest:^BOOL(id obj, NSUInteger idx, BOOL *stop) {
                        return [remoteId isEqualToString:[obj objectForKey:@"id"]];
                    }];

                    if (index != NSNotFound && isComplete)
                    {
                        [mutableItems removeObjectAtIndex:index];
                    }
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });
            }];
        }

2.  **QSTodoService.h** から **completeItem** の宣言を削除し、この **updateItem** の宣言を追加します。

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion;

3.  **QSTodoListViewController.m** で、操作 **viewWillAppear** を追加します。これは、詳細ビュー コントローラーから戻った後、マスター ビューが表示されるたびに更新メソッドを呼び出します。

        - (void)viewWillAppear:(BOOL)animated {
            if (self.editedItem && self.editedItemIndex >= 0) {
                // Returning from the details view controller
                NSDictionary *item = [self.todoService.items objectAtIndex:self.editedItemIndex];

                BOOL changed = ![item isEqualToDictionary:self.editedItem];
                if (changed) {
                    [self.tableView setUserInteractionEnabled:NO];

                    // Change the appearance to look greyed out until we remove the item
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:self.editedItemIndex inSection:0];

                    UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:indexPath];
                    cell.textLabel.textColor = [UIColor grayColor];

                    // Ask the todoService to update the item, and remove the row if it's been completed
                    [self.todoService updateItem:self.editedItem atIndex:self.editedItemIndex completion:^(NSUInteger index) {
                        if ([[self.editedItem objectForKey:@"complete"] boolValue]) {
                            // Remove the row from the UITableView
                            [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                                  withRowAnimation:UITableViewRowAnimationTop];
                        } else {
                            [self.tableView reloadRowsAtIndexPaths:[NSArray arrayWithObject:indexPath]
                                                  withRowAnimation:UITableViewRowAnimationAutomatic];
                        }

                        [self.tableView setUserInteractionEnabled:YES];

                        self.editedItem = nil;
                        self.editedItemIndex = -1;
                    }];
                } else {
                    self.editedItem = nil;
                    self.editedItemIndex = -1;
                }
            }
        }

4.  ここで、アプリをテストします。ここまでで加えたすべての変更を反映してアプリが動作することを確認します。シミュレーターでアプリを実行します。項目を todo リストに追加し、それらをクリックします。項目の編集を試みて、元に戻ります。アプリのマスター ビューで項目の説明が更新されていることを確認します。下へのドラッグ ジェスチャでアプリを更新し、編集内容がクラウド内で反映されていることを確認します。

### 競合処理の問題

1.  2 台の異なるクライアントが、同時にデータの同じ部分の変更を試みると何が起きるかを調べます。以下に一覧した例では、項目 "Hello world 3" があります。これを一方のデバイスでは "Hello world 13" に変更し、もう一方のデバイスでは "Hello world 23" に変更します。

    ![][8]

2.  2 台の iOS デバイス、またはシミュレーターと 1 台の iOS デバイスの 2 か所でアプリを起動します。テスト用の物理デバイスがない場合は、シミュレーター内で 1 つのインスタンスを起動し、REST クライアントを使用して、PATCH 要求を Mobile Service へ送信します。PATCH 要求の URL には、Mobile Service の名前、todo 項目テーブルの名前、および編集中の todo 項目テーブルの ID が反映され、x-zumo-application ヘッダーはアプリケーション キーとなります。

        PATCH https://todolist.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: shYOoDFdKhmzLEbnMQqPYrCLhwGOVA10

        {
            "id": "D265929E-B17B-42D1-8FAB-D0ADF26486FA",
            "text": "Hello world 23"
        }

3.  ここで、アプリの 2 つのインスタンス内の項目を更新します。Xcode コードの出力ログにエラーが表示されます。

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

これは、**pullWithQuery:completion:** を呼び出したときの完了ブロックに起因しており、エラー パラメーターは nil ではないため、**NSLog** を介した出力にエラーが返されました。

### 競合処理をサポートするための QSTodoService の更新

1.  クライアント内で競合を扱うことで、競合に対処する方法を決定します。これを実行するため、**MSSyncContextDelegate** プロトコルを実装します。**QSTodoService.h** と **QSTodoService.m** の両方で、\*\*(QSTodoService \*)defaultService;\*\* 工場出荷時のメソッド宣言を以下のステートメントに変更します。これによって、パラメーターとして同期コンテキスト デリゲートを受信できます。

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2.  **QSTodoService.m** で、以下に示すように、**init** 行を変更します。これも、パラメーターとして同期コンテキスト デリゲートを受信します。

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3.  **QSTodoService.m** で、**defaultServiceWithDelegate** 内の **init** 呼び出しを **initWithDelegate** に変更します。

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4.  **QSTodoService.m** に戻り、デリゲートの場合、**nil** ではなく **syncDelegate** を渡すように **self.client.syncContext** の初期化を変更します。

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### 競合処理をサポートするための UI アラート ビュー ヘルパーの追加

1.  競合がある場合、ユーザーは、保持するバージョンを選択できます。

-   クライアント バージョンを保持する (サーバー上のバージョンを上書きする)
-   サーバー バージョンを保持する (クライアントのローカル テーブルを更新する)
-   いずれのバージョンも保持しない (プッシュを取り消し、操作を保留したままにする)

プロンプトの表示中に別の更新が実行されることがあるため、サーバーが障害応答の返送を停止するまで、オプションを表示したままにします。ここで使用するコードでは、アラート ビューを表示するヘルパー クラスを使用し、アラート ビューが表示されたときにデリゲートを呼び出します。最初に、ヘルパー クラス **QSUIAlertViewWithBlock** を定義します。

1.  Xcode を使用して、この新しいクラス **QSUIAlertViewWithBlock** を追加し、次のコンテンツで **QSUIAlertViewWithBlock.h** を上書きします。

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

2.  次に、**QSUIAlertViewWithBlock.m** を次のファイルで上書きします。

        #import "QSUIAlertViewWithBlock.h"
        #import <objc/runtime.h>

        @interface QSUIAlertViewWithBlock()

        @property (nonatomic, copy) QSUIAlertViewBlock callback;

        @end

        @implementation QSUIAlertViewWithBlock

        static const char *key;

        @synthesize callback = _callback;

        - (id) initWithCallback:(QSUIAlertViewBlock)callback
        {
            self = [super init];
            if (self) {
                _callback = [callback copy];
            }
            return self;
        }

        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title
                                                            message:message
                                                           delegate:self
                                                  cancelButtonTitle:cancelButtonTitle
                                                  otherButtonTitles:nil];

            if (otherButtonTitles) {
                for (NSString *buttonTitle in otherButtonTitles) {
                    [alert addButtonWithTitle:buttonTitle];
                }
            }

            [alert show];

            objc_setAssociatedObject(alert, &key, self, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
        }

        - (void) alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex
        {
            if (self.callback) {
                self.callback(buttonIndex);
            }
        }

        @end

### 競合ハンドラーの Todo リスト ビュー コントローラーへの追加

1.  **QSTodoListViewController.m** で、以下に示すように、**viewDidLoad** 内の **defaultService** への呼び出しを、**defaultServiceWithDelegate** への呼び出しで置換します。

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2.  **QSTodoListViewController.h** で、**\<MSSyncContextDelegate\>** をインターフェイス宣言に追加し、**MSSyncContextDelegate** プロトコルを実装します。

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate>

3.  **QSTodoListViewController.m** の最上部に次の import ステートメントを追加します。

        #import "QSUIAlertViewWithBlock.h"

4.  最後に、このヘルパー クラスを使用するために、次の 2 つの操作を **QSTodoListViewController.m** に追加し、3 とおりのいずれかの方法で競合を調整するようにユーザーに要求します。

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

### アプリケーションをテストする

競合するアプリケーションをテストします。稼働中のアプリケーションの 2 つの異なるインスタンス内の同じ項目を同時に編集します。上部からドラッグして、アプリ インスタンス内で更新ジェスチャを実行します。これで、変更を調整するためのプロンプトが表示されます。

### まとめ

「[オフライン データの使用][オフライン データの使用]」で完了したプロジェクトを競合を検出するように設定するために、最初に todo 項目を編集および更新する機能を追加しました。

そのために、新しい項目詳細ビュー コントローラーを追加し、メイン ビュー コントローラーと詳細ビュー コントローラーを接続して、最後に、編集内容を保存し、それらをクラウドにプッシュする機能を追加しました。

次に、競合がある場合に何が起こるかを検出しました。**MSSyncContextDelegate** プロトコルを実装することで、競合ハンドラーのサポートを追加しました。また、サーバー インターフェイス クラスの **QSTodoService**、**QSTodoListViewController**、およびサポートしているクラスを通じて、同期コンテキスト デリゲートのサポートを有効にしました。

ここまでで、ユーザーにアラートを表示するための **QSUIAlertViewWithBlock** ヘルパー クラスを追加し、最後に、3 とおりのいずれかで競合を調整するようにユーザーに要求するためのコードを **QSTodoListViewController** に追加しました。



  [オフライン データの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started-offline-data/
  [Azure の無料評価版サイト]: http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AE564AB28
  [0]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-1.png
  [1]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
  [2]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-1.png
  [3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-2.png
  [Adding a Scene to a Storyboard (シーンのストーリーボードへの追加)]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
  [Adding a Segue Between Scenes in a Storyboard (ストーリーボード内のシーン間へのセグエの追加)]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
  [Build a User Interface (ユーザー インターフェイスの作成)]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
  [Segmented Controls (セグメント化コントロール)]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
  [4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
  [Creating an Outlet Connection (アウトレット接続の作成)]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
  [5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
  [6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
  [7]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
  [8]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
