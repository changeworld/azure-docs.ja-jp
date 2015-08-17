<properties
	pageTitle="JavaScript バックエンド モバイル サービスでのユーザーのサービス側承認 | Azure Mobile Services"
	description="Azure モバイル サービスの JavaScript バックエンドでユーザーを承認する方法を学習します。"
	services="mobile-services"
	documentationCenter=""
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="javascript"
	ms.date="05/20/2015"
	ms.author="krisragh"/>

# モバイル サービスでのユーザーのサービス側承認

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

このトピックでは、サーバー側スクリプトを使用してユーザーを承認する方法を示します。このチュートリアルでは、Azure モバイル サービスでスクリプトを登録し、ユーザー ID に基づいてクエリをフィルターし、ユーザーに自分のデータのみへのアクセスを提供します。ユーザー ID によるユーザーのクエリ結果のフィルター処理は、承認の最も基本的な形式です。またシナリオによっては、特定のユーザーのアクセスを許可するエンドポイントなど、より詳細なユーザーの承認情報を確認するために、ユーザーまたはロールのテーブルを作成することもできます。

このチュートリアルは、「Mobile Services クイック スタート」に基づいており、「[既存の Mobile Services アプリケーションに認証を追加]」チュートリアルに基づいて作成されています。最初に「[既存の Mobile Services アプリケーションに認証を追加]」を完了してください。

## <a name="register-scripts"></a>スクリプトを登録する

1. [[Azure 管理ポータル]]にログオンし、[**Mobile Services**]をクリックして、お使いのモバイル サービスをクリックします。**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

2. **[スクリプト]** をクリックし、**挿入**操作を選択して、既存のスクリプトを次の関数に置き換え、**[保存]** をクリックします。

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }

	このスクリプトは、挿入前に項目に認証済みユーザーのユーザー ID を追加します。

    >[AZURE.NOTE][動的スキーマ](https://msdn.microsoft.com/library/azure/jj193175.aspx) が有効であることを確認します。それ以外の場合、*userId* 列は自動的には追加されません。新しいモバイル サービスの既定ではこの設定は有効になります。

3. 同様に、既存の**読み取り**操作を次の関数で置き換えます。このスクリプトは、ユーザーが自身に挿入した項目だけを受信できるように、返された TodoItem オブジェクトをフィルターします。

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }

## <a name="test-app"></a>アプリケーションをテストする

1. 前のチュートリアルの _TodoItem_ テーブルに既に項目がある場合にクライアント側のアプリケーションを実行すると、アイテムが返されないことに注意してください。これは、ユーザーの ID 列を含まない前の項目が挿入され、現在は null 値であるために発生します。新しく追加された項目が、_TodoItem_ テーブル内に関連付けられている userId 値を持っていることを確認します。

2. その他のログイン アカウントがある場合は、アプリケーションを終了して削除し、もう一度実行することによって、ユーザーは自分のデータのみ表示できることを確認します。ログインの資格情報ダイアログ ボックスが表示される場合、別のログインを入力し、前のログインで入力した項目が表示されないことを確認します。

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->

[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[既存の Mobile Services アプリケーションに認証を追加]: /develop/mobile/tutorials/get-started-with-users-ios

[Azure 管理ポータル]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO6-->