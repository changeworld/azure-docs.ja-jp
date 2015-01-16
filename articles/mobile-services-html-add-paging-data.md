<properties urlDisplayName="Add paging to data (HTML5)" pageTitle="データへのページングの追加 (HTML 5) | モバイル デベロッパー センター" metaKeywords="" description="ページングを使用して、Mobile Services から HTML アプリに返されるデータの量を管理する方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# ページングを使用したモバイル サービス クエリの改善

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

このトピックでは、ページングを使用して、Azure Mobile Services から HTML アプリケーションに返されるデータの量を管理する方法について説明します。このチュートリアルでは、クライアントで **Take** および **Skip** クエリ メソッドを使用して、データの特定の "ページ" を要求します。

<div class="dev-callout"><b>注</b>
<p>モバイル デバイス クライアントでデータがオーバーフローしないように、モバイル サービスでは、自動ページ制限を実装しています。既定では、1 つの応答で最大 50 項目に設定されます。ページ サイズを指定することで、1 つの応答で 1,000 項目まで明示的に要求できます。</p>
</div>

このチュートリアルは、前の[データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、少なくともデータ操作シリーズの最初の[データの使用]に関するチュートリアルを完了している必要があります。 

1. 「[データの使用]」チュートリアルを実行したときに変更したプロジェクトの **server** サブフォルダーから次のコマンド ファイルのいずれかを実行します。

	+ **launch-windows** (Windows コンピューター) 
	+ **launch-mac.command** (Mac OS X コンピューター)
	+ **launch-linux.sh** (Linux コンピューター)

	<div class="dev-callout"><b>注</b>
		<p>Windows コンピューターでは、PowerShell からスクリプトの実行の確認を求められた場合は、「`R`」と入力します。Web ブラウザーでは、インターネットからダウンロードしたスクリプトであるため、実行しないよう警告されることがあります。その場合は、ブラウザーがスクリプトの読み込みを開始するよう要求する必要があります。</p>
	</div>

	これにより、アプリケーションをホストする Web サーバーがローカル コンピューター上で起動します。

1. Web ブラウザーで、<a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> に移動し、**[Add new task]** にテキストを入力して、**[Add]** をクリックします。

3. 前の手順を少なくとも 3 回繰り返して、TodoItem テーブルに項目を 3 つ以上保存します。 

2. app.js ファイルで、**refreshTodoItems** メソッドの `query` 変数の定義を次のコード行に置き換えます。

       
        var query = todoItemTable.where({ complete: false }).take(3);

  	このクエリでは、実行時に、完了マークが付けられていない上位 3 つの項目を返します。

3. Web ブラウザーに戻り、ページを再読み込みします。

  	TodoItem テーブルから最初の 3 つの結果だけが表示されることに注目してください。 

4. (省略可能) ブラウザー開発者ツールや [Fiddler] などのメッセージ検査ソフトウェアを使用して、モバイル サービスに送信された要求の URI を表示します。 

   	クエリの URI では、**take(3)** メソッドがクエリ オプション **$top=3** に変換されていることに注目してください。

5. 再度 query を次のコードに置き換えます。
            
        var query = todoItemTable.where({ complete: false }).skip(3).take(3);

3. Web ブラウザーに戻り、ページを再読み込みします。

   	このクエリでは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

    <div class="dev-callout"><b>注</b>
    <p>このチュートリアルでは、ハードコーディングされたページング値を <strong>Take</strong> メソッドおよび <strong>Skip</strong> メソッドに渡すことで簡略化したシナリオを使用しています。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。  また、  <strong>includeTotalCount</strong> メソッドを呼び出して、ページングされたデータと共に、サーバーで使用できる項目の合計数を取得することもできます。</p>
    </div>

6. (省略可能) 再度、モバイル サービスに送信された要求の URI を表示します。 

   	クエリの URI では、**skip(3)** メソッドがクエリ オプション **$skip=3** に変換されていることに注目してください。

## <a name="next-steps"> </a>次のステップ

これで、モバイル サービスのデータを操作するための基本について説明する一連のチュートリアルは終了です。次は、チュートリアル「[認証の使用]」でアプリケーションのユーザーを認証する方法について説明します。[モバイル サービス HTML/JavaScript の使用方法の概念リファレンス] で、HTML/JavaScript でモバイル サービスを使用する方法について説明します。

<!-- Anchors. -->

[次のステップ]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-html
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-html
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-html


[管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-html-js-client

