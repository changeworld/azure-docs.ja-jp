<properties 
	pageTitle="サービス側の承認 (Windows Phone) | モバイル デベロッパー センター" 
	description="Azure Mobile Services の JavaScript バックエンドでユーザーを承認する方法について説明します。" 
	services="" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="glenga"/>

# モバイル サービス ユーザーのサービス側の承認

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>このトピックでは、認証済みのユーザーをサーバー スクリプトで承認し、Azure モバイル サービスのデータに Windows Phone 8 アプリからアクセスできるようにする方法を説明します。このチュートリアルでは、認証済みのユーザーの ID に基づいてクエリにフィルター処理を実施するスクリプトをモバイル サービスに登録します。これによって、それぞれのユーザーが自分のデータのみを閲覧できる状態を実現できます。</p>
<p>このチュートリアルは、モバイル サービスのクイック スタートと、1 つ前の<a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-wp8">認証の使用</a>に関するチュートリアルの内容を前提としています。このため、このチュートリアルの前に、<a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-wp8">認証の使用</a>に関するチュートリアルを完了している必要があります。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="label">チュートリアルを見る</a><a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-scripts-for-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">15:00</span></div>
</div> 

## <a name="register-scripts"></a>スクリプトを登録する
クイック スタート アプリケーションでは、データの読み取りおよび挿入を実行します。このため、TodoItem テーブルにそのような操作を実行するためのスクリプトを登録する必要があります。

1. [Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、アプリケーションをクリックします。 

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

    > [AZURE.NOTE] 挿入スクリプトを初めて実行するときには、動的スキーマを必ず有効にしてください。動的スキーマが有効になっていると、挿入スクリプトを最初に実行した時点で Mobile Services によって **TodoItem** テーブルに **[ユーザー ID]** 列が自動で追加されます。動的スキーマは、新しいモバイル サービスでは既定で有効になっているため、アプリケーションを Windows Phone ストアに発行する前に無効にする必要があります。


5. 手順 3. および 4. を繰り返し、既存の **読み取り** 操作を以下の関数で置き換えます。

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	このスクリプトは、返される TodoItem オブジェクトにフィルター処理を実施して、それぞれのユーザーが自分で挿入した項目のみを受け取るようにするためのものです。

## アプリケーションをテストする

1. Visual Studio 2012 Express for Windows Phone で、[認証の使用] に関するチュートリアルを実行したときに変更したプロジェクトを開きます。

2. F5 キーを押してアプリケーションを実行し、選択した ID プロバイダーでログオンします。 

   	このとき、前のチュートリアルで TodoItem テーブルに項目を挿入していても、項目が返されることはない点に注意してください。このようなことが起こるのは、その項目がユーザー ID 列のない状態で挿入されており、ユーザー ID の値が null になっているためです。

3. そのアプリケーションで、テキスト ボックスにテキストを入力し、**[保存]** をクリックします。

   	![][3]

   	この操作によって、モバイル サービスの TodoItem テーブルにテキストおよびユーザー ID が挿入されます。新しい項目に正しいユーザー ID が設定されたため、モバイル サービスでその項目が返されます。

5. [管理ポータル][Azure の管理ポータル]の **todoitem** テーブルに戻り、**[参照]** をクリックして、新しく追加された項目に対してユーザー ID の値が設定されているかどうかを確認します。

## 次のステップ

これで、認証の基本について説明するチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [データの使用]
  <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

* [モバイル サービスのサーバー スクリプト リファレンス]
  <br/>サーバー スクリプトの登録および使用について説明します。

<!-- Anchors. -->
[サーバー スクリプトを登録する]: #register-scripts
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup-wp8.png

<!-- URLs. -->
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/?LinkId=262293
[マイ アプリ ダッシュ ボード]: http://go.microsoft.com/fwlink/?LinkId=262039
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-wp8
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-wp8
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-wp8

[Azure の管理ポータル]: https://manage.windowsazure.com/



<!--HONumber=42-->
