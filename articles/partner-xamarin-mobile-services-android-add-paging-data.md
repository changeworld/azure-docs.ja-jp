<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-android" urlDisplayName="データへのページングの追加" pageTitle="データへのページングの追加 (Xamarin Android) | モバイル デベロッパー センター" metaKeywords="" description="ページングを使用して、モバイル サービスから Xamarin Android アプリケーションに返されるデータの量を管理する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="ページングを使用したモバイル サービス クエリの改善" authors="" />

# ページングを使用したモバイル サービス クエリの改善
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">iOS C#</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Android C#</a>
</div>

このトピックでは、ページングを使用して、Azure モバイル サービスから Xamarin.Android アプリに返されるデータの量を管理する方法について説明します。このチュートリアルでは、クライアントで **Take** および **Skip** クエリ メソッドを使用して、データの特定の "ページ" を要求します。

<div class="dev-callout"><b>注</b>
<p>モバイル デバイス クライアントでデータがオーバーフローしないように、モバイル サービスでは、自動ページ制限を実装しています。既定では、1 つの応答で最大 50 項目に設定されます。ページ サイズを指定することで、1 つの応答で 1,000 項目まで明示的に要求できます。</p>
</div>

このチュートリアルは、前の[データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、少なくとも、データ操作シリーズの最初のチュートリアル (「[データの使用]」) を完了している必要があります。

1. Xamarin Studio で、「[モバイル サービスでのデータの使用]」チュートリアルを実行したときに作成したプロジェクトを開きます。

2. **[Run]** をクリックして、アプリケーションを開始します。次に、テキスト ボックスにテキストを入力し、**[Add]** をクリックします。

3. 前の手順を少なくとも 3 回繰り返して、3 つを超える項目を TodoItem テーブルに保存します。

4. **TodoActivity.cs** ファイルで、**RefreshItemsFromTableAsync** メソッドの LINQ クエリを次のクエリで置き換えます。

		var list = await todoTable.Where(item => item.Complete == false)
						          .Take(3)
			                      .ToListAsync();

	  このクエリでは、完了マークが付けられていない上位 3 つの項目を返します。

5. アプリケーションをリビルドして開始します。
   
    TodoItem テーブルから最初の 3 つの結果だけが表示されることに注目してください。

6. (省略可能) ブラウザー開発者ツールや [Fiddler] などのメッセージ検査ソフトウェアを使用して、モバイル サービスに送信された要求の URI を表示します。

   	クエリの URI では、`Take(3)` メソッドがクエリ オプション `$top=3` に変換されていることに注目してください。

7. 再度、**RefreshItemsFromTableAsync** メソッドの LINQ クエリを次のクエリに置き換えます。
            
			var list = await todoTable.Where(item => item.Complete == false)
							          .Skip(3)
				                      .Take(3)
                 				      .ToListAsync();

   	このクエリでは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

    <div class="dev-callout"><b>注</b>
    <p>このチュートリアルでは、ハードコーディングされたページング値を <strong>Take</strong> メソッドおよび <strong>Skip</strong> メソッドに渡すことで簡略化したシナリオを使用しています。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。また、<strong>IncludeTotalCount</strong> メソッドを呼び出して、ページングされたデータと共に、サーバーで使用できる項目の合計数を取得することもできます。</p>
    </div>

8. (省略可能) 再度、モバイル サービスに送信された要求の URI を表示します。

   	クエリの URI では、`Skip(3)` メソッドがクエリ オプション `$skip=3` に変換されていることに注目してください。

## <a name="next-steps"> </a>次のステップ

これで、モバイル サービスのデータを操作するための基本について説明する一連のチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [認証の使用]
  <br/>Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。
 
* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->

[次のステップ]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-android
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android

[管理ポータル]: https://manage.windowsazure.com/


