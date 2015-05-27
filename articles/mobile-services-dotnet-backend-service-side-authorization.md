<properties
	pageTitle=".NET バックエンドを使用したモバイル サービス内のユーザーのサービス側の承認 | モバイル デベロッパー センター"
	description="Azure Mobile Services の .NET バックエンドでユーザーを承認する方法について説明します。"
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

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

このトピックでは、サーバー側ロジックを使用してユーザーを承認する方法を説明します。このチュートリアルでは、.NET でデータ アクセス メソッドを変更して、ユーザー ID に基づいてクエリをフィルター処理し、ユーザーが自分のデータのみにアクセスできるようにします。

このチュートリアルは、「Mobile Services クイック スタート」に基づいており、「[既存の Mobile Services アプリケーションに認証を追加]」チュートリアルに基づいて作成されています。最初に「[既存の Mobile Services アプリケーションに認証を追加]」を完了してください。

## <a name="register-scripts"></a>データ アクセス メソッドを変更する

1. Visual Studio で、モバイル プロジェクトを開き、DataObjects　フォルダーを展開して、**TodoItem.cs** を開きます。**TodoItem** クラスは、データ オブジェクトを定義します。フィルター処理に使用するためには、**UserId** プロパティを追加する必要があります。次の新しい UserId プロパティを **TodoItem** クラスに追加します。

		public string UserId { get; set; }

	>[AZURE.NOTE]このようなデータ モデルの変更を行ってデータベース内で既存のデータを保持するには、[Code First Migrations](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md) を使用する必要があります。

2. Visual Studio で、Controllers フォルダーを展開し、**TodoItemController.cs** を開きます。**PostTodoItem** メソッドを見つけ、メソッドの先頭に次のコードを追加します。このコードでは、TodoItem テーブルに挿入される前に、項目に、認証済みユーザーのユーザー ID を追加します。

			// Get the logged in user
			var currentUser = User as ServiceUser;

			// Set the user ID on the item
			item.UserId = currentUser.Id;

3. **GetAllTodoItems** メソッドを見つけ、既存の **return** ステートメントを次のコード行と置き換えます。このクエリは、返される TodoItem オブジェクトにフィルター処理を実施して、それぞれのユーザーが自分で挿入した項目のみを受け取るためのものです。

				// Get the logged in user
				var currentUser = User as ServiceUser;

				return Query().Where(todo => todo.UserId == currentUser.Id);

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
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with data]: mobile-services-dotnet-backend-ios-get-started-data.md
[既存の Mobile Services アプリケーションに認証を追加]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md

[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!--HONumber=54-->