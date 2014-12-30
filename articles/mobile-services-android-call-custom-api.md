<properties writer="ricksal" pageTitle="Android クライアントからのカスタム API の呼び出し | モバイル デベロッパー センター" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="ricksal,glenga" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal,glenga" />

# クライアントからのカスタム API 呼び出し

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

このトピックでは、Android アプリケーションからカスタム API を呼び出す方法について説明します。カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

このトピックで作成したカスタム API では、モバイル サービスのテーブル内にあるすべての Todo 項目について*完了*フラグを `true` に設定する単一の POST 要求を送信できます。このカスタム API を使用せずにこれを行う場合、クライアントは、テーブル内の各 Todo 項目について個別にフラグの更新要求を送信する必要があります。

チュートリアル「[モバイル サービスの使用]」または「[データの使用]」の最後に作成したアプリケーションが、この機能の追加対象となります。次の手順を実行します。

1. [カスタム API を定義する]
2. [カスタム API を呼び出すようにアプリケーションを更新する]
3. [アプリケーションをテストする] 


>[AZURE.NOTE] アプリケーション全体のソース コードを参照するには、 <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">ここ</a>をクリックしてください。

##前提条件

[WACOM.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>カスタム API を定義する

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]


[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]


## 次のステップ

以上、カスタム API を作成し、Android アプリケーションから呼び出す方法について説明しました。モバイル サービスに関連した次のトピックも参考にしてください。



* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>カスタム API の作成について説明します。

* [ソース管理へのサーバー スクリプトの保存]
  <br/> ソース管理機能を使用して、カスタム API スクリプト コードを簡単かつ安全に開発、発行する方法について説明します。

<!-- Anchors. -->
[カスタム API を定義する]: #define-custom-api
[カスタム API を呼び出すようにアプリケーションを更新する]: #update-app
[アプリケーションをテストする]: #test-app
[次のステップ]: #next-steps

<!-- URLs. -->
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[マイ アプリ ダッシュボード]: http://go.microsoft.com/fwlink/?LinkId=262039
[Mobile Services の使用]: /ja-jp/documentation/articles/mobile-services-android-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-android-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-android-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-android-get-started-push/

[ソース管理へのサーバー スクリプトの保存]: /ja-jp/documentation/articles/mobile-services-store-scripts-source-control

<!--HONumber=35_1-->
