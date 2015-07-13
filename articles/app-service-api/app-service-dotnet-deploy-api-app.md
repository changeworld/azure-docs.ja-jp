<properties 
	pageTitle="Azure App Service での API アプリのデプロイ" 
	description="Azure サブスクリプションに API アプリ プロジェクトをデプロイする方法について説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Azure App Service での API アプリのデプロイ 

## 概要

このチュートリアルでは、[前のチュートリアル](app-service-dotnet-create-api-app.md)で作成した Web API プロジェクトを新しい [API アプリ](app-service-api-apps-why-best-platform.md)にデプロイします。Visual Studio を使用して [Azure App Service](../app-service/app-service-value-prop-what-is.md) で API アプリ リソースを作成し、その Azure API アプリに Web API コードをデプロイします。

### その他のデプロイ オプション

API アプリをデプロイする方法は他にも多数あります。API アプリは、Web サービスをホストするための追加機能を備えた [Web アプリ](../app-service-web/app-service-web-overview.md)で、[Web アプリで利用できるデプロイ方法](../app-service-web/web-sites-deploy.md)はすべて API アプリでも使用できます。API アプリをホストする Web アプリは、Azure プレビュー ポータルでは API アプリ ホストと呼ばれ、[API アプリ ホスト] ポータル ブレードを使用してデプロイを構成できます。[API アプリ ホスト] ブレードの詳細については、[API アプリの管理](app-service-api-manage-in-portal.md)に関するページを参照してください。

API アプリが Web アプリをベースにしているという事実は、ASP.NET 以外のプラットフォーム用に記述されたコードを API アプリにデプロイできることを意味しています。Git を使用して Node.js コードを API アプリにデプロイする例については、[Azure App Service での Node.js API アプリの作成](app-service-api-nodejs-api-app.md)に関するページを参照してください。
 
## <a id="provision"></a>Azure での API アプリの作成 

このセクションでは、Visual Studio の **Web の発行**ウィザードを使用して、Azure の新しい API アプリを作成します。

1. **ソリューション エクスプローラー**で、プロジェクト (ソリューションではありません) を右クリックしてから、**[発行]** をクリックします。 

	![プロジェクトの [発行] メニュー オプション](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

2. **[プロファイル]** タブ、**[Microsoft Azure API Apps (プレビュー)]** の順にクリックします。

	![[Web の発行] ダイアログ](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

3. **[新規]** をクリックして、Azure サブスクリプションに新しい API アプリをプロビジョニングします。

	![既存の API サービス選択ダイアログ](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

4. **[API アプリの作成]** ダイアログで、次のように入力します。

	- **[API アプリの名前]** に「ContactsList」と入力します。 
	- 複数の Azure サブスクリプションがある場合は、使用するサブスクリプションを選択します。
	- **[App Service プラン]** で、既存の App Service プランを選択するか、**[App Service プランの新規作成]** を選択して新しいプランの名前を入力します。 
	- **[リソース グループ]** で、既存のリソース グループを選択するか、**[新しいリソース グループの作成]** を選択して名前を入力します。名前は一意にする必要があります。アプリ名をプレフィックスとして使用し、@ 記号を除いた Microsoft ID などの個人情報を追加することを検討してください。  
	- **[アクセス レベル]** で、**[すべてのユーザーが利用できる]** を選択します。これは API を完全にパブリックにするオプションですが、このチュートリアルでは問題ありません。Azure プレビュー ポータルを使用して後でアクセスを制限することができます。
	- **[リージョン]** で、近くのリージョンを選択します。  

	![Microsoft Azure Web アプリの構成ダイアログ](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

5. **[OK]** をクリックします。サブスクリプションに API アプリが作成されます。このプロセスには数分かかることがあるため、Visual Studio に確認ダイアログ ボックスが表示されます。

	![API サービスの作成の開始の確認メッセージ](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

6. [確認] ダイアログで **[OK]** をクリックします。プロビジョニング プロセスにより、Azure サブスクリプションにリソース グループと API アプリが作成されます。Visual Studio の **[Azure App Service アクティビティ]** ウィンドウに進捗状況が表示されます。

	![[Azure App Service アクティビティ] ウィンドウによる状態の通知](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

## <a id="deploy"></a>新しい API アプリにコードをデプロイする

このセクションでは、同じ **Web の発行**ウィザードを使用して、コードをデプロイします。

7. **ソリューション エクスプローラー**でプロジェクトを右クリックして **[発行]** を選択します。発行ダイアログが再び開きます。前の手順で作成した発行プロファイルが選択されます。**[発行]** をクリックしてデプロイ プロセスを開始します。 

	![API アプリのデプロイ](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

	**[Azure App Service アクティビティ]** ウィンドウにデプロイの進捗状況が表示されます。

	![[Azure App Service アクティビティ] ウィンドウの状態通知](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

	このデプロイ プロセスの間に、Visual Studio によって*ゲートウェイ*が自動的に再起動されます。ゲートウェイは、リソース グループ内のすべての API アプリの管理機能を処理する Web アプリであり、API アプリにおける API の変更を認識するには、これを再起動する必要があります。API アプリを別の方法でデプロイする場合やデプロイによって API が変更される場合は、手動によるゲートウェイの再起動が必要になることがあります。ポータルでゲートウェイを再起動する方法については、「[API アプリの管理](app-service-api-manage-in-portal.md)」を参照してください。

## Azure プレビュー ポータルでのアプリの表示

このセクションでは、ポータルに移動して、API Apps の使用可能な基本設定を表示し、API アプリに反復的な変更を加えます。デプロイするたびに、API アプリに加えた変更がポータルに反映されます。

1. ブラウザーで、[Azure プレビュー ポータル](https://portal.azure.com)に移動します。 

2. サイド バーで **[参照]** をクリックし、**[API Apps]** を選択します。

	![Azure ポータルの [参照] のオプション](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

3. サブスクリプションの API アプリの一覧から作成した API を選択します。

	![API Apps の一覧](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

4. **[API の定義]** をクリックします。アプリの **[API の定義]** ブレードに、アプリを作成したときに定義した API 操作の一覧が表示されます。

	![API の定義](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. ここで Visual Studio のプロジェクトに戻り、**ContactsController.cs** ファイルに次のコードを追加します。このコードは、新しい `Contact` インスタンスを API にポストできる **Post** メソッドを追加します。

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![コントローラーへの Post メソッドの追加](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

6. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[発行]** を選択します。

	![プロジェクトの [発行] コンテキスト メニュー](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

7. **[設定]** タブをクリックします。

8. **[構成]** ボックスの一覧で、**[デバッグ]** をクリックします。

	![[Web の発行] の設定](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

9. **[プレビュー]** タブをクリックします。

10. **[プレビュー開始]** をクリックして、適用される変更を表示します。

	![[Web の発行] ダイアログ](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. **[発行]** をクリックします。

12. 発行プロセスが完了したら、ポータルに戻り、**[API の定義]** ブレードを閉じてから再び開きます。先ほど作成し、Azure サブスクリプションに直接デプロイした新しい API エンドポイントが表示されます。

	![API の定義](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## 次のステップ

ここまでは、Visual Studio の直接デプロイする機能を使用して、反復処理、迅速なデプロイ、および API が正常に機能しているかどうかのテストを簡単に実行する方法について説明しました。[次のチュートリアル](../app-service-dotnet-remotely-debug-api-app.md)では、Azure で実行中の API アプリをデバッグする方法について説明します。
 

<!---HONumber=62-->