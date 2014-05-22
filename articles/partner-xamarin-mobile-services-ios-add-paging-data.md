<properties linkid="" urlDisplayName="" pageTitle="データへのページングの追加 | モバイル デベロッパー センター" metaKeywords="" description="ページングを使用して、モバイル サービスから Xamarin iOS アプリケーションに返されるデータの量を管理する方法について説明します。" metaCanonical="" services="" authors="" solutions="" manager="" editor="" title="ページングを使用したモバイル サービス クエリの改善" documentationCenter="Mobile" />


# ページングを使用したモバイル サービス クエリの改善

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Android C#</a>
</div>


このトピックでは、ページングを使用して、Azure モバイル サービスから iOS アプリケーションに返されるデータの量を管理する方法について説明します。このチュートリアルでは、クライアントで **Skip** および **Take** クエリ プロパティを使用して、データの特定の "ページ" を要求します。

<div class="dev-callout"><b>注</b>
<p>モバイル デバイス クライアントでデータがオーバーフローしないように、モバイル サービスでは、自動ページ制限を実装しています。既定では、1 つの応答で最大 50 項目に設定されます。ページ サイズを指定することで、1 つの応答で 1,000 項目まで明示的に要求できます。</p>
</div>

このチュートリアルは、前の[データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、少なくとも、データ操作シリーズの最初のチュートリアル (「[データの使用]」) を完了している必要があります。

1. Xamarin Studio で、「[モバイル サービスでのデータの使用]」チュートリアルを実行したときに変更したプロジェクトを開きます。

2. **[Run]** をクリックして、プロジェクトをビルドし、アプリケーションを開始します。次に、テキスト ボックスにテキストを入力し、プラス (**[+]**) アイコンをクリックします。

3. 前の手順を少なくとも 3 回繰り返して、3 つを超える項目を TodoItem テーブルに保存します。

4. **TodoService.cs** ファイルを開き、**RefreshDataAsync** メソッドを見つけ、<code>todoTable</code> の LINQ クエリを次のクエリで置き換えます。

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
        		            .Take(3)
                		    .ToListAsync();

   	このクエリでは、完了マークが付けられていない上位 3 つの項目を返します。

5. アプリケーションをリビルドして開始します。
   
    TodoItem テーブルから最初の 3 つの結果だけが表示されることに注目してください。

7. 再度、**RefreshDataAsync** メソッドの LINQ クエリを次のクエリに置き換えます。

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
		                    .Skip(3)
		                    .Take(3)
		                    .ToListAsync();

   	今回は、**Skip** の値を 3 に設定します。

   	このクエリでは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

    <div class="dev-callout"><b>注</b>
    <p>このチュートリアルでは、<strong>Skip</strong> および <strong>Take</strong> プロパティにハードコーディングされたページング値を設定することで簡略化したシナリオを使用しています。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。また、<strong>IncludeTotalCount</strong> メソッドを呼び出して、ページングされたデータと共に、サーバーで使用できるすべての項目の合計数を取得することもできます。</p>
    </div>

## <a name="next-steps"> </a>次のステップ

これで、モバイル サービスのデータを操作するための基本について説明する一連のチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [認証の使用]
  <br/>Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。
 
<!--
* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。
-->

<!-- Anchors. -->

[次のステップ]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[モバイル サービスの使用]: ./ja-jp/develop/mobile/tutorials/get-started-xamarin-ios
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[管理ポータル]: https://manage.windowsazure.com/

