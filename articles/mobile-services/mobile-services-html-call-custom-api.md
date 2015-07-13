<properties 
	pageTitle="HTML クライアントからのカスタム API 呼び出し - Mobile Services" 
	description="カスタム API を定義し、Azure Mobile Services を使用する HTML アプリからそれを呼び出す方法について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="bureado"  
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# HTML アプリケーションからのカスタム API 呼び出し

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

このトピックでは、HTML アプリケーションからカスタム API を呼び出す方法について説明します。カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

このトピックで作成したカスタム API では、テーブル内のすべての Todo 項目の完了フラグを `true` に設定する単一の POST 要求を送信できます。このカスタム API を使用せずにこれを行う場合、クライアントは、テーブル内の各 Todo 項目について個別にフラグの更新要求を送信する必要があります。

このチュートリアルは、Mobile Services のクイック スタートに基づいています。このチュートリアルを開始する前に、「[Mobile Services の使用]」または[既存のアプリケーションへの Mobile Services の追加]に関するチュートリアルを完了しておく必要があります。

## <a name="define-custom-api"></a>カスタム API を定義する

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

##<a name="update-app"></a>カスタム API を呼び出すようにアプリケーションを更新する

1. テキスト エディターを使用して index.html ファイルを開き、`buttonRefresh` という **button** 要素を探し、その直後に次の要素を新たに追加します。 

		<button id="buttonCompleteAll">Complete All</button> 

	新しいボタンがページに追加されます。

2. page.js で、**refreshTodoItems** 関数の後に、次のコードを追加します。

		var completeAllTodoItems = function () {
			// Asynchronously call the custom API using the POST method.
			client.invokeApi("completeall", {
				body: null,
				method: "post"
			}).done(function (results) {
				var message = results.result.count + " item(s) marked as complete.";
				alert(message);
				refreshTodoItems();
			}, function(error) {
				alert(error.message);
			});
		};

		$('#buttonCompleteAll').click(function () {
			completeAllTodoItems();
		});

	これは、新しいボタンの **Click** イベントを処理するメソッドです。POST 要求を新しいカスタム API に送信する **invokeApi** メソッドがクライアントで呼び出されます。カスタム API から返された結果は、メッセージ ダイアログに表示されます。エラーが発生した場合はそれらも表示されます。

## <a name="test-app"></a>アプリケーションをテストする

1. ブラウザーの表示を更新します。

2. アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

3. 前の手順を繰り返して、複数の Todo 項目をリストに追加します。

4. **[Complete All]** ボタンをクリックします。完了としてマークされた項目の数を示すメッセージ ダイアログが表示され、フィルター処理済みのクエリが再度実行されて、すべての項目がリストから消去されます。

## 次のステップ

このトピックでは、**invokeApi** 関数を使用して、HTML/JavaScript アプリからごく簡単なカスタム API を呼び出す方法を紹介しました。**invokeApi** 関数の詳しい使用方法については、[Azure Mobile Services のカスタム API に関する投稿](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx)を参照してください。

また、次の Mobile Services のトピックの詳細を確認することをお勧めします。

* [Mobile Services のサーバー スクリプト リファレンス] <br/>カスタム API の作成について説明します。

* [ソース管理へのサーバー スクリプトの保存] <br/> ソース管理機能を使用して、カスタム API スクリプト コードを簡単かつ安全に開発、発行する方法について説明します。

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Mobile Services のサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services の使用]: mobile-services-html-get-started.md
[既存のアプリケーションへの Mobile Services の追加]: mobile-services-html-get-started-data.md
[ソース管理へのサーバー スクリプトの保存]: mobile-services-store-scripts-source-control.md

<!---HONumber=July15_HO1-->