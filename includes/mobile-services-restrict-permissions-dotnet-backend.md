

既定では、モバイル サービスのリソースに対するすべての要求を実行するには、クライアントがアプリケーション キーを提出する必要があるという制限が加えられていますが、この方法ではリソースに対するアクセスが厳格に保護されるわけではありません。リソースを保護するには、認証されたクライアントのみにアクセスを制限する必要があります。

1. Visual Studio で、モバイル サービスを含むプロジェクトを開きます。 

2. ソリューション エクスプローラーで、Controllers フォルダーを展開し、TodoItemController.cs プロジェクト ファイルを開きます。

	**TodoItemController** クラスは、TodoItem テーブルに対するデータ アクセスを実装します。 

3. コード ページの先頭に次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.Mobile.Service.Security;

4. 次の AuthorizeLevel 属性を **TodoItemController** クラスに適用します。

		[AuthorizeLevel(AuthorizationLevel.User)] 

	これにより、**TodoItem** テーブルに対するすべての操作には、認証されたユーザーが必要になります。 

	>[AZURE.NOTE]コント ローラーによって公開されるメソッドに対して特定の認証レベルを設定するには、個々のメソッドに AuthorizeLevel 属性を適用します。

5. ローカルで認証をデバッグする場合は、App_Start フォルダーを展開し、WebApiConfig.cs プロジェクト ファイルを開いてから、次のコードを **Register** メソッドに追加します。

		config.SetIsHosted(true);
	
	この作業により、ローカルのモバイル サービス プロジェクトに対して、AuthorizeLevel 設定に従うことも含め、まるで Azure でホストされているかのように動作することを指示します。この設定がない場合は、AuthorizeLevel の設定にかかわりなく、*localhost* に対するすべての HTTP 要求は認証なしで許可されます。  

6. サービス プロジェクトを再発行します。


<!--HONumber=42-->
