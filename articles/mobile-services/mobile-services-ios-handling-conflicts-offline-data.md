<properties 
	pageTitle="Mobile Services におけるオフライン データとの競合の処理 (iOS) | モバイル デベロッパー センター" 
	description="Azure Mobile Services を使用して、iOS アプリケーションのオフライン データの同期時に生じる競合を処理する方法を説明します。" 
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
	ms.date="04/16/2015" 
	ms.author="krisragh;donnam"/>


# モバイル サービスでのオフライン データの同期との競合の処理

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

このトピックでは、Azure Mobile Services のオフライン機能を使用しているときに、データを同期し、競合を処理する方法について説明します。このチュートリアルは、前の「[オフライン データの使用]」チュートリアルの手順およびサンプル アプリを基に作成されています。このチュートリアルを開始する前に、「[オフライン データの使用]」チュートリアルを完了している必要があります。

>[AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure の無料評価版サイト</a>」をご覧ください。

このチュートリアルでは、次の基本的な手順について説明します。

1. [アプリケーション プロジェクトを編集可能に更新]
2. [Todo リスト ビュー コントローラーの更新]
3. [Todo 項目ビュー コントローラーの追加]
4. [Todo 項目ビュー コントローラーとセグエのストーリーボードへの追加]
5. [項目の詳細の Todo 項目ビュー コントローラーへの追加]
6. [編集内容を保存するためのサポートの追加]
7. [競合処理の問題]
8. [競合処理をサポートするための QSTodoService の更新]
9. [競合処理をサポートするための UI アラート ビュー ヘルパーの追加]
10. [競合ハンドラーの Todo リスト ビュー コントローラーへの追加]
11. [アプリケーションをテストする]

## 「オフライン データの使用」チュートリアルの完了

「[オフライン データの使用]」チュートリアルの手順に従って、そのプロジェクトを完了します。そのチュートリアルで完了したプロジェクトを、このチュートリアルの起点として使用します。

## <a name="update-app"></a>アプリケーション プロジェクトを編集可能に更新

「[オフライン データの使用]」で完了したプロジェクトを更新し、項目を編集可能にします。現時点では、この同じアプリを 2 台の電話で稼働し、両方の電話で同じ項目をローカルに変更して、変更内容をサーバーにプッシュすると、競合が生じて失敗します。

SDK のオフライン同期機能を使用すると、コードを介してこのような競合を扱い、競合している項目への対処方法を動的に判断できます。クイックスタート プロジェクトを変更することで、この機能を体験できます。

### <a name="update-list-view"></a>Todo リスト ビュー コントローラーの更新

1. Xcode プロジェクト ナビゲーターで **MainStoryboard_iPhone.storyboard** を選択し、次に、**[Todo リスト ビュー コントローラー]** を選択します。テーブル ビュー セルを選択して、そのアクセサリ モードを**ディスクロージャー インジケーター**に設定します。ディスクロージャー インジケーターは、関連付けられたテーブル ビュー コントローラーをタップすると、新しいビューが表示されることをユーザーに示します。ディスクロージャー インジケーターは、イベントを生成しません。

      ![][update-todo-list-view-controller-2]

2. **TodoListViewController.m** で、次の操作とそのコンテンツを一緒に削除します。それらは必要ありません。

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### <a name="add-view-controller"></a>Todo 項目ビュー コントローラーの追加

1. UIViewController から派生した **QSItemViewController** という名前の新しい **Cocoa Touch** クラスを作成します。

2. **QSItemViewController.h** で、次のような型の定義を追加します。

        typedef void (^ItemEditCompletionBlock) (NSDictionary *editedItem);

3. **QSItemViewController.h** で、変更する項目を保持するプロパティと、ユーザーが詳細ビューで [戻る] ボタンを押した後に呼び出されるコールバックのプロパティを追加します。

        @property (nonatomic, weak) NSMutableDictionary *item;
        @property (nonatomic, strong) ItemEditCompletionBlock editCompleteBlock;

4. **QSItemViewController.m** で、編集する 2 つの Todo 項目のフィールド (Todo 項目自体の完了ステータスとテキスト) に対応した 2 つのプライベート プロパティを追加します。

        @interface QSItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

5. **QSItemViewController.m** で、**viewDidLoad** のスタブ実装を次のコードに更新します。

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

6. **QSItemViewController.m** で、編集したコントロールのイベントを処理するための 4 つのメソッドを追加します。

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

7. **QSItemViewController** で、次のメソッドも追加します。このメソッドは、ユーザーがナビゲーション バーで [**戻る**] ボタンを押したときに呼び出されます。このメソッドは、他のイベントで呼び出すこともできるため、親ビューを最初に確認します。項目に変更があった場合は、**self.item** が変更されてコールバック **editCompleteBlock** が呼び出されます。

        - (void)didMoveToParentViewController:(UIViewController *)parent
        {
            if (![parent isEqual:self.parentViewController]) {
                NSNumber *completeValue = [NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0];
                
                Boolean changed =
                    [self.item valueForKey:@"text"] != [self.itemText text] ||
                    [self.item valueForKey:@"complete"] != completeValue;
                
                if (changed) {
                    [self.item setValue:[self.itemText text] forKey:@"text"];
                    [self.item setValue:completeValue forKey:@"complete"];
                    
                    self.editCompleteBlock(self.item);
                }
            }
        }

### <a name="add-segue"></a>Todo 項目ビュー コントローラーとセグエのストーリーボードへの追加

1. プロジェクト ナビゲーターを使用して、**MainStoryboard_iPhone.storyboard** ファイルに戻ります。

2. Todo 項目の新しいビュー コントローラーをストーリーボードの既存の **Todo リスト ビュー コントローラー**の右に追加します。この新しいビュー コントローラーのカスタム クラスを **QSItemViewController** に設定します。詳細については、「[Adding a Scene to a Storyboard (シーンのストーリーボードへの追加)]」を参照してください。

3. **Show** セグエを **Todo リスト ビュー コントローラー**から **Todo 項目ビュー コントローラー**に追加します。次に、属性インスペクターでセグエの識別子を **detailSegue** に設定します。

    このセグエは、元のビュー コントローラーのセルまたはボタンからは作成しないでください。代わりに、ストーリーボード インターフェイスの **Todo リスト ビュー コントローラー**の上にあるビュー コントローラー アイコンを Ctrl キーを押しながら、**Todo 項目ビュー コントローラー**へドラッグします。

    ![][todo-list-view-controller-add-segue]

    間違ってセルからセグエを作成した場合は、アプリの実行時にセグエが 2 回トリガーされ、次のエラーが表示されます。

        Nested push animation can result in corrupted navigation bar

    セグエの詳細については、「[ストーリーボード内のシーン間へのセグエの追加]」を参照してください。

4. 項目テキスト用のテキスト フィールドと完了ステータス用のセグメント化コントロールを、ラベルと共に新しい **Todo 項目ビュー コントローラー**に追加します。セグメント化コントロールで、**セグメント 0** のタイトルを **Yes** に設定し、**セグメント 1** のタイトルを **No** に設定します。これらの新しいフィールドをコードのアウトレットに接続します。詳細については、「[Build a User Interface (ユーザー インターフェイスの作成)]」および「[Segmented Controls (セグメント化コントロール)]」を参照してください。

      ![][add-todo-item-view-controller-3]

5. これらの新しいフィールドを、既に **QSItemViewController.m** に追加されている対応するアウトレットに接続します。項目テキスト フィールドを **itemText** アウトレットに接続し、完了ステータス セグメント化コントロールを **itemComplete** アウトレットに接続します。詳細については、「[Creating an Outlet Connection (アウトレット接続の作成)]」を参照してください。

6. テキスト フィールドのデリゲートをビュー コントローラーに設定します。テキスト フィールドからストーリーボード インターフェイスの **Todo 項目ビュー コントローラー**の下にあるビュー コントローラー アイコンまで Ctrl キーを押しながらドラッグし、デリゲート アウトレットを選択します。この操作はストーリーボードに、このテキスト フィールドのデリゲートがこのビュー コントローラーであることを示します。

7. ここまでで加えたすべての変更を反映してアプリが動作することを確認します。シミュレーターでアプリを実行します。項目を todo リストに追加し、それらをクリックします。項目ビュー コントローラー (現在は空) が表示されます。

      ![][add-todo-item-view-controller-4] ![][add-todo-item-view-controller-5]

### <a name="add-item-details"></a>項目の詳細の Todo 項目ビュー コントローラーへの追加

1. **QSTodoListViewController.m** から **QSItemViewController** を参照します。そのため、**QSTodoListViewController.m** で、**QSItemViewController.h** をインポートするための行を追加します。

        #import "QSItemViewController.h"

2. **QSTodoListViewController.m** で、編集する項目を格納するための新しいプロパティを **QSTodoListViewController** インターフェイスに追加します。

        @property (strong, nonatomic)   NSDictionary *editingItem;

3. 編集した項目を保存してから、セグエを呼び出して詳細ビューを表示するために、**QSTodoListViewController.m** で **tableView:didSelectRowAtIndexPath:** を実装します。

        - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
            NSManagedObject *item = [self.fetchedResultsController objectAtIndexPath:indexPath];
            self.editingItem = [MSCoreDataStore tableItemFromManagedObject:item]; // map from managed object to dictionary
            
            [self performSegueWithIdentifier:@"detailSegue" sender:self];
        }

4. 項目を **Todo 項目ビュー コントローラー**に渡すために、**QSTodoListViewController.m** で **prepareForSegue:sender:** を実装し、ユーザーが詳細ビューを終了したときにコールバックを指定します。

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSItemViewController *ivc = (QSItemViewController *) [segue destinationViewController];
                ivc.item = [self.editingItem mutableCopy];
                
                ivc.editCompleteBlock = ^(NSDictionary *editedValue) {
                    [self.todoService updateItem:editedValue completion:^(NSUInteger index) {
                        self.editingItem = nil;
                    }];
                };
            }
        }

5. ここまでで加えたすべての変更を反映してアプリが動作することを確認します。シミュレーターでアプリを実行します。項目を todo リストに追加し、それらをクリックします。空でない項目ビュー コントローラーが表示され、todo 項目の詳細が示されます。

      ![][add-todo-item-view-controller-6]

### <a name="saving-edits"></a>編集内容を保存するためのサポートの追加

1. ナビゲーション ビューで [戻る] ボタンをクリックすると、編集内容は失われます。データは詳細ビューに送信しましたが、データはマスター ビューに送信されていません。既にポインターを項目のコピーに渡しているため、そのポインターを使用して、項目に加えられた更新の一覧を取得し、それを使用してサーバーを更新できます。開始するには、最初に、**QSTodoService.m** のサーバー ラッパー クラスの **QSTodoService** で、**completeItem** 操作を削除し、新しい **updateItem** 操作を追加して更新します。operation.これは、**completeItem** が完了として項目をマークするのみのため、代わりに、**updateItem** によって項目を更新します。

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion
        {
            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            
            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:mutable completion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                
                if (completion != nil) {
                    dispatch_async(dispatch_get_main_queue(), completion);
                }
            }];
        }

2. **QSTodoService.h** から **completeItem** の宣言を削除し、この **updateItem** の宣言を追加します。

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion;

3. ここで、アプリをテストします。ここまでで加えたすべての変更を反映してアプリが動作することを確認します。シミュレーターでアプリを実行します。項目を todo リストに追加し、それらをクリックします。項目の編集を試みて、元に戻ります。アプリのマスター ビューで項目の説明が更新されていることを確認します。下へのドラッグ ジェスチャでアプリを更新し、編集内容がリモート サービス内で反映されていることを確認します。

### <a name="conflict-handling-problem"></a>競合処理の問題

1. 2 台の異なるクライアントが、同時にデータの同じ部分の変更を試みると何が起きるかを調べます。次の例の一覧では、「Mobile Services はかっこいい」という項目があります。 たとえば、この項目を 1 つのデバイスでは「Mobile Services が大好きです」、別のデバイスでは「Azure が大好きです」に変更してみましょう。

      ![][conflict-handling-problem-1]

2. 2 台の iOS デバイス、またはシミュレーターと 1 台の iOS デバイスの 2 か所でアプリを起動します。テスト用の物理デバイスがない場合は、シミュレーター内で 1 つのインスタンスを起動し、REST クライアントを使用して、PATCH 要求を Mobile Service へ送信します。PATCH 要求の URL には、Mobile Service の名前、todo 項目テーブルの名前、および編集中の todo 項目テーブルの ID が反映され、x-zumo-application ヘッダーはアプリケーション キーとなります。

        PATCH https://donnam-tutorials.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: xuAdWVDcLuCNfkTvOfaqzCCSBVHqoy96

        {
            "id": "CBBF4464-E08A-47C9-B6FB-6DCB30ACCE7E",
            "text": "I love Azure!"
        }

3. ここで、アプリの 2 つのインスタンス内の項目を更新します。Xcode コードの出力ログにエラーが表示されます。

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

  これは、**pullWithQuery:completion:** を呼び出したときの完了ブロックに起因しており、エラー パラメーターは nil ではないため、**NSLog** を介した出力にエラーが返されました。

### <a name="service-add-conflict-handling"></a>競合処理をサポートするための QSTodoService の更新

1. クライアント内で競合を扱うことで、競合に対処する方法を決定します。これを実行するため、**MSSyncContextDelegate** プロトコルを実装します。**QSTodoService.h** と **QSTodoService.m** の両方で、**(QSTodoService *)defaultService;** 工場出荷時のメソッド宣言を以下のステートメントに変更します。これによって、パラメーターとして同期コンテキスト デリゲートを受信できます。

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2. **QSTodoService.m** で、以下に示すように、**init** 行を変更します。これも、パラメーターとして同期コンテキスト デリゲートを受信します。

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3. **QSTodoService.m** で、**defaultServiceWithDelegate** 内の **init** 呼び出しを **initWithDelegate** に変更します。

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4. **QSTodoService.m** に戻り、デリゲートの場合、**nil** ではなく **syncDelegate** を渡すように **self.client.syncContext** の初期化を変更します。

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### <a name="add-alert-view"></a>競合処理をサポートするための UI アラート ビュー ヘルパーの追加

1. 競合がある場合、ユーザーは、保持するバージョンを選択できます。
  * クライアント バージョンを保持する (サーバー上のバージョンを上書きする)
  * サーバー バージョンを保持する (クライアントのローカル テーブルを更新する)
  * いずれのバージョンも保持しない (プッシュを取り消し、操作を保留したままにする)

  プロンプトの表示中に別の更新が実行されることがあるため、サーバーが障害応答の返送を停止するまで、オプションを表示したままにします。ここで使用するコードでは、アラート ビューを表示するヘルパー クラスを使用し、アラート ビューが表示されたときにデリゲートを呼び出します。最初に、ヘルパー クラス **QSUIAlertViewWithBlock** を定義します。

2. Xcode を使用して、この新しいクラス **QSUIAlertViewWithBlock** を追加し、次のコンテンツで **QSUIAlertViewWithBlock.h** を上書きします。

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

3. 次に、**QSUIAlertViewWithBlock.m** を次のファイルで上書きします。

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

### <a name="add-conflict-handling"></a>競合ハンドラーの Todo リスト ビュー コントローラーへの追加

1. **QSTodoListViewController.m** で、**viewDidLoad** を編集します。**defaultService** への呼び出しを、**defaultServiceWithDelegate** への呼び出しに置き換えます。

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. **QSTodoListViewController.h** で、**&lt;MSSyncContextDelegate&gt;** をインターフェイス宣言に追加し、**MSSyncContextDelegate** プロトコルを実装します。

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. **QSTodoListViewController.m** の最上部に次の import ステートメントを追加します。

        #import "QSUIAlertViewWithBlock.h"

4. 最後に、このヘルパー クラスを使用するために、次の 2 つの操作を **QSTodoListViewController.m** に追加し、3 とおりのいずれかの方法で競合を調整するようにユーザーに要求します。

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

### <a name="test-app"></a>アプリケーションをテストする

競合するアプリケーションをテストします。 稼働中のアプリケーションの 2 つの異なるインスタンス内の同じ項目を同時に編集するか、またはアプリケーションと REST クライアントを使用します。

上部からドラッグして、アプリケーション インスタンス内で更新ジェスチャを実行します。これで、競合を調整するためのプロンプトが表示されます。

![][conflict-ui]


### 概要

「[オフライン データの使用]」で完了したプロジェクトを競合を検出するように設定するために、最初に todo 項目を編集および更新する機能を追加しました。

そのために、新しい項目詳細ビュー コントローラーを追加し、メイン ビュー コントローラーと詳細ビュー コントローラーを接続して、最後に、編集内容を保存し、それらをクラウドにプッシュする機能を追加しました。

次に、競合がある場合に何が起こるかを検出しました。**MSSyncContextDelegate** プロトコルを実装することで、競合ハンドラーのサポートを追加しました。また、サーバー インターフェイス クラスの **QSTodoService**、**QSTodoListViewController**、およびサポートしているクラスを通じて、同期コンテキスト デリゲートのサポートを有効にしました。

ここまでで、ユーザーにアラートを表示するための **QSUIAlertViewWithBlock** ヘルパー クラスを追加し、最後に、3 とおりのいずれかで競合を調整するようにユーザーに要求するためのコードを **QSTodoListViewController** に追加しました。

<!-- URLs. -->

[アプリケーション プロジェクトを編集可能に更新]: #update-app
[Todo リスト ビュー コントローラーの更新]: #update-list-view
[Todo 項目ビュー コントローラーの追加]: #add-view-controller
[Todo 項目ビュー コントローラーとセグエのストーリーボードへの追加]: #add-segue
[項目の詳細の Todo 項目ビュー コントローラーへの追加]: #add-item-details
[編集内容を保存するためのサポートの追加]: #saving-edits
[競合処理の問題]: #conflict-handling-problem
[競合処理をサポートするための QSTodoService の更新]: #service-add-conflict-handling
[競合処理をサポートするための UI アラート ビュー ヘルパーの追加]: #add-alert-view
[競合ハンドラーの Todo リスト ビュー コントローラーへの追加]: #add-conflict-handling
[アプリケーションをテストする]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Segmented Controls (セグメント化コントロール)]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection (アウトレット接続の作成)]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface (ユーザー インターフェイスの作成)]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[ストーリーボード内のシーン間へのセグエの追加]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard (シーンのストーリーボードへの追加)]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[オフライン データの使用]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md

<!--HONumber=54--> 