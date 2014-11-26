<properties linkid="mobile-services-html-call-custom-api" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from an HTML client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# HTML アプリケーションからのカスタム API 呼び出し

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" >iOS</a><a href="/ja-jp/documentation/articles/mobile-services-android-call-custom-api" title="Android" >Android</a><a href="/ja-jp/documentation/articles/mobile-services-html-call-custom-api" title="HTML" class="current">HTML</a></div>

このトピックでは、HTML アプリケーションからカスタム API を呼び出す方法について説明します。カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

このトピックで作成したカスタム API では、テーブル内のすべての Todo 項目の完了フラグを `true` に設定する単一の POST 要求を送信できます。このカスタム API を使用せずにこれを行う場合、クライアントは、テーブル内の各 Todo 項目について個別にフラグの更新要求を送信する必要があります。

チュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」の最後に作成したアプリケーションが、この機能の追加対象となります。次の手順を実行します。

1.  [カスタム API を定義する][カスタム API を定義する]
2.  [カスタム API を呼び出すようにアプリケーションを更新する][カスタム API を呼び出すようにアプリケーションを更新する]
3.  [アプリケーションをテストする][アプリケーションをテストする]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」を完了している必要があります。

## <a name="define-custom-api"></a>カスタム API を定義する

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

## <a name="update-app"></a><span class="short-header">アプリケーションの更新</span>カスタム API を呼び出すようにアプリケーションを更新する

1.  テキスト エディターを使用して index.html ファイルを開き、`buttonRefresh` という **button** 要素を探し、その直後に次の要素を新たに追加します。

        <button id="buttonCompleteAll">Complete All</button> 

    新しいボタンがページに追加されます。

2.  page.js で、**refreshTodoItems** 関数の後に、次のコードを追加します。

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

## <a name="test-app"></a> アプリケーションをテストする

1.  ブラウザーの表示を更新します。

2.  アプリケーションで、**[Insert a TodoItem]** に任意のテキストを入力し、**[Save]** をクリックします。

3.  前の手順を繰り返して、複数の Todo 項目をリストに追加します。

4.  **[Complete All]** ボタンをクリックします。完了としてマークされた項目の数を示すメッセージ ダイアログが表示され、フィルター処理済みのクエリが再度実行されて、すべての項目がリストから消去されます。

## 次のステップ

ここまでで、カスタム API を作成し、HTML アプリケーションから呼び出す方法について説明しました。モバイル サービスに関連する次のトピックも参考にしてください。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]
    カスタム API の作成について説明します。

 

  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-html-get-started
  [データの使用]: /ja-jp/documentation/articles/mobile-services-html-get-started-data
  [カスタム API を定義する]: #define-custom-api
  [カスタム API を呼び出すようにアプリケーションを更新する]: #update-app
  [アプリケーションをテストする]: #test-app
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
