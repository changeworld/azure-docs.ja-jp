<properties linkid="develop-mobile-tutorials-authorize-users-in-scripts-xamarin-android" urlDisplayName="Authorize Users in Scripts (Xamarin.Android)" pageTitle="Authorize users in scripts (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure authorizing user, Xamarin.Android scripts authorization, authorize mobile services" description="Learn how to authorize users with scripts in your Azure Mobile Services app for Xamarin.Android." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Use scripts to authorize users in Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# モバイル サービスでユーザー承認にスクリプトを使用する

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios" title="Xamarin.iOS">iOS C#</a><a href="/ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android" title="Xamarin.Android" class="current">Android C#</a>
</div>

このトピックでは、認証済みのユーザーをサーバー スクリプトで承認し、Azure モバイル サービスのデータに Xamarin.Android アプリケーションからアクセスできるようにする方法を説明します。このチュートリアルでは、認証済みのユーザーの ID に基づいてクエリにフィルター処理を実施するスクリプトをモバイル サービスに登録します。これによって、それぞれのユーザーが自分のデータのみを閲覧できる状態を実現できます。

このチュートリアルは、モバイル サービスのクイック スタートと、1 つ前の[認証の使用][認証の使用]に関するチュートリアルの内容を前提としています。このため、このチュートリアルの前に、[認証の使用][認証の使用]に関するチュートリアルを完了している必要があります。

## <a name="register-scripts"></a>スクリプトを登録する

クイック スタート アプリケーションでは、データの読み取りおよび挿入を実行します。このため、TodoItem テーブルにそのような操作を実行するためのスクリプトを登録する必要があります。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。

    ![][0]

2.  **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][1]

3.  **[スクリプト]** をクリックし、**[挿入]** 操作を選択します。

    ![][2]

4.  既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    このスクリプトは、ユーザー ID の値 (認証済みのユーザーの ID) を TodoItem テーブルに挿入する前に、項目に追加するためのものです。

    <div class="dev-callout">

    <b>注</b>
    <p>挿入スクリプトを初めて実行するときには、動的スキーマを必ず有効にしてください。動的スキーマが有効になっていると、挿入スクリプトを最初に実行した時点でモバイル サービスによって <b>TodoItem</b> テーブルに <b>[ユーザー ID]</b> 列が自動で追加されます。動的スキーマは、新しいモバイル サービスでは既定で有効になっているため、アプリケーションを Windows ストアに発行する前に無効にする必要があります。</p>

    </div>

5.  手順 3. および 4. を繰り返し、既存の**読み取り**操作を以下の関数で置き換えます。

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    このスクリプトは、返される TodoItem オブジェクトにフィルター処理を実施して、それぞれのユーザーが自分で挿入した項目のみを受け取るようにするためのものです。

## アプリケーションをテストする

1.  Xamarin Studio または Visual Studio で、「[認証の使用][認証の使用]」チュートリアルを実行したときに変更したプロジェクトを開きます。

2.  **[Run]** をクリックしてアプリケーションを開始し、選択した ID プロバイダーでサインインします。

    このとき、前のチュートリアルで TodoItem テーブルに項目を挿入していても、項目が返されることはない点に注意してください。このようなことが起こるのは、その項目がユーザー ID 列のない状態で挿入されており、ユーザー ID の値が null になっているためです。

3.  そのアプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

    この操作によって、モバイル サービスの TodoItem テーブルにテキストおよびユーザー ID が挿入されます。新しい項目に正しいユーザー ID が設定されたため、モバイル サービスでその項目が返され、2 番目の列に表示されるようになります。

4.  [管理ポータル][Azure の管理ポータル]の **TodoItem** テーブルに戻り、**[参照]** をクリックして、新しく追加された項目に対してユーザー ID の値が設定されているかどうかを確認します。

5.  (省略可能) ログイン アカウントが他にある場合には、ユーザーがそれぞれ自分のデータのみを閲覧できる状態になっていることを確認できます。これにはまず、アプリケーションを終了して再度実行します。ログイン資格情報の入力を求めるダイアログが表示されたら別のログインを入力し、前のアカウントで入力した項目が表示されないことを確認してください。

## 次のステップ

これで、認証の基本について説明するチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

-   [データの使用][データの使用]
   
    Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
   
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

-   [モバイル サービスのサーバー スクリプト リファレンス][モバイル サービスのサーバー スクリプト リファレンス]
   
    サーバー スクリプトの登録および使用について説明します。



  [認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png
  [データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android
  [モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p/?LinkId=262293
