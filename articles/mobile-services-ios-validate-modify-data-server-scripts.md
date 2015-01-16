<properties urlDisplayName="Validate Data" pageTitle="サーバー スクリプトを使用したデータの検証および変更 (iOS) | モバイル デベロッパー センター" metaKeywords="" description="iOS アプリからサーバー スクリプトを使用して送信されたデータを検証および変更する方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# サーバー スクリプトを使用したモバイル サービスのデータの検証および変更

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

このトピックでは、Azure のモバイル サービスでサーバー スクリプトを活用する方法について説明します。サーバー スクリプトは、モバイル サービスに登録され、挿入や更新が行われるデータでの広範な操作 (検証やデータの修正を含む) の実行に使用できます。このチュートリアルでは、検証およびデータの修正を行うサーバー スクリプトを定義して登録します。多くの場合、サーバー側スクリプトの動作がクライアントに影響を与えるため、これらの新しい動作の利点を活用できるように iOS アプリケーションも更新します。

このチュートリアルでは、次の基本的な手順について説明します。

1. [文字列の長さの検証の追加]
2. [検証をサポートするためのクライアントの更新]


このチュートリアルは、前の[データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、[データの使用]に関するチュートリアルを完了している必要があります。  

## <a name="string-length-validation"></a>検証の追加

ユーザーにより送信されたデータの長さを検証することをお勧めします。最初に、モバイル サービスに送信された文字列データの長さを検証するスクリプトを登録し、長すぎる文字列 (この場合は 10 文字を超える) を拒否します。

1. [Azure の管理ポータル]にログインし、**[Mobile Services]** をクリックして、アプリケーションをクリックします。

   	![][0]

2. **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   	![][1]

3.  **[スクリプト]** をクリックし、**[挿入]** 操作を選択します。

   	![][2]

4. 既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

        このスクリプトは、**text** プロパティの長さを調べ、長さが 10 文字を超えた場合にエラー応答を送信します。それ以外の場合、**execute** メソッドが呼び出されて挿入を完了します。

    <div class="dev-callout">
	<b>注</b>
	<p>登録したスクリプトを <strong>[スクリプト]</strong> タブで削除できます。<strong>[クリア]</strong> をクリックし、<strong>[保存]</strong> をクリックします。</p></div>

## <a name="update-client-validation"></a>クライアントの更新

モバイル サービスはデータを検証してエラー応答を送信するため、検証からのエラー応答を処理できるようにアプリケーションを更新する必要があります。

1. Xcode で、[データの使用]に関するチュートリアルを実行したときに変更したプロジェクトを開きます。

2. **[実行]** (Command + R キー) を押してプロジェクトをビルドし、アプリケーションを開始します。次に、テキスト ボックスに 11 文字以上のテキストを入力し、プラス (**[+]**) アイコンをクリックします。

   	モバイル サービスから返された応答 400 (正しくない要求) の結果として、処理されないエラーが発生します。

3. QSTodoService.m ファイルの **addItem** メソッドで、次のコード行を見つけます。

        [self logErrorIfNotNil:error];

   	このコード行の後ろで、完了ブロックの残りの部分を次のコードに置き換えます。

        BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

        // detect text validation error from service.
        if (goodRequest) // The service responded appropriately
        {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:result atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }
        else{

            // if there's an error that came from the service
            // log it, and popup up the returned string.
            if (error && error.code == MSErrorMessageErrorCode) {
                NSLog(@"ERROR %@", error);
                UIAlertView *av =
                [[UIAlertView alloc]
                 initWithTitle:@"Request Failed"
                 message:error.localizedDescription
                 delegate:nil
                 cancelButtonTitle:@"OK"
                 otherButtonTitles:nil
                 ];
                [av show];
            }
        }

   	これにより、出力ウィンドウにエラーが記録され、ユーザーに表示されます。

4. アプリケーションをリビルドして開始します。

   	![][4]

  	エラーが処理され、エラー メッセージがユーザーに表示されることに注目してください。

## <a name="add-timestamp"></a>タイムスタンプの追加

前のタスクでは、挿入を検証して、受け入れるか拒否しました。ここでは、オブジェクトへの挿入前にタイムスタンプ プロパティをそのオブジェクトに追加するサーバー スクリプトを使用して、挿入されたデータを更新します。

1. [管理ポータル]の **[スクリプト]** タブで、現在の **[挿入]** スクリプトを次の関数で置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    この関数は、前の挿入スクリプトを強化するものです。新しい **createdAt** タイムスタンプ プロパティを、**request**.**execute** の呼び出しによりオブジェクトの挿入前にそのオブジェクトに追加します。

    <div class="dev-callout"><b>注</b>
	<p>挿入スクリプトを初めて実行するときには、動的スキーマを必ず有効にしてください。動的スキーマが有効になっていると、挿入スクリプトを最初に実行した時点でモバイル サービスによって<strong>createdAt</strong> テーブルに <strong>TodoItem</strong> 列が自動で追加されます。. 動的スキーマは、新しいモバイル サービスでは既定で有効になっているため、アプリケーションの公開前に無効にする必要があります。</p>
    </div>

2. Visual Studio で、F5 キーを押してアプリケーションを実行し、[Insert a TodoItem] に 10 文字未満のテキストを入力し、[Save] をクリックします。

   	新しいタイムスタンプがアプリケーション UI に表示されないことを確認します。

3. 管理ポータルに戻り、todoitem テーブルの [参照] タブをクリックします。

   	**createdAt** 列が存在し、新しく挿入された項目にタイムスタンプ値があることがわかります。

次に、この新しい列を表示するように iOS アプリケーションを更新する必要があります。

## <a name="update-client-timestamp"></a>クライアントの再更新

モバイル サービス クライアントは、定義された型のプロパティにシリアル化できない応答内のデータを無視します。最後の手順は、クライアントを更新してこの新しいデータを表示することです。

1. Visual Studio で、MainPage.xaml.cs ファイルを開き、既存の TodoItem クラスを次の定義に置き換えます。

	    public class TodoItem
	    {
	        public int Id { get; set; }

            [DataMember(Name="text")]
	        public string Text { get; set; }

            [DataMember(Name="complete")]
	        public bool Complete { get; set; }

            [DataMember(Name="createdAt")]
	        public DateTime? CreatedAt { get; set; }
	    }

    新しいクラス定義には、null 値を許容する DateTime 型として新しいタイムスタンプ プロパティが含まれます。

    <div class="dev-callout"><b>注</b>
	<p><strong>DataMemberAttribute</strong> は、アプリケーション内の新しい <strong>CreatedAt</strong> プロパティを、TodoItem テーブルに定義された (大文字と小文字が異なる) <strong>createdAt</strong> 列にマップするように、クライアントに指示します。この属性を使用することにより、アプリケーションでは、SQL データベース内の列名と異なるプロパティ名をオブジェクトに対して使用することができます。この属性がなければ、大文字と小文字が異なるため、エラーが発生します。</p>
    </div>

2. MainPage.xaml ファイル内の [CheckBoxComplete] 要素のすぐ下に、次の XAML 要素を追加します。

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	これにより、新しい CreatedAt プロパティがテキスト ボックスに表示されます。

3. F5 キーを押してアプリケーションを実行します。

   挿入スクリプトを更新した後で、タイムスタンプが挿入された項目にのみ表示されることに注目してください。

4. 既存の **RefreshTodoItems** メソッドを次のコードに置き換えます。

        private void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and
            // items without a timestamp.
            items = todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionView();

            ListItems.ItemsSource = items;
        }

   	このメソッドは、クエリを更新し、タイムスタンプ値を保持しない項目をフィルターで除きます。

5. F5 キーを押してアプリケーションを実行します。

   	タイムスタンプ値なしで作成されたすべての項目が UI から消去されていることに注目してください。

これで、データの使用に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルが完了したため、データ シリーズの最終チュートリアルに進むことを検討してください。[ページングを使用したクエリの改善]。

サーバー スクリプトは、ユーザーを認証するときに、およびプッシュ通知の送信のためにも使用されます。詳細については、次のチュートリアルを参照してください。

* [スクリプトを使用したユーザーの承認]
  <br/>認証されたユーザーの ID に基づきデータをフィルター処理する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録および使用について説明します。

<!-- Anchors. -->
[文字列の長さの検証の追加]: #string-length-validation
[検証をサポートするためのクライアントの更新]: #update-client-validation
[挿入時のタイムスタンプの追加]: #add-timestamp
[タイムスタンプを表示するためのクライアントの更新]: #update-client-timestamp
[次のステップ]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-ios
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-ios
[ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-ios
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-ios
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios

[管理ポータル]: https://manage.windowsazure.com/
[Azure 管理ポータル]: https://manage.windowsazure.com/
