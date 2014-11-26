<properties pageTitle="Service-side authorization (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# モバイル サービス ユーザーのサービス側の承認

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="Windows ストア JavaScript" class="current">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS">iOS</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/" title=".NET バックエンド" class="current">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/"  title="JavaScript バックエンド">JavaScript バックエンド</a></div>

このトピックでは、認証済みのユーザーを承認し、Azure モバイル サービスのデータに Windows ストア アプリケーションからアクセスできるようにする方法を説明します。このチュートリアルでは、コントローラー内にあるデータ アクセス メソッドに、認証済みのユーザーの ID に基づいてクエリにフィルター処理を実施するコードを追加します。これによって、それぞれのユーザーが自分のデータのみを閲覧できる状態を実現できます。

このチュートリアルは、モバイル サービスのクイック スタートと、1 つ前の[認証の使用][認証の使用]に関するチュートリアルの内容を前提としています。このため、このチュートリアルの前に、[認証の使用][認証の使用]に関するチュートリアルを完了している必要があります。

## <a name="register-scripts"></a>データ アクセス メソッドの変更

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

## アプリケーションをテストする

1.  Visual Studio で、「[認証の使用][認証の使用]」チュートリアルを実行したときに変更したプロジェクトを開きます。

2.  F5 キーを押してアプリケーションを実行し、選択した ID プロバイダーでログオンします。

    このとき、前のチュートリアルで TodoItem テーブルに項目を挿入していても、項目が返されることはない点に注意してください。このようなことが起こるのは、その項目がユーザー ID 列のない状態で挿入されており、ユーザー ID の値が null になっているためです。

3.  そのアプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[保存]** をクリックします。

    ![][0]

    この操作によって、モバイル サービスの TodoItem テーブルにテキストおよびユーザー ID が挿入されます。新しい項目に正しいユーザー ID が設定されたため、モバイル サービスでその項目が返され、2 番目の列に表示されるようになります。

4.  (省略可能) ログイン アカウントがほかにある場合には、ユーザーがそれぞれ自分のデータのみを閲覧できる状態になっていることを確認できます。これにはまず、アプリケーションを終了 (Alt + F4) して再度実行します。ログイン資格情報の入力を求めるダイアログが表示されたら別のログインを入力し、前のアカウントで入力した項目が表示されないことを確認してください。

## 次のステップ

これで、認証の基本について説明するチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

-   [データの使用][データの使用]
    Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [Azure モバイル サービス向け HTML/JavaScript クライアントを使用する方法][Azure モバイル サービス向け HTML/JavaScript クライアントを使用する方法]
    HTML および JavaScript でモバイル サービスを使用する方法について説明します。

 
 


  [認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/mobile-quickstart-startup.png
  [データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Azure モバイル サービス向け HTML/JavaScript クライアントを使用する方法]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library
