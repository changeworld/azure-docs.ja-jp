<properties 
	pageTitle="サービス側の承認 (Android) | モバイル デベロッパー センター" 
	description="Azure Mobile Services の JavaScript バックエンドでユーザーを承認する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# モバイル サービス ユーザーのサービス側の承認

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	

このトピックでは、認証済みのユーザーをサーバー スクリプトで承認し、Azure のモバイル サービスのデータに Android アプリケーションからアクセスできるようにする方法について説明します。  このチュートリアルでは、認証済みのユーザーの ID に基づいてクエリにフィルター処理を実施するスクリプトをモバイル サービスに登録します。これによって、それぞれのユーザーが自分のデータのみを閲覧できる状態を実現できます。

## 前提条件

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]  

## <a name="register-scripts"></a>スクリプトを登録する
クイック スタート アプリケーションでは、データの読み取りと挿入を実行します。このため、TodoItem テーブルにそのような操作を実行するためのスクリプトを登録する必要があります。

1. [Azure 管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。 

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

    > [AZURE.NOTE] 挿入スクリプトを初めて実行するときには、動的スキーマを必ず有効にしてください。動的スキーマが有効になっていると、挿入スクリプトを最初に実行した時点で Mobile Services によって **TodoItem** テーブルに **[ユーザー ID]** 列が自動で追加されます。動的スキーマは、新しいモバイル サービスでは既定で有効になっているため、アプリケーションを Windows ストアに発行する前に無効にする必要があります。


5. 手順 3. と 4. を繰り返し、既存の**読み取り**操作を以下の関数で置き換えます。

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	このスクリプトは、返される TodoItem オブジェクトにフィルター処理を実施して、それぞれのユーザーが自分で挿入した項目のみを受け取るようにするためのものです。

## アプリケーションをテストする

1. Android Studio で、チュートリアル「[認証の使用]」を実行したときに変更したプロジェクトを開きます。

2. **[実行]** メニューの **[実行]** をクリックしてアプリケーションを再開し、選択した ID プロバイダーでサインインします。 

   	このとき、前のチュートリアルで TodoItem テーブルに項目を挿入していても、項目が返されることはない点に注意してください。このようなことが起こるのは、その項目がユーザー ID 列のない状態で挿入されており、ユーザー ID の値が null になっているためです。

3. そのアプリケーションで、**[Insert a TodoIte]** にテキストを入力し、**[Save]** をクリックします。

   	この操作によって、モバイル サービスの TodoItem テーブルにテキストとユーザー ID が挿入されます。新しい項目に正しいユーザー ID が設定されたため、モバイル サービスでその項目が返され、2 番目の列に表示されるようになります。

5. [管理ポータル][Azure Management Portal]の **todoitem** テーブルに戻り、**[参照]** をクリックして、新しく追加された項目に対してユーザー ID の値が設定されているかどうかを確認します。

6. (省略可能) ログイン アカウントが他にある場合には、ユーザーがそれぞれ自分のデータのみを閲覧できる状態になっていることを確認できます。これにはまず、アプリケーションを終了して再度実行します。ログイン資格情報の入力を求めるダイアログが表示されたら別のログインを入力し、前のアカウントで入力した項目が表示されないことをご確認ください。

## 次のステップ

これで、認証の基本について説明するチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [データの使用]
  <br/>モバイル サービスを使用してデータの格納とクエリを実行する方法について説明します。

* [プッシュ通知の使用] 
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録と使用について説明します。

<!-- Anchors. -->
[サーバー スクリプトを登録]: #register-scripts
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[マイ アプリ ダッシュ ボード]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Mobile Services の使用]: /develop/mobile/tutorials/get-started-android
[データの使用]: /develop/mobile/tutorials/get-started-with-data-android
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-android
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-android

[Azure Management Portal]: https://manage.windowsazure.com/
[Azure 管理ポータル]: https://manage.windowsazure.com/

<!--HONumber=47-->
