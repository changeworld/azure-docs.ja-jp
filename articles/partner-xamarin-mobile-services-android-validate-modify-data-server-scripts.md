<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-xamarin-android" urlDisplayName="データの検証" pageTitle="サーバー スクリプトを使用したデータの検証および変更 (Xamarin Android) | モバイル デベロッパー センター" metaKeywords="データのアクセスと変更, Azure モバイル サービス, モバイル デバイス, Azure, モバイル, Xamarin.Android" description="Xamarin Android アプリからサーバー スクリプトを使用して、送信されたデータを検証および変更する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="サーバー スクリプトを使用したモバイル サービスのデータの検証および変更" authors="" />

# サーバー スクリプトを使用したモバイル サービスのデータの検証および変更
<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>


このトピックでは、Azure のモバイル サービスでサーバー スクリプトを活用する方法について説明します。サーバー スクリプトは、モバイル サービスに登録され、挿入や更新が行われるデータでの広範な操作 (検証やデータの修正を含む) の実行に使用できます。このチュートリアルでは、検証およびデータの修正を行うサーバー スクリプトを定義して登録します。多くの場合、サーバー側スクリプトの動作がクライアントに影響を与えるため、これらの新しい動作の利点を活用できるように Android アプリも更新します。完成したコードは、[ValidateModifyData アプリケーション][GitHub] サンプルで参照できます。

このチュートリアルでは、次の基本的な手順について説明します。

1. [文字列の長さの検証の追加]
2. [検証をサポートするためのクライアントの更新]
3. [挿入時のタイムスタンプの追加]
4. [タイムスタンプを表示するためのクライアントの更新]

このチュートリアルは、前の[データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、[データの使用]に関するチュートリアルを完了している必要があります。

## <a name="string-length-validation"></a>検証の追加

ユーザーにより送信されたデータの長さを検証することをお勧めします。最初に、モバイル サービスに送信された文字列データの長さを検証するスクリプトを登録し、長すぎる文字列 (この場合は 10 文字を超える) を拒否します。

1. [Azure 管理ポータル]にログインし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

	![][0]

2. **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

	![][1]

3. **[スクリプト]** をクリックし、**[挿入]** 操作を選択します。

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

モバイル サービスはデータを検証してエラー応答を送信するため、アプリケーションが検証からのエラー応答を適切に処理していることを確認する必要があります。

1. Xamarin Studio で、「[モバイル サービスでのデータの使用]」チュートリアルを実行したときに作成したプロジェクトを開きます。

2. TodoActivity.cs ファイルで、**AddItem** メソッドを探し、CreateAndShowDialog メソッドの呼び出しを次のコードで置き換えます。

    	var exDetail = ex.InnerException.InnerException as 	
			MobileServiceInvalidOperationException;
    	CreateAndShowDialog(exDetail.Message, "Error");

	これにより、モバイル サービスから返されたエラー メッセージが表示されます。

3. **[Run]** をクリックして、アプリケーションを開始します。次に、テキスト ボックスに 10 文字より長いテキストを入力し、**[Add]** をクリックします。

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

    この関数は、**request**.**execute** の呼び出しで、オブジェクトへの挿入前に、新しい **createdAt** タイムスタンプ プロパティをそのオブジェクトに追加することにより、前の挿入スクリプトを強化しています。

    <div class="dev-callout"><b>注</b>
	<p>挿入スクリプトを初めて実行するときには、動的スキーマを必ず有効にしてください。動的スキーマが有効になっていると、挿入スクリプトを最初に実行した時点でモバイル サービスによって <strong>TodoItem</strong> テーブルに <strong>createdAt</strong> 列が自動で追加されます。動的スキーマは、新しいモバイル サービスでは既定で有効になっているため、アプリケーションの公開前に無効にする必要があります。</p>
    </div>

2. **[Run]** メニューの **[Run]** をクリックして、アプリケーションを開始します。次に、テキスト ボックスに 10 文字未満のテキストを入力し、**[Add]** をクリックします。

   	新しいタイムスタンプがアプリケーション UI に表示されないことを確認します。

3. 管理ポータルに戻り、**todoitem** テーブルの **[参照]** タブをクリックします。
   
   	**[createdAt]** 列が存在し、新しく挿入された項目にタイムスタンプ値が保持されていることを確認します。
  
次に、Android アプリを更新してこの新しい列を表示する必要があります。

## <a name="update-client-timestamp"></a>クライアントの再更新

モバイル サービス クライアントは、定義された型のプロパティにシリアル化できない応答内のデータを無視します。最後の手順は、クライアントを更新してこの新しいデータを表示することです。

1. **TodoItem** クラス内のプライベート フィールド定義に、次のコードを追加します。

        [DataMember(Name = "createdAt")]
        public DateTime? CreatedAt { get; set; }
  
    <div class="dev-callout"><b>注</b>
	<p><code>DataMember's Name</code> 注釈は、アプリケーション内の新しい <code>CreatedAt</code> プロパティを、別の名前を持つ TodoItem テーブルに定義された [<code>createdAt</code>] 列にマップするように、クライアントに指示しますこの注釈を使用することにより、使用しているアプリケーションで、SQL データベース内の列名と異なるオブジェクトにプロパティ名を持つことができます。この注釈がない場合は、大文字と小文字が区別されるため、エラーが発生します。</p>
    </div>

2. GetView メソッドで、<code>checkBox.Text</code> を <code>currentItem.Text</code> に設定する現在のコードの直前に、次のコードを追加します。

       	string displayDate = "missing";
       	if (currentItem.CreatedAt.HasValue)
       		displayDate = currentItem.CreatedAt.Value.ToShortTimeString();

   	これにより、タイムスタンプ値が存在する場合は、フォーマットされた日付文字列が作成されます。

3. コードの `checkBox.Text = currentItem.Text` を探し、このコード行を次のコードで置き換えます。

		checkBox.Text = string.Format("{0} - {1}", currentItem.Text, displayDate);

	これにより、タイムスタンプ日付が表示用に項目に追加されます。
	
4. **[Run]** メニューの **[Run]** をクリックして、アプリケーションを開始します。

	挿入スクリプトを更新した後で、タイムスタンプが挿入された項目にのみ表示されることに注目してください。

5. **TodoActivity.cs** で、**RefreshItemsFromTableAsync** の既存のクエリを次のクエリに置き換えます。

		var list = await todoTable.Where(item => item.Complete == false && 
										 item.CreatedAt != null)
								  .ToListAsync();

	このメソッドは、クエリを更新し、タイムスタンプ値を保持しない項目をフィルターで除きます。
	
6. **[Run]** メニューの **[Run]** をクリックして、アプリケーションを開始します。

  	タイムスタンプ値なしで作成されたすべての項目が UI から消去されていることに注目してください。

これで、データの使用に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルが完了したため、データ シリーズの最終チュートリアル「[ページングを使用したモバイル サービス クエリの改善]」に進むことを検討してください。

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
[0]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png



<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-android
[スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[ページングを使用したクエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android

[管理ポータル]: https://manage.windowsazure.com/
[Azure 管理ポータル]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330

