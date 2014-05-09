<properties linkid="mobile-services-call-custom-api-wp8" urlDisplayName="クライアントからのカスタム API 呼び出し" pageTitle="Windows Phone クライアントからのカスタム API 呼び出し - モバイル サービス" metaKeywords="" description="カスタム API を定義し、次に Azure モバイル サービスを使用する Windows Phone アプリケーションからその API を呼び出す方法について説明します。" metaCanonical="" services="" documentationCenter="" title="クライアントからのカスタム API 呼び出し" authors="glenga" solutions="" manager="" editor="" />




# クライアントからのカスタム API 呼び出し

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone" class="current">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-ios-call-custom-api" title="iOS">iOS</a><a href="/ja-jp/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a>
</div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api" title=".NET バックエンド">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-windows-phone-call-custom-api"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a></div>

このトピックでは、Windows Phone アプリケーションからカスタム API を呼び出す方法について説明します。カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

このトピックで作成したカスタム API では、todoitem テーブル内のすべての Todo 項目の完了フラグを `true` に設定する単一の POST 要求を送信できます。このカスタム API を使用せずにこれを行う場合、クライアントは、テーブル内の各 Todo 項目について個別にフラグの更新要求を送信する必要があります。

チュートリアル「[モバイル サービスの使用]」または「[データの使用]」の最後に作成したアプリケーションが、この機能の追加対象となります。次の手順を実行します。

1. [カスタム API を定義する]
2. [カスタム API を呼び出すようにアプリケーションを更新する]
3. [アプリケーションをテストする]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用]」または「[データの使用]」を完了している必要があります。このチュートリアルでは、Visual Studio 2012 Express for Windows Phone を使用します。

## <a name="define-custom-api"></a>カスタム API を定義する

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]

## 次のステップ

以上、カスタム API を作成し、Windows Phone アプリケーションから呼び出す方法について説明しました。モバイル サービスに関連した次のトピックも参考にしてください。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>カスタム API の作成について説明します。

* [ソース管理へのサーバー スクリプトの保存]
  <br/>ソース管理機能を使用して、カスタム API スクリプト コードを簡単かつ安全に開発、発行する方法について説明します。

<!-- Anchors. -->
[カスタム API を定義する]: #define-custom-api
[カスタム API を呼び出すようにアプリケーションを更新する]: #update-app
[アプリケーションをテストする]: #test-app
[次のステップ]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push/

[ソース管理へのサーバー スクリプトの保存]: /ja-jp/documentation/articles/mobile-services-store-scripts-source-control

