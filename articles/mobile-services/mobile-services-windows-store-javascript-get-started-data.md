<properties
	pageTitle="既存のアプリへの Mobile Services の追加 (Windows ストア JavaScript) | Microsoft Azure"
	description="Mobile Services を使用して Windows ストア JavaScript アプリでデータを活用する方法について説明します。"
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="glenga"/>


# 既存のアプリケーションへの Mobile Services の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../../includes/mobile-services-selector-get-started-data-legacy.md)]

##概要
このトピックでは、Azure のモバイル サービスを使用して Windows ストア アプリケーションのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションの Visual Studio 2013 プロジェクトをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにサインインして、アプリケーションの実行中にデータに加えられた変更を表示します。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F)を参照してください。
* Visual Studio 2013。Windows ストア アプリ プロジェクトを Mobile Services に接続できる機能を備えています。

##GetStartedWithData プロジェクトをダウンロードする

このチュートリアルは、Visual Studio 2013 の Windows ストア アプリケーション プロジェクトである [GetStartedWithData アプリケーション][Developer Code Samples site]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービスのクイック スタートで生成したアプリケーションと同じです。

1. JavaScript バージョンの GetStartedWithData サンプル アプリケーションを[デベロッパー サンプル コード集のサイト]からダウンロードします。

2. Visual Studio で、ダウンロードしたプロジェクトを開き、**js** フォルダーを展開して、default.js ファイルを確認します。

   	追加された **TodoItem** オブジェクトはメモリ内の **List** オブジェクトに格納されます。

3. **F5** キーを押してプロジェクトをリビルドし、アプリケーションを開始します。

4. アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

   	![][0]

   	保存したテキストが、**[Query and update data]** の下の 2 番目の列に表示されます。

##<a name="create-service"></a>Visual Studio からの新しいモバイル サービスの作成

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>ソリューション エクスプローラーで、**サービス**、**モバイル サービス**、**&lt;モバイル サービス名>** の各フォルダーを順に展開します。service.js スクリプト ファイルを開き、新しいグローバル変数を確認します。次に例を示します。</p>

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>このコードは、グローバル変数を使用して、アプリケーションの新しいモバイル サービスへのアクセスを提供します。クライアントは、新しいモバイル サービスの URI とアプリケーション キーを提供することによって作成されます。このスクリプトへの参照は default.html ファイルに追加されているため、この変数は、このページからも参照されるすべてのスクリプト ファイルで使用できます。</p>
</li>
</ol>

##データ ストレージへの新しいテーブルの追加

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##モバイル サービスを使用するためのアプリケーションの更新

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../../includes/mobile-services-windows-javascript-update-data-app.md)]

##新しいモバイル サービスに対するアプリケーションのテスト

1. Visual Studio で、F5 キーを押してアプリケーションを実行します。

2. ここでも、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	**TodoItem** テーブルに、モバイル サービスによって生成された ID 値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

5. アプリケーションで、リストの項目の 1 つをチェックします。ポータルの **[参照]** タブに戻り、[最新の情報に更新] をクリックします。

  	"complete" 値が **false** から **true** に変更されます。

6. default.js プロジェクト ファイルで、既存の **RefreshTodoItems** 関数を、完了済み項目をフィルター処理で除外する次のコード行で置き換えます。

        var refreshTodoItems = function () {
            // More advanced query that filters out completed items.
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });
        };

7. アプリケーションで、リストの項目をもう 1 つチェックし、**[最新の情報に更新]** をクリックします。

   	リスト内のチェックされた項目が非表示になります。最新の情報への更新のたびにモバイル サービスへの往復が発生し、その結果、フィルター処理されたデータが返されます。

これで、**データの使用**に関するチュートリアルはおしまいです。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Windows ストア アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。次に、以下のその他のトピックのいずれかを読むことを検討してください。

* [アプリへの認証の追加](mobile-services-windows-store-javascript-get-started-users.md) <br/>アプリケーションのユーザーを認証する方法について説明します。

* [アプリケーションにプッシュ通知を追加する](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md) <br/>アプリにごく基本的なプッシュ通知を送信する方法について説明します。

* [Mobile Services サービス HTML/JavaScript の使用方法の概念リファレンス](mobile-services-html-how-to-use-client-library.md) <br/>HTML と JavaScript で Mobile Services を使用する方法について説明します。

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Azure Management Portal]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[デベロッパー サンプル コード集のサイト]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md

<!---HONumber=September15_HO1-->