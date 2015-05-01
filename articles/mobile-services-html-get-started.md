<properties 
	pageTitle="HTML 5 アプリ用 Azure Mobile Services の使用" 
	description="次のチュートリアルに従って、HTML 開発用の Azure Mobile Services を使用します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="javascript" 
	ms.topic="hero-article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>


# <a name="getting-started"> </a>Mobile Services の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Azure の Mobile Services を使用して HTML アプリケーションにクラウドベースのバックエンド サービスを追加する方法について説明します。このチュートリアルでは、新しい Mobile Services と、新しい Mobile Services にアプリケーション データを保存する簡単な <em>To do list</em> アプリケーションの両方を作成します。このチュートリアルのビデオ バージョンを表示するには、右側のクリップをクリックします。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">3:51</span></div>
</div>
 
完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

このチュートリアルを完了することは、HTML アプリの他のすべてのMobile Services チュートリアルの前提条件です。 

> [AZURE.IMPORTANT] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成できます。詳細については、[Azure の無料評価版サイト](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fja-jp%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank)をご覧ください。  

### その他の要件

+ このチュートリアルでは、ローカル コンピューター上で次のいずれかの Web サーバーが実行されている必要があります。

	+  **Windows の場合**:IIS Express。IIS Express は、[Microsoft Web プラットフォーム インストーラー]によってインストールされます。   
	+  **MacOS X**:Python。既にインストールされています。
	+  **Linux の場合**:Python。[最新バージョンの Python] をインストールする必要があります。 
	
	このアプリケーションは任意の Web サーバーを使用してホストできますが、これらの Web サーバーは、ダウンロードしたスクリプトでサポートされています。  

+ HTML5 をサポートする Web ブラウザー。


## <a name="create-new-service"> </a>新しい Mobile Services を作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>新しい HTML アプリを作成する</h2>

Mobile Services を作成したら、管理ポータルの簡単なクイック スタートに従って、新しいアプリケーションを作成するか、既存のアプリケーションを変更して Mobile Services に接続できます。 

ここでは、Mobile Services に接続された新しい HTML アプリを作成します。

1.  管理ポータルで、**[Mobile Services]** をクリックし、先ほど作成した Mobile Services をクリックします。

   
2. [クイック スタート] タブの **[プラットフォームの選択]** で **[Windows]** を選択し、**[新しい HTML アプリを作成する]** を展開します。

   	![][6]

   	これにより、Mobile Services に接続された HTML アプリを作成し、ホストするための簡単な 3 つの手順が表示されます。

  	![][7]

3. **[TodoItems テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

4. **[アプリケーションをダウンロードして実行する]** の下の **[ダウンロード]** をクリックします。 

  	これにより、Mobile Services に接続する、_To do list_ サンプル アプリケーションの Web サイト ファイルがダウンロードされます。圧縮されたファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

5. **[構成]** タブで、**[クロス オリジン リソース共有 (CORS)]** の **[ホスト名からの要求を許可する]** に `localhost` が既に表示されていることを確認します。表示されていない場合は、**[ホスト名]** フィールドに「`localhost`」と入力し、**[保存]** をクリックします。

  	![][9]

	> [AZURE.IMPORTANT] localhost 以外の Web サーバーにクイック スタート アプリケーションを展開する場合は、**[ホスト名からの要求を許可する]** の一覧に Web サーバーのホスト名を追加する必要があります。詳細については、「[クロス オリジン リソース共有](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank)」をご覧ください。

## HTML アプリのホストと実行

このチュートリアルの最後に、ローカル コンピューターの新しいアプリケーションをホストして実行します。

1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上に展開して、**server** サブフォルダーから次のコマンド ファイルの 1 つを開始します。

	+ **launch-windows** (Windows コンピューター) 
	+ **launch-mac.command** (Mac OS X コンピューター)
	+ **launch-linux.sh** (Linux コンピューター)

	> [AZURE.NOTE] Windows コンピューターでは、PowerShell からスクリプトの実行の確認を求められた場合は、「R」と入力します。Web ブラウザーでは、インターネットからダウンロードしたスクリプトであるため、実行しないよう警告されることがあります。その場合は、ブラウザーがスクリプトの読み込みを開始するよう要求する必要があります。

	これにより、新しいアプリケーションをホストする Web サーバーがローカル コンピューター上で起動します。

2. Web ブラウザーで <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> を開いて、アプリケーションを開始します。

3. アプリケーションで、意味のあるテキスト (たとえば、チュートリアルの完了) を **[Enter new task]** に入力し、**[Add]** をクリックします。

   	![][10]

   	これで、Azure でホストされている新しい Mobile Services に POST 要求が送信されます。要求のデータは TodoItems テーブルに挿入されます。テーブルに格納された項目は Mobile Services によって返され、データはアプリケーションの 2 番目の列に表示されます。

	> [AZURE.NOTE] Mobile Services にアクセスして app.js ファイルにあるデータを照会および挿入するコードを確認できます。

4. 管理ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。

   	![][11]

   	これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

   	![][12]

## <a name="next-steps"> </a>次のステップ
クイック スタートはこれで完了です。Mobile Services で重要になるこれ以外の作業については、以下のトピックをご覧ください。 

* **[データの使用]**
  <br/>Mobile Services を使用してデータの格納とクエリを実行する方法について説明します。
  
* **[HTML アプリケーションからのカスタム API 呼び出し]**
  <br/>HTML アプリケーションを Mobile Services でホストされるカスタム API に接続します。

* **[認証の使用]**
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* **[Mobile Services HTML/JavaScript の使用方法の概念リファレンス]**
  <br/>HTML/JavaScript で Mobile Services を使用する方法について説明します。 

<!-- Anchors. -->
[Mobile Services の使用]:#getting-started
[新しい Mobile Services を作成する]:#create-new-service
[Mobile Services インスタンスの定義]:#define-mobile-service-instance
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[データの使用]: /develop/mobile/tutorials/get-started-with-data-html
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-html
[HTML アプリケーションからのカスタム API 呼び出し]: mobile-services-html-call-custom-api.md 

[管理ポータル]: https://manage.windowsazure.com/
[Microsoft Web プラットフォーム インストーラー]:  http://go.microsoft.com/fwlink/p/?LinkId=286333
[Python の最新バージョン]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Mobile Services HTML/JavaScript の使用方法の概念リファレンス]: /develop/mobile/how-to-guides/work-with-html-js-client
[クロス オリジン リソース共有]: http://msdn.microsoft.com/library/windowsazure/dn155871.aspx

[最新バージョンの Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342

<!--HONumber=52-->