7. **ソリューション エクスプローラー**で API アプリ プロジェクトを右クリックして **[発行]** を選択します。発行ダイアログが開きます。先ほど作成した発行プロファイルが選択されているはずです。 

9. **[発行]** をクリックしてデプロイ プロセスを開始します。

	![API アプリのデプロイ](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)

	**[Azure App Service アクティビティ]** ウィンドウにデプロイの進捗状況が表示されます。

	![[Azure App Service アクティビティ] ウィンドウの状態通知](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)

	このデプロイ プロセスの間に、Visual Studio によって*ゲートウェイ*の再起動が自動的に試行されます。ゲートウェイは、リソース グループ内のすべての API アプリの管理機能を処理する Web アプリであり、API アプリの API 定義または *apiapp.json* ファイルにおける変更を認識するには、これを再起動する必要があります。
 
	API アプリを別の方法でデプロイする場合や Visual Studio によるゲートウェイの再起動が失敗する場合は、手動によるゲートウェイの再起動が必要になることがあります。次の手順ではその方法について説明します。

1. ブラウザーで、[Azure プレビュー ポータル](https://portal.azure.com)に移動します。

2. デプロイした API アプリの **API アプリ** ブレードに移動します。

	**API アプリ** ブレードとそれを検索する方法については、「[API アプリの管理](../articles/app-service-api/app-service-api-manage-in-portal.md)」を参照してください。

4. **[ゲートウェイ]** リンクをクリックします。

3. **[ゲートウェイ]** ブレードの **[再起動]** をクリックします。

	![](./media/app-service-api-pub-web-deploy/restartgateway.png)

<!---HONumber=August15_HO6-->