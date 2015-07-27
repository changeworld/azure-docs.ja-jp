3. **ソリューション エクスプローラー**で、プロジェクト (ソリューションではありません) を右クリックします。**[追加]、[Azure API アプリ クライアント]** の順にクリックします。 

	![](./media/app-service-api-dotnet-add-generated-client/03-add-azure-api-client-v3.png)
	
3. **[Azure API アプリ クライアントの追加]** ダイアログ ボックスで、**[Azure API アプリからダウンロード]** をクリックします。

5. ドロップダウン リストから、呼び出す API アプリを選択します。

7. **[OK]** をクリックします。

	![生成画面](./media/app-service-api-dotnet-add-generated-client/04-select-the-api-v3.png)

	ウィザードによって、API メタデータ ファイルがダウンロードされ、API アプリを呼び出すための型指定されたインターフェイスが生成されます。

	![生成中を示す画面](./media/app-service-api-dotnet-add-generated-client/05-metadata-downloading-v3.png)

	コードの生成が完了すると、API アプリの名前が付いた新しいフォルダーが**ソリューション エクスプローラー**に表示されます。このフォルダーには、クライアント クラスとデータ モデルを実装するコードが含まれています。

	![生成完了画面](./media/app-service-api-dotnet-add-generated-client/06-code-gen-output-v3.png)

<!---HONumber=July15_HO3-->