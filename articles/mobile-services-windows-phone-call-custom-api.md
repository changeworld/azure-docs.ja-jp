<properties urlDisplayName="Call a custom API from the client" pageTitle="Windows Phone クライアントからのカスタム API 呼び出し - モバイル サービス" metaKeywords="" description="カスタム API を定義し、Azure Mobile Services を使用する Windows Phone アプリケーションからそれを呼び出す方法について説明します。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="10/06/2014" ms.author="glenga" />

# クライアントからのカスタム API 呼び出し

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

このトピックでは、Windows Phone アプリケーションからカスタム API を呼び出す方法について説明します。カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

このトピックで作成したカスタム API では、テーブル内のすべての Todo 項目の完了フラグを true に設定する単一の POST 要求を送信できます。このカスタム API を使用せずにこれを行う場合、クライアントは、テーブル内の各 Todo 項目について個別にフラグの更新要求を送信する必要があります。

この機能は、チュートリアル [既存のアプリケーションへの Mobile Services の追加] を完了したときに作成したアプリに(/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data/) 追加します。次の手順を実行します。

1. [カスタム API を定義する]
2. [カスタム API を呼び出すようにアプリケーションを更新する]
3. [アプリケーションをテストする]

このチュートリアルは、簡単な TodoList アプリケーションである GetStartedWithData サンプルに基づいています。このチュートリアルを開始する前に、[既存のアプリにモバイル サービスを追加する] を完了する必要があります(/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data/)。

## <a name="define-custom-api"></a>カスタム API を定義する

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]

## 次のステップ

以上、カスタム API を作成し、Windows Phone アプリケーションから呼び出す方法について説明しました。モバイル サービスに関連した次のトピックも参考にしてください。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>カスタム API の作成について説明します。

* [ソース管理へのサーバー スクリプトの保存]
  <br/> ソース管理機能を使用して、カスタム API スクリプト コードを簡単かつ安全に開発、発行する方法について説明します。

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

<!--HONumber=35.2-->
