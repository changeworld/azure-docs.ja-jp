<properties
	pageTitle="JavaScript バックエンドを使用したモバイル サービスでのユーザーのサービス側承認 | モバイル デベロッパー センター"
	description="Azure のモバイル サービスの JavaScript バックエンドでユーザーを承認する方法を学習します。"
	services="mobile-services"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.topic="article"
	ms.date="2/18/2015"
	ms.author="krisragh"/>

# モバイル サービスでのユーザーのサービス側承認

> [AZURE.SELECTOR-LIST (プラットフォーム | バックエンド)]
- [(任意 | .NET)](/ja-jp/documentation/articles/mobile-services-dotnet-backend-service-side-authorization/)
- [(任意 | Javascript)](/ja-jp/documentation/articles/mobile-services-javascript-backend-service-side-authorization/)

このトピックでは、サーバー側スクリプトを使用してユーザーを承認する方法を示します。このチュートリアルでは、Azure モバイル サービスでスクリプトを登録し、ユーザー ID に基づいてクエリをフィルターし、ユーザーに自分のデータのみへのアクセスを提供します。

このチュートリアルは、モバイル サービス クイック スタートに基づいており、「[既存のモバイル サービス アプリケーションに認証を追加]」チュートリアルに基づいて作成されています。最初に「[既存のモバイル サービス アプリケーションに認証を追加]」を完了してください。

## <a name="register-scripts"></a>スクリプトを登録する

1. [[Azure 管理ポータル]]にログオンし、[**モバイル サービス**]をクリックして、お使いのモバイル サービスをクリックします。**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

2. **[スクリプト]** をクリックし、**[挿入]** 操作を選択します。

   	![][2]

3. 既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。このスクリプトは、挿入前に項目に認証済みユーザーのユーザー ID を追加します。

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }


    > [AZURE.NOTE] これを機能させるには[動的スキーマを有効にする必要があります](https://msdn.microsoft.com/library/azure/jj193175.aspx)。新しいモバイル サービスの既定ではこの設定は有効です。

5. 同様に、既存の**読み取り**操作を次の関数で置き換えます。このスクリプトは、ユーザーが自身に挿入した項目だけを受信できるように、返された TodoItem オブジェクトをフィルターします。

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }




## <a name="test-app"></a>アプリケーションをテストする

1. 前のチュートリアルの _TodoItem_ テーブルに既に項目がある場合にクライアント側のアプリケーションを実行すると、アイテムが返されないことに注意してください。これは、ユーザーの ID 列を含まない前の項目が挿入され、現在は null 値であるために発生します。新しく追加された項目が、_TodoItem_ テーブル内に関連付けられている userId 値を持っていることを確認します。

2. その他のログイン アカウントがある場合は、アプリケーションを終了して削除し、もう一度実行することによって、ユーザーは自分のデータのみ表示できることを確認します。ログインの資格情報ダイアログ ボックスが表示される場合、別のログインを入力し、前のログインで入力した項目が表示されないことを確認します。

<!-- Anchors. -->
[サーバー スクリプトを登録]: #register-scripts
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->

[Windows プッシュ通知および Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[モバイル サービスのサーバー スクリプト リファレンス]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[マイ アプリ ダッシュ ボード]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started/#create-new-service
[既存のアプリにモバイル サービスを追加]: /ja-jp/develop/mobile/tutorials/get-started-with-data-ios
[既存のモバイル サービス アプリケーションに認証を追加]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios
[既存のアプリにプッシュ通知を追加]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios

[Azure 管理ポータル]: https://manage.windowsazure.com/

<!--HONumber=45--> 
