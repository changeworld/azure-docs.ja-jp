<properties 
	pageTitle="スクリプトを使用したユーザーの承認 (Xamarin.Android) - Azure モバイル サービス" 
	description="Xamarin.Android 向け Azure Mobile Services アプリケーションでスクリプトを使用してユーザーを承認する方法について説明します。" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services" 
	documentationCenter=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# モバイル サービスでユーザー承認にスクリプトを使用する

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	

このトピックでは、認証済みのユーザーをサーバー スクリプトで承認し、Azure Mobile Services のデータに Xamarin.Android アプリケーションからアクセスできるようにする方法を説明します。このチュートリアルでは、認証済みのユーザーの ID に基づいてクエリにフィルター処理を実施するスクリプトをモバイル サービスに登録します。これによって、それぞれのユーザーが自分のデータのみを閲覧できる状態を実現できます。

このチュートリアルは、モバイル サービスのクイック スタートと、1 つ前の[認証の使用]に関するチュートリアルの内容を前提としています。このため、このチュートリアルの前に、[認証の使用]に関するチュートリアルを完了している必要があります。  

## <a name="register-scripts"></a>スクリプトを登録する
クイック スタート アプリケーションでは、データの読み取りおよび挿入を実行します。このため、TodoItem テーブルにそのような操作を実行するためのスクリプトを登録する必要があります。

1. [Azure の管理ポータル]にログオンし、**[Mobile Services]** をクリックして、アプリケーションをクリックします。 

   	![][0]

2. **[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   	![][1]

3. **[スクリプト]** をクリックし、**[挿入]** 操作を選択します。

   	![][2]

4. 既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    このスクリプトは、ユーザー ID の値 (認証済みのユーザーの ID) を TodoItem テーブルに挿入する前に、項目に追加するためのものです。 

    > [AZURE.NOTE] 挿入スクリプトを初めて実行するときには、動的スキーマを必ず有効にしてください。動的スキーマが有効になっていると、挿入スクリプトを最初に実行した時点でモバイル サービスによって **TodoItem** テーブルに **[ユーザー ID]** 列が自動で追加されます。動的スキーマは、新しいモバイル サービスでは既定で有効になっているため、アプリケーションを Windows ストアに発行する前に無効にする必要があります。


5. 手順 3. および 4. を繰り返し、既存の**読み取り**操作を以下の関数で置き換えます。

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	このスクリプトは、返される TodoItem オブジェクトにフィルター処理を実施して、それぞれのユーザーが自分で挿入した項目のみを受け取るようにするためのものです。

## アプリケーションをテストする

1. Xamarin Studio または Visual Studio で、「[認証の使用]」チュートリアルを実行したときに変更したプロジェクトを開きます。

2. **[Run]** をクリックしてアプリケーションを開始し、選択した ID プロバイダーでサインインします。 

   	このとき、前のチュートリアルで TodoItem テーブルに項目を挿入していても、項目が返されることはない点にご注意ください。このようなことが起こるのは、その項目がユーザー ID 列のない状態で挿入されており、ユーザー ID の値が null になっているためです。

3. そのアプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[保存]** をクリックします。

   	この操作によって、モバイル サービスの TodoItem テーブルにテキストおよびユーザー ID が挿入されます。新しい項目に正しいユーザー ID が設定されたため、モバイル サービスでその項目が返され、2 番目の列に表示されるようになります。

5. [管理ポータル][Azure の管理ポータル] の **TodoItem** テーブルに戻り、**[参照]** をクリックして、新しく追加された項目に対して関連付けられているユーザー ID の値が設定されているかどうかを確認します。

6. (省略可能) ログイン アカウントが他にある場合には、ユーザーがそれぞれ自分のデータのみを閲覧できる状態になっていることを確認できます。これにはまず、アプリケーションを終了して再度実行します。ログイン資格情報の入力を求めるダイアログが表示されたら別のログインを入力し、前のアカウントで入力した項目が表示されないことをご確認ください。

## 次のステップ

これで、認証の基本について説明するチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [データの使用]
  <br/>モバイル サービスを使用してデータの格納およびクエリを実行する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録および使用について説明します。

<!-- Anchors. -->
[サーバー スクリプトを登録する]: #register-scripts
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[マイ アプリ ダッシュ ボード]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-android
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Azure の管理ポータル]: https://manage.windowsazure.com/


<!--HONumber=42-->
