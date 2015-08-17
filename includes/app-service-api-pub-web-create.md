1. **ソリューション エクスプローラー**で、プロジェクト (ソリューションではありません) を右クリックしてから、**[発行]** をクリックします。 

	![プロジェクトの [発行] メニュー オプション](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. **[プロファイル]** タブ、**[Microsoft Azure API Apps (プレビュー)]** の順にクリックします。

	![[Web の発行] ダイアログ](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. **[新規]** をクリックして、Azure サブスクリプションに新しい API アプリをプロビジョニングします。

	![既存の API サービス選択ダイアログ](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. **[API アプリの作成]** ダイアログで、次のように入力します。

	- **[API アプリの名前]** には、このチュートリアルで使用する名前を入力します。 
	- 複数の Azure サブスクリプションがある場合は、使用するサブスクリプションを選択します。
	- **[App Service プラン]** で、既存の App Service プランを選択するか、**[App Service プランの新規作成]** を選択して新しいプランの名前を入力します。 
	- **[リソース グループ]** で、既存のリソース グループを選択するか、**[新しいリソース グループの作成]** を選択して名前を入力します。 
	- **[アクセス レベル]** で、**[すべてのユーザーが利用できる]** を選択します。Azure プレビュー ポータルを使用して後でアクセスを制限することができます。
	- **[リージョン]** で、近くのリージョンを選択します。  

	![Microsoft Azure Web アプリの構成ダイアログ](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. **[OK]** をクリックします。サブスクリプションに API アプリが作成されます。

	このプロセスには数分かかることがあるため、Visual Studio に確認ダイアログ ボックスが表示されます。

	![API サービスの作成の開始の確認メッセージ](./media/app-service-api-pub-web-create/25-api-provisioning-started-v3.png)

6. [確認] ダイアログで **[OK]** をクリックします。
 
	プロビジョニング プロセスにより、Azure サブスクリプションにリソース グループと API アプリが作成されます。Visual Studio の **[Azure App Service アクティビティ]** ウィンドウに進捗状況が表示されます。

	![[Azure App Service アクティビティ] ウィンドウによる状態の通知](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

<!---HONumber=August15_HO6-->