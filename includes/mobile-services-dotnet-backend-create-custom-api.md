

1. Visual Studio のソリューション エクスプローラーで、モバイル サービス プロジェクトの Controllers フォルダーを右クリックし、**[追加]** を展開して **[新しいスキャフォールディング項目]** をクリックします。

	[スキャフォールディングの追加] ダイアログ ボックスが表示されます。

2. **[Azure モバイル サービス]** を展開し、**[Azure モバイル サービス カスタム コントローラー]** をクリックします。**[追加]** をクリックし、 `CompleteAllController` で **[コントローラー名]** を指定した後、**[追加]** をもう一度クリックします。

	![Web API Add Scaffold dialog](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	**CompleteAllController** という名前の新しいからのコントローラー クラスが作成されます。

>[AZURE.NOTE]ダイアログに Mobile Services 固有のスキャフォールディングがない場合は、代わりに新しい **Web API コントローラー - 空**を作成します。この新しいコントローラー クラスで、**ApiServices** タイプを返すパブリックの **Services** プロパティを追加します。このプロパティを使用して、コントローラー内からサーバー固有の設定にアクセスできます。

3. 新しい CompleteAllController.cs プロジェクト ファイルに、次の **using** ステートメントを追加します。

		using System.Threading.Tasks;
		using todolistService.Models;

	上のコードで、 `todolistService` をモバイル サービス プロジェクトの名前空間 (モバイル サービス名の後に  `Service` が付いた名前) と置き換えます。 

4. CompleteAllController.cs で、名前空間に次のクラス定義を追加します。このクラスは、クライアントに送信された応答をラップします。

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public Int32 count;
        }


5. 新しいコントローラーに、次のコードを追加します。

	    // POST api/completeall        
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolistService.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                    result.count.ToString()));
                
                return result;
            }
        }

	上のコードで、 `todolistContext` をデータ モデルの DbContext の名前 (モバイル サービス名の後に  `Context` が付いた名前) と置き換えます。また UPDATE ステートメント内のスキーマ名をモバイル サービス名と置き換えます。 

	このコードは [Database クラス](http://msdn.microsoft.com/ja-jp/library/system.data.entity.database.aspx) を使用して **TodoItems** テーブルに直接アクセスし、すべての項目の完了フラグを設定します。このメソッドは POST 要求をサポートし、変更された行数が整数値としてクライアントに返されます。

	> [AZURE.NOTE] 既定のアクセス許可が設定されるため、アプリケーションのユーザーすべてがこのカスタム API を呼び出すことができます。ただし、アプリケーション キーはセキュリティの保護がない状態で配布または保存されるため、安全な資格情報として扱うことはできません。このため、データの変更またはモバイル サービスに影響する操作を、認証されたユーザーのみのアクセスに制限することを考慮する必要があります。 

次に、quickstart アプリケーションを変更し、新しいボタンと、非同期的に新しいカスタム API を呼び出すコードを追加します。

<!--HONumber=42-->
