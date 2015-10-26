
既定では、モバイル アプリ バックエンドの API は匿名で呼び出すことができます。次に、認証されたクライアントのみにアクセスを制限する必要があります。

1. お使いの PC の Visual Studio で、サーバー プロジェクトを開き、**[Controllers]**、**[TodoItemController.cs]** の順に移動します。

2. 次のように、`[Authorize]` 属性を **TodoItemController** クラスに追加します。これにより、TodoItem テーブルに対するすべての操作には、認証されたユーザーが必要になります。アクセスを特定のメソッドのみに制限するには、この属性を、クラスではなく、そのメソッドのみに適用するだけです。


        [Authorize]
        public class TodoItemController : TableController<TodoItem>
   
    これにより、TodoItem テーブルに対するすべての操作には、認証されたユーザーが必要になります。アクセスを特定のメソッドのみに制限するには、この属性を、クラスではなく、そのメソッドのみに適用するだけです。
   
3. サーバー プロジェクトを再発行します。

<!---HONumber=Oct15_HO3-->