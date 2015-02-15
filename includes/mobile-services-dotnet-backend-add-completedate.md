このセクションでは、**CompleteDate** という名前の新しいタイムスタンプ フィールドを追加して、データベースのモデルを変更します。このフィールドには、最後に Todo 項目が完了した時刻が記録されます。Entity Framework は、[DropCreateDatabaseIfModelChanges](http://go.microsoft.com/fwlink/?LinkId=394621) から派生した既定のデータベース初期化子クラスを使用し、このモデルの変更に基づいてデータベースを更新します。 

1. Visual Studio のソリューション エクスプローラーで、todolist サービス プロジェクト内の **[App_Start]** フォルダーを展開します。WebApiConfig.cs ファイルを開きます。

2. WebApiConfig.cs ファイルで、既定のデータベース初期化子クラスが  `DropCreateDatabaseIfModelChanges` クラスから派生していることに注意してください。つまり、モデルへの変更により、テーブルが削除され、新しいモデルを格納するために再作成されることになります。したがって、テーブル内のデータは失われ、テーブルは再シードされます。次のように登録されたデータが WebApiConfig.cs ファイルを保存するように、データベース初期化子の Seed メソッドを変更します。

    >[AZURE.NOTE] データベースの既定の初期化子を使用する場合は、Code First のモデル定義内でのデータ モデルの変更が検出されるたびに、Entity Framework がデータベースを削除して再作成します。このようなデータ モデルの変更を行ってデータベース内で既存のデータを保持するには、Code First Migrations を使用する必要があります。詳細については、[Code First Migrations を使用してデータ モデルを更新する方法に関するページ](/ja-jp/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations)を参照してください。

        List<TodoItem> todoItems = new List<TodoItem>
        {
          new TodoItem { Id = "1", Text = "First seed item", Complete = false },
          new TodoItem { Id = "2", Text = "Second seed item", Complete = false },
        };
     

3. Visual Studio のソリューション エクスプローラーで、todolist サービス プロジェクト内の **[DataObjects]** フォルダーを展開します。TodoItem.cs ファイルを開き、CompleteDate フィールドを次のように追加して TodoItem クラスを更新します。その後、TodoItem.cs ファイルを保存します。

        public class TodoItem : EntityData
        {
          public string Text { get; set; }
          public bool Complete { get; set; }
          public System.DateTime? CompleteDate { get; set; }
        }

4. Visual Studio のソリューション エクスプローラーで、todolist サービス プロジェクト内の **[Controllers]** フォルダーを展開します。TodoItemController.cs ファイルを開き、**Complete** プロパティが false から true に変更されたときに **CompleteDate** が設定されるように、 `PatchTodoItem` メソッドを更新します。その後、TodoItemController.cs ファイルを保存します。

        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            // If complete is being set to true and was false, set CompleteDate
            if ((patch.GetEntity().Complete == true) &&
                (GetTodoItem(id).Queryable.Single().Complete == false))
            {
                patch.TrySetPropertyValue("CompleteDate", System.DateTime.Now);
            }
            return UpdateAsync(id, patch);
        }


5. todolist .NET バックエンド サービス プロジェクトをリビルドし、ビルド エラーがないことを確認します。 

次に、クライアント アプリケーションを更新して新しい **CompleteDate** データを表示します。
<!--HONumber=42-->
