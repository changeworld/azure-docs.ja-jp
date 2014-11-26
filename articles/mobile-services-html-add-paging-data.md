<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# ページングを使用したモバイル サービス クエリの改善

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

このトピックでは、ページングを使用して、Azure のモバイル サービスから HTML アプリケーションに返されるデータの量を管理する方法について説明します。このチュートリアルでは、クライアントで **Take** および **Skip** クエリ メソッドを使用して、データの特定の "ページ" を要求します。

<div class="dev-callout"><b>注</b>
<p>モバイル デバイス クライアントでデータがオーバーフローしないように、モバイル サービスでは、自動ページ制限を実装しています。既定では、1 つの応答で最大 50 項目に設定されます。ページ サイズを指定することで、1 つの応答で 1,000 項目まで明示的に要求できます。</p>
</div>

このチュートリアルは、前の[データの使用][データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、少なくとも、データ操作シリーズの最初のチュートリアル (「[データの使用][データの使用]」) を完了している必要があります。

1.  [データの使用][データの使用]に関するチュートリアルを実行したときに変更したプロジェクトの **server** サブフォルダーから次のコマンド ファイルのいずれかを実行します。

    -   **launch-windows** (Windows コンピューター)
    -   **launch-mac.command** (Mac OS X コンピューター)
    -   **launch-linux.sh** (Linux コンピューター)

    <div class="dev-callout"><b>注</b>
<p>Windows コンピューターでは、PowerShell からスクリプトの実行の確認を求められた場合は、「R」と入力します。Web ブラウザーでは、インターネットからダウンロードしたスクリプトであるため、実行しないよう警告されることがあります。その場合は、ブラウザーがスクリプトの読み込みを開始するよう要求する必要があります。</p>
</div>

    これにより、アプリケーションをホストする Web サーバーがローカル コンピューター上で起動します。

2.  Web ブラウザーで、<http://localhost:8000/> に移動し、**[Add new task]** にテキストを入力して、**[追加]** をクリックします。

3.  前の手順を少なくとも 3 回繰り返して、TodoItem テーブルに項目を 3 つ以上保存します。

4.  app.js ファイルで、**refreshTodoItems** メソッドの `query` 変数の定義を次のコード行に置き換えます。

        var query = todoItemTable.where({ complete: false }).take(3);

    このクエリでは、実行時に、完了マークが付けられていない上位 3 つの項目を返します。

5.  Web ブラウザーに戻り、ページを再読み込みします。

    TodoItem テーブルから最初の 3 つの結果だけが表示されることに注目してください。

6.  (省略可能) ブラウザー開発者ツールや [Fiddler] などのメッセージ検査ソフトウェアを使用して、モバイル サービスに送信された要求の URI を表示します。

    クエリの URI では、**take(3)** メソッドがクエリ オプション **$top=3** に変換されていることに注目してください。

7.  再度 query を次のコードに置き換えます。

        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

8.  Web ブラウザーに戻り、ページを再読み込みします。

    このクエリでは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

    <div class="dev-callout"><b>注</b>
<p>このチュートリアルでは、ハードコーディングされたページング値を <strong>Take</strong> メソッドおよび <strong>Skip</strong> メソッドに渡すことで簡略化したシナリオを使用しています。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。また、<strong>includeTotalCount</strong> メソッドを呼び出して、ページングされたデータと共に、サーバーで使用できる項目の合計数を取得することもできます。</p>
</div>

9.  (省略可能) 再度、モバイル サービスに送信された要求の URI を表示します。

    クエリの URI では、**skip(3)** メソッドがクエリ オプション **$skip=3** に変換されていることに注目してください。

## <a name="next-steps"> </a> 次のステップ

これで、モバイル サービスのデータを操作するための基本について説明する一連のチュートリアルは終了です。次は、チュートリアル「[認証の使用][認証の使用]」でアプリケーションのユーザーを認証する方法について説明します。[モバイル サービス HTML/JavaScript の使用方法の概念リファレンス][モバイル サービス HTML/JavaScript の使用方法の概念リファレンス] で、HTML/JavaScript でモバイル サービスを使用する方法について説明します。

 
 


  [データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-html
  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-html
  [モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-html-js-client
