<properties linkid="develop-mobile-tutorials-build-realtime-apps-with-pusher-ios" urlDisplayName="Build Realtime Apps with Pusher" pageTitle="Build Realtime Apps with Pusher (iOS) - Mobile Services" metaKeywords="" description="Learn how to use Pusher to send notifications to your Azure Media Services app on iOS." metaCanonical="" services="" documentationCenter="Mobile" title="Build Real-time Apps with Mobile Services and Pusher" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# モバイル サービスおよび Pusher を使用したリアルタイム アプリケーションの構築

<div class="dev-center-tutorial-selector sublanding"> 
<a href="" title="iOS" class="current">iOS</a> 
</div>

このトピックでは、Azure モバイル サービス ベースのアプリケーションにリアルタイム機能を追加する方法を示します。完了すると、アプリケーションのすべての実行中のインスタンスにわたって、TodoList データがリアルタイムで同期されます。

[ユーザーへのプッシュ通知][ユーザーへのプッシュ通知]に関するチュートリアルでは、プッシュ通知を使用して Todo リスト内の新しい項目をユーザーに知らせる方法を示しています。プッシュ通知は変更を随時知らせるための優れた方法です。しかし、アプリケーションには頻繁にリアルタイム通知が必要になることもあります。リアルタイム通知は、Pusher API を使用してモバイル サービスに追加できます。このチュートリアルでは、モバイル サービスと共に Pusher を使用して、アプリケーションの実行中のインスタンスで変更が行われたときに Todo リストを同期された状態に保ちます。

Pusher はモバイル サービスのようにクラウド ベースのサービスです。Pusher によってリアルタイム アプリケーションの構築が非常に簡単になります。Pusher を使用すると、ライブ投票、チャット ルーム、マルチプレーヤー ゲーム、コラボレーション アプリケーションをすばやく構築して、ライブ データとライブ コンテンツをブロードキャストすることができます。用途はまだまだあります。詳細については、[][]<http://pusher.com></a> を参照してください。

このチュートリアルでは、Todo リスト アプリケーションにリアルタイム コラボレーションを追加するための基本的な手順について説明します。

1.  [Pusher アカウントを作成する][Pusher アカウントを作成する]
2.  [アプリケーションを更新する][アプリケーションを更新する]
3.  [サーバー スクリプトをインストールする][サーバー スクリプトをインストールする]
4.  [アプリケーションをテストする][アプリケーションをテストする]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

## <a name="sign-up"></a>新しい Pusher アカウントを作成する

[WACOM.INCLUDE [pusher-sign-up][pusher-sign-up]]

## <a name="update-app"></a>アプリケーションを更新する

Pusher アカウントの設定が終わったので、次に iOS アプリケーション コードを新しい機能向けに変更します。

### libPusher ライブラリをインストールする

[libPusher][libPusher] ライブラリを使用すると、iOS から Pusher にアクセスできます。

1.  libPusher ライブラリを[ここから][ここから]ダウンロードします。

2.  プロジェクト内に *libPusher* という名前のグループを作成します。

3.  Finder で、ダウンロードした zip ファイルを解凍し、**libPusher-combined.a** および **/headers** フォルダーを選択して、これらの項目をプロジェクト内の **libPusher** グループにドラッグします。

4.  **[Copy items into destination group's folder]** をオンにして、**[完了]** をクリックします。

    ![][]

    これで、libPusher ファイルがプロジェクトにコピーされます。

1.  プロジェクト エクスプローラーのプロジェクト ルートで、**[Build Phases]**、**[Add Build Phase]**、**[Add Copy Files]** の順にクリックします。

2.  **libPusher-combined.a** ファイルを、プロジェクト エクスプローラーから新しいビルド フェーズにドラッグします。

3.  **[Destination]** を **[Frameworks]** に変更し、**[Copy only when installing]** をクリックします。

    ![][1]

4.  **[Link Binary With Libraries]** 領域内で、次のライブラリを追加します。

    -   libicucore.dylib
    -   CFNetwork.framework
    -   Security.framework
    -   SystemConfiguration.framework

5.  最後に **[ビルド設定]** 内で、ターゲットのビルド設定 **[Other Linker Flags]** を見つけ、**-all\_load** フラグを追加します。

    ![][2]

    これで、[デバッグ] ビルド ターゲット向けに設定された **-all\_load** フラグが示されます。

ライブラリはインストールされ、使用できる状態になりました。

### アプリケーションにコードを追加する

1.  Xcode で、**QSTodoService.h** ファイルを開き、次のメソッド宣言を追加します。

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item;

        // To be called when items are added by other users
        - (NSUInteger) itemAdded:(NSDictionary *)item;

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item;

2.  **addItem** および **completeItem** の既存の宣言を次の宣言で置き換えます。

        - (void) addItem:(NSDictionary *) item;
        - (void) completeItem: (NSDictionary *) item;

3.  **QSTodoService.m** で、次のコードを追加して新しいメソッドを実装します。

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item
        {
            NSInteger itemId = [[item objectForKey: @"id"] integerValue];

            return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                 {
                     return ([[currItem objectForKey: @"id"] integerValue] == itemId);
                 }];
        }

        // To be called when items are added by other users
        -(NSUInteger) itemAdded:(NSDictionary *)item
        {
            NSUInteger index = [self getItemIndex:item];

            // Only complete action if item not already in list
            if(index == NSNotFound)
            {
                NSUInteger newIndex = [items count];
                [(NSMutableArray *)items insertObject:item atIndex:newIndex];
                return newIndex;
            }
            else
                return -1;
        }

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item
        {
            NSUInteger index = [self getItemIndex:item];

            // Only complete action if item exists in items list
            if(index != NSNotFound)
            {
                NSMutableArray *mutableItems = (NSMutableArray *) items;
                [mutableItems removeObjectAtIndex:index];
            }       
            return index;
        }

    QSTodoService を使用すると、**id** で項目を検索し、リモート サービスに明示的な要求を送信せずにローカルに項目を追加して完了させることができます。

4.  既存の **addItem** および **completeItem** メソッドを次のコードで置き換えます。

        -(void) addItem:(NSDictionary *)item
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
                [self logErrorIfNotNil:error];
            }];
        }

        -(void) completeItem:(NSDictionary *)item
        {
            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@(YES) forKey:@"complete"];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
                [self logErrorIfNotNil:error];
            }];
        }

    データ テーブルが更新されたときではなく、Pusher からイベントを受け取ったときに、UI が更新されるとともに、項目が追加され完了に設定されることに注意してください。

5.  **QSTodoListViewController.h** ファイルに、次の import ステートメントを追加します。

        #import "PTPusherDelegate.h"
        #import "PTPusher.h"
        #import "PTPusherEvent.h"
        #import "PTPusherChannel.h"

6.  次のように、インターフェイス宣言を変更して **PTPusherDelegate** を追加します。

        @interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7.  次の新しいプロパティを追加します。

        @property (nonatomic, strong) PTPusher *pusher;

8.  次のコードを追加して新しいメソッドを宣言します。

        // Sets up the Pusher client
        - (void) setupPusher;

9.  **QSTodoListViewController.m** で、**@synthesise** 行の下に次の行を追加して新しいプロパティを実装します。

        @synthesize pusher = _pusher;

10. 次のコードを追加して、新しいメソッドを実装します。

        // Sets up the Pusher client
        - (void) setupPusher {

            // Create a Pusher client, using your Pusher app key as the credential
            // TODO: Move Pusher app key to configuration file
            self.pusher = [PTPusher pusherWithKey:@"**your_app_key**" delegate:self encrypted:NO];
            self.pusher.reconnectAutomatically = YES;

            // Subscribe to the 'todo-updates' channel
            PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];

            // Bind to the 'todo-added' event
            [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {

                // Add item to the todo list
                NSUInteger index = [self.todoService itemAdded:channelEvent.data];

                // If the item was not already in the list, add the item to the UI
                if(index != -1)
                {
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
                    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
                }
            }];

            // Bind to the 'todo-completed' event
            [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {

                // Update the item to be completed
                NSUInteger index = [self.todoService itemCompleted:channelEvent.data];

                // As long as the item did exit in the list, update the UI
                if(index != NSNotFound)
                {
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
                    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
                }               
            }];
        }

11. プレースホルダー `**your_app_key**` を、前に [出力値] ダイアログからコピーした app\_key の値で置き換えます。

12. **onAdd** メソッドを次のコードに置き換えます。

        - (IBAction)onAdd:(id)sender
        {
            if (itemText.text.length  == 0) {
                return;
            }

            NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };
            [self.todoService addItem:item];

            itemText.text = @"";
        }

13. **QSTodoListViewController.m** ファイルで、(void)viewDidLoad メソッドを見つけ、次のように **setupPusher** メソッドへの呼び出しを追加します。

        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self setupPusher];

14. **tableView:commitEditingStyle:forRowAtIndexPath** メソッドの末尾で、**completeItem** への呼び出しを次のコードに置き換えます。

        // Ask the todoService to set the item's complete value to YES
        [self.todoService completeItem:item];

これで、アプリケーションは Pusher からイベントを受け取り、それに応じてローカルな Todo リストを更新できるようになりました。

## <a name="install-scripts"></a>サーバー スクリプトをインストールする

残りの作業はサーバー スクリプトの設定です。TodoList テーブルに項目が挿入された場合や、項目が更新された場合に対応するスクリプトを挿入します。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

2.  管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][3]

3.  **[TodoItem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。

    ![][4]

    **TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

4.  挿入関数を次のコードに置き換えます。

        var Pusher = require('pusher');

        function insert(item, user, request) {   

            request.execute({
                success: function() {
                    // After the record has been inserted, trigger immediately to the client
                    request.respond();

                    // Publish event for all other active clients
                    publishItemCreatedEvent(item);
                }
            });

            function publishItemCreatedEvent(item) {

                // Ideally these settings would be taken from config
                var pusher = new Pusher({
                  appId: '**your_app_id**',
                  key: '**your_app_key**',
                  secret: '**your_app_secret**'
                });     

                // Publish event on Pusher channel
                pusher.trigger( 'todo-updates', 'todo-added', item );   
            }
        }

5.  上記スクリプト内のプレースホルダーを、前に [出力値] ダイアログからコピーした値に置き換えます。

    -   **`**your_app_id**`**: app\_id の値
    -   **`**your_app_key**`**: app\_key の値
    -   **`**your_app_key_secret**`**: app\_key\_secret

6.  **[保存]** ボタンをクリックします。これで、新しい項目が **TodoItem** テーブルに挿入されるたびに Pusher にイベントを発行するスクリプトが構成されました。

7.  **[操作]** ドロップダウンから **[更新]** を選択します。

8.  update 関数を次のコードに置き換えます。

        var Pusher = require('pusher');

        function update(item, user, request) {   

            request.execute({
                success: function() {
                    // After the record has been updated, trigger immediately to the client
                    request.respond();

                    // Publish event for all other active clients
                    publishItemUpdatedEvent(item);
                }
            });

            function publishItemUpdatedEvent(item) {

                // Ideally these settings would be taken from config
                var pusher = new Pusher({
                  appId: '**your_app_id**',
                  key: '**your_app_key**',
                  secret: '**your_app_secret**'
                });     

                // Publish event on Pusher channel
                pusher.trigger( 'todo-updates', 'todo-completed', item );

            }
        }

9.  このスクリプトにも手順 5 を繰り返して、プレースホルダーを置き換えます。

10. **[保存]** ボタンをクリックします。これで、新しい項目が更新されるたびに Pusher にイベントを発行するスクリプトが構成されました。

## <a name="test-app"></a>アプリケーションをテストする

アプリケーションをテストするには、2 つのインスタンスを実行する必要があります。1 つのインスタンスを iOS デバイスから実行し、もう 1 つを iOS シミュレーターで実行します。

1.  iOS デバイスを接続し、**[Run]** ボタン (または Command + R キー) を押してデバイスでアプリケーションを起動してから、デバッグを停止します。

    これで、デバイスにアプリケーションがインストールされました。

2.  iOS シミュレーターでアプリケーションを実行し、同時に iOS デバイスでアプリケーションを起動します。

    アプリケーションの 2 つのインスタンスが実行されています。

3.  1 つのアプリケーション インスタンスで新しい Todo 項目を追加します。

    追加した項目がもう 1 つのインスタンスに表示されることを確認します。

4.  1 つのアプリケーション インスタンスで Todo 項目をチェックして、完了としてマークします。

    もう 1 つのインスタンスから項目が消えたことを確認します。

これで、モバイル サービス アプリケーションがすべてのクライアントにわたってリアルタイムで同期されるように構成されました。

## <a name="nextsteps"> </a> 次のステップ

モバイル サービスで Pusher サービスを簡単に使用できることがわかりました。Pusher の詳細については、次のリンク先を参照してください。

-   Pusher API ドキュメント: <http://pusher.com/docs>
-   Pusher チュートリアル: <http://pusher.com/tutorials>

サーバー スクリプトの登録および使用の詳細については、「[モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]」を参照してください。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [iOS]:  "iOS"
  [ユーザーへのプッシュ通知]: /ja-jp/develop/mobile/tutorials/push-notifications-to-users-ios
  []: http://pusher.com
  [Pusher アカウントを作成する]: #sign-up
  [アプリケーションを更新する]: #update-app
  [サーバー スクリプトをインストールする]: #install-scripts
  [アプリケーションをテストする]: #test-app
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
  [pusher-sign-up]: ../includes/pusher-sign-up.md
  [libPusher]: http://go.microsoft.com/fwlink/p?LinkId=276999
  [ここから]: http://go.microsoft.com/fwlink/p/?LinkId=276998
  []: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png
  [1]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png
  [2]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png
  [4]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p/?LinkId=262293
