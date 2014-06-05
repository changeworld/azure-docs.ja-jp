

TodoItem テーブル内のデータにアクセスするために認証が必要なため、モバイル サービスによって割り当てられた userID の値を使用して、返されたデータをフィルター処理します。

>[WACOM.NOTE]下に示すすべてのメソッドでは、**RequiresAuthorizationAttribute** が **User** **Authorizationlevel**で適用されている必要があります。これにより、テーブルへのアクセス許可が、認証されたユーザーのみに制限されます。

1. Visual Studio 2013 で、モバイル サービス プロジェクトを開きます。DataObjects フォルダーを展開し、TodoItem.cs プロジェクト ファイルを開きます。

	TodoItem クラスは、データ オブジェクトを定義します。フィルター処理に使用するためには、UserId プロパティを追加する必要があります。

2. 次の新しい UserId プロパティを **TodoItem** クラスに追加します。

		public string UserId { get; set; }

	>[WACOM.NOTE] データベースの既定の初期化子を使用する場合は、Code First のモデル定義内でのデータ モデルの変更が検出されるたびに、Entity Framework がデータベースを削除して再作成します。このようなデータ モデルの変更を行ってデータベース内で既存のデータを保持するには、Code First Migrations を使用する必要があります。Azure 内の SQL データベースに対して、既定の初期化子を使用することはできません。詳細については、[Code First Migrations を使用してデータ モデルを更新する方法に関するページ](/ja-jp/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations)を参照してください。

3. ソリューション エクスプローラーで、Controllers フォルダーを展開し、TodoItemController.cs プロジェクト ファイルを開いて、次の **using** ステートメントを追加します。

		using Microsoft.WindowsAzure.Mobile.Service.Security;

	**TodoItemController** クラスは、TodoItem テーブルに対するデータ アクセスを実装します。
 
4. **PostTodoItem** メソッドを探し、最後の **return** ステートメントの直前に次のコードを追加します。

		// Get the logged-in user.
	    var currentUser = User as ServiceUser;
	
	    // Set the user ID on the item.
	    item.UserId = currentUser.Id;

    このコードは、UserId の値 (認証済みのユーザーの ID) を TodoItem テーブルに挿入する前に、項目に追加するためのものです。
	

5. **GetAllTodoItems** メソッドを見つけ、既存の ** return** ステートメントを次のコード行と置き換えます。

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

   	このクエリは、返される TodoItem オブジェクトにフィルター処理を実施して、それぞれのユーザーが自分で挿入した項目のみを受け取るためのものです。必要に応じて、次の手順を実行します。

6. モバイル サービス プロジェクトを Azure に対して再発行します。

