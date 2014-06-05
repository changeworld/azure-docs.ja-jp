<properties pageTitle="サービス側の承認 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="" description="Azure モバイル サービスの .NET バックエンドでユーザーを承認する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービス ユーザーのサービス側の承認" authors="glenga" solutions="" manager="" editor="" />

# モバイル サービス ユーザーのサービス側の承認

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="Windows ストア C#" class="current">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS">iOS</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/" title=".NET バックエンド" class="current">.NET バックエンド</a> | <a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/"  title="JavaScript バックエンド">JavaScript バックエンド</a></div>

このトピックでは、認証済みのユーザーを承認し、Azure モバイル サービスのデータに Windows ストア アプリケーションからアクセスできるようにする方法を説明します。このチュートリアルでは、コントローラー内にあるデータ アクセス メソッドに、認証済みのユーザーの ID に基づいてクエリにフィルター処理を実施するコードを追加します。これによって、それぞれのユーザーが自分のデータのみを閲覧できる状態を実現できます。

このチュートリアルは、モバイル サービスのクイック スタートと、1 つ前の[認証の使用]に関するチュートリアルの内容を前提としています。このため、このチュートリアルの前に、[認証の使用]に関するチュートリアルを完了している必要があります。

## <a name="register-scripts"></a>データ アクセス メソッドの変更

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)] 


## アプリケーションをテストする

1. Visual Studio で、「[認証の使用]」チュートリアルを実行したときに変更したプロジェクトを開きます。

2. F5 キーを押してアプリケーションを実行し、選択した ID プロバイダーでログオンします。

   	このとき、前のチュートリアルで TodoItem テーブルに項目を挿入していても、項目が返されることはない点に注意してください。このようなことが起こるのは、その項目がユーザー ID 列のない状態で挿入されており、ユーザー ID の値が null になっているためです。

3. そのアプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

   	![][3]

   	この操作によって、モバイル サービスの TodoItem テーブルにテキストおよびユーザー ID が挿入されます。新しい項目に正しいユーザー ID が設定されたため、モバイル サービスでその項目が返され、2 番目の列に表示されるようになります。

6. (省略可能) ログイン アカウントがほかにある場合には、ユーザーがそれぞれ自分のデータのみを閲覧できる状態になっていることを確認できます。これにはまず、アプリケーションを終了 (Alt + F4) して再度実行します。ログイン資格情報の入力を求めるダイアログが表示されたら別のログインを入力し、前のアカウントで入力した項目が表示されないことを確認してください。

## 次のステップ

これで、認証の基本について説明するチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [データの使用]
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。
  
* [モバイル サービス .NET の使用方法の概念リファレンス]
  <br/>.NET でモバイル サービスを使用する方法について説明します

<!-- Anchors. -->
[サーバー スクリプトを登録する]: #register-scripts
[次のステップ]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push

[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

