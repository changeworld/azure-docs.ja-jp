

既定で、Mobile App で定義されているエンドポイントは公開されます。リソースを保護するには、認証されたクライアントのみにアクセスを制限する必要があります。

1. Visual Studio で、モバイル アプリ コードを含むプロジェクトを開きます。 

2. ソリューション エクスプローラーで、Controllers フォルダーを展開し、TodoItemController.cs プロジェクト ファイルを開きます。

	**TodoItemController** クラスは、TodoItem テーブルに対するデータ アクセスを実装します。

3. `Authorize` 属性を **TodoItemController** クラスに適用します。

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

	これにより、**TodoItem** テーブルに対するすべての操作には、認証されたユーザーが必要になります。

	>[AZURE.NOTE]コント ローラーによって公開されるメソッドに対して特定の認証レベルを設定するには、個々のメソッドに Authorize 属性を適用します。

4. モバイル アプリ プロジェクトを再発行します。

<!---HONumber=August15_HO6-->