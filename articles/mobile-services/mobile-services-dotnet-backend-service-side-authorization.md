<properties
	pageTitle=".NET バックエンド モバイル サービスでのユーザーのサービス側承認 | Microsoft Azure"
	description=".NET バックエンド モバイル サービスで権限のあるユーザーのアクセスを制限する方法を学習します。"
	services="mobile-services"
	documentationCenter="windows"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="dotnet"
	ms.date="03/09/2016"
	ms.author="krisragh"/>

# モバイル サービスでのユーザーのサービス側承認
> [AZURE.SELECTOR]
- [.NET バックエンド](mobile-services-dotnet-backend-service-side-authorization.md)
- [JavaScript バックエンド](mobile-services-javascript-backend-service-side-authorization.md)

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> このトピックと同等の Mobile Apps バージョンについては、「Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作」の「[方法: 承認されたユーザーに対するデータ アクセスを制限する](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#authorize)」を参照してください。

このトピックでは、サーバー側ロジックを使用してユーザーを承認する方法を説明します。このチュートリアルでは、テーブル コントローラーを変更して、ユーザー ID に基づいてクエリをフィルター処理し、ユーザーが自分のデータのみにアクセスできるようにします。ユーザー ID によるユーザーのクエリ結果のフィルター処理は、承認の最も基本的な形式です。またシナリオによっては、特定のユーザーのアクセスを許可するエンドポイントなど、より詳細なユーザーの承認情報を確認するために、ユーザーまたはロールのテーブルを作成することもできます。

このチュートリアルは、「Mobile Services クイック スタート」に基づいており、「[既存の Mobile Services アプリケーションに認証を追加]」チュートリアルに基づいて作成されています。最初に「[既存の Mobile Services アプリケーションに認証を追加]」を完了してください。

## <a name="register-scripts"></a>データ アクセス メソッドを変更する

1. Visual Studio で、モバイル プロジェクトを開き、DataObjects　フォルダーを展開して、**TodoItem.cs** を開きます。**TodoItem** クラスは、データ オブジェクトを定義します。フィルター処理に使用するためには、**UserId** プロパティを追加する必要があります。次の新しい UserId プロパティを **TodoItem** クラスに追加します。

		public string UserId { get; set; }

	>[AZURE.NOTE] このようなデータ モデルの変更を行ってデータベース内で既存のデータを保持するには、[Code First Migrations](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md) を使用する必要があります。

2. Visual Studio で、Controllers フォルダーを展開し、**TodoItemController.cs** を開き、ステートメントを使用して次を追加します。

		using Microsoft.WindowsAzure.Mobile.Service.Security;

3. **PostTodoItem** メソッドを見つけ、メソッドの先頭に次のコードを追加します。

		// Get the logged in user
		var currentUser = User as ServiceUser;

		// Set the user ID on the item
		item.UserId = currentUser.Id;

	このコードでは、TodoItem テーブルに挿入される前に、項目に、認証済みユーザーのユーザー ID を追加します。

3. **GetAllTodoItems** メソッドを見つけ、既存の **return** ステートメントを次のコード行と置き換えます。

		// Get the logged in user
		var currentUser = User as ServiceUser;

		return Query().Where(todo => todo.UserId == currentUser.Id);

	このクエリは、返される TodoItem オブジェクトにフィルター処理を実施して、それぞれのユーザーが自分で挿入した項目のみを受け取るためのものです。

4. モバイル サービス プロジェクトを Azure に対して再発行します。


## <a name="test-app"></a>アプリケーションをテストする

1. クライアント側のアプリケーションを実行している場合は、前のチュートリアルでデータベースに項目を挿入していても、項目が返されることはない点に注意してください。これは、ユーザーの ID 列を含まない前の項目が挿入され、現在は null 値であるために発生します。

2. その他のログイン アカウントがある場合は、アプリケーションを終了して削除し、もう一度実行することによって、ユーザーは自分のデータのみ表示できることを確認します。ログインの資格情報ダイアログ ボックスが表示される場合、別のログインを入力し、前のログインで入力した項目が表示されないことを確認します。



<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[既存の Mobile Services アプリケーションに認証を追加]: mobile-services-dotnet-backend-ios-get-started-users.md

<!---HONumber=AcomDC_0316_2016-->