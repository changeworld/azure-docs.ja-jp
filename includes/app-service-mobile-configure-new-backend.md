
1. モバイル アプリのバックエンドに戻り、**[設定]**、**[クイック スタート]** の順にクリックし、目的のクライアント プラットフォームをクリックします。 

2. **[Create a table API]** (テーブル API を作成する) で、**[Backend language]** (バックエンド言語) として [C#] または [Node.js] を選択します。

	+ **Node.js バックエンド**: 確認要求をそのまま受け入れ、**[TodoItem テーブルを作成する]** をクリックします。新しい *TodoItem* テーブルがデータベースに作成されます。既存のバックエンドを Node.js に切り替えると、すべてのコンテンツが上書きされるので注意してください。

	+ **C# (.NET バックエンド)**:
		1. **[ダウンロード]** をクリックします。
		2. 抽出したソリューションを Visual Studio で開きます。
		3. プロジェクトをビルドして、NuGet パッケージを復元します。 
		4. プロジェクトを Azure にデプロイします。 
	
		C# プロジェクトを Azure にデプロイする詳細な手順については、「[How to: Publish Backend Project with Visual Studio (方法: Visual Studio でバックエンド プロジェクトを発行する)](../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#publish-server-project)」を参照してください。

