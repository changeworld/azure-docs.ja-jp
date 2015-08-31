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
	ms.date="08/14/2015" 
	ms.author="tdykstra"/>

# Azure App Service での API アプリのデプロイ 

## 概要

このチュートリアルでは、[前のチュートリアル](app-service-dotnet-create-api-app.md)で作成した Web API プロジェクトを新しい [API アプリ](app-service-api-apps-why-best-platform.md)にデプロイします。Visual Studio を使用して [Azure App Service](../app-service/app-service-value-prop-what-is.md) で API アプリ リソースを作成し、その Azure API アプリに Web API コードをデプロイします。

### その他のデプロイ オプション

API アプリをデプロイする方法は他にも多数あります。API アプリは、Web サービスをホストするための追加機能を備えた [Web アプリ](../app-service-web/app-service-web-overview.md)で、[Web アプリで利用できるデプロイ方法](../app-service-web/web-sites-deploy.md)はすべて API アプリでも使用できます。API アプリをホストする Web アプリは、Azure プレビュー ポータルでは API アプリ ホストと呼ばれ、[API アプリ ホスト] ポータル ブレードを使用してデプロイを構成できます。[API アプリ ホスト] ブレードの詳細については、[API アプリの管理](app-service-api-manage-in-portal.md)に関するページを参照してください。

API アプリが Web アプリをベースにしているという事実は、ASP.NET 以外のプラットフォーム用に記述されたコードを API アプリにデプロイできることを意味しています。Git を使用して Node.js コードを API アプリにデプロイする例については、[Azure App Service での Node.js API アプリの作成](app-service-api-nodejs-api-app.md)に関するページを参照してください。
 
## <a id="provision"></a>Azure での API アプリの作成 

このセクションでは、Visual Studio の **Web の発行**ウィザードを使用して、Azure の API アプリを作成します。API アプリの名前を入力するよう要求されたら、「*ContactsList*」と入力します。

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>新しい Azure API アプリへのコードのデプロイ

同じ **Web の発行**ウィザードを使用して、新しい API アプリにコードをデプロイします。

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Azure API アプリの呼び出し 

前のチュートリアルで Swagger UI を有効にしたので、その Swagger UI を使用して、API アプリが Azure で実行されていることを確認できます。

1. [Azure プレビュー ポータル](https://portal.azure.com)で、先ほどデプロイしたAPI アプリの **API アプリ** ブレードに移動します。

2. API アプリの URL をクリックします。

	![URL をクリック](./media/app-service-dotnet-deploy-api-app/clickurl.png)

	[API アプリが正常に作成されました] ページが表示されます。

3. ブラウザーのアドレス バーで、URL の最後に "/swagger" を追加します。

4. 表示された Swagger ページで、**[Contacts]、[Get]、[Try it Out]** の順にクリックします。

	![実際に使ってみる](./media/app-service-dotnet-deploy-api-app/swaggerui.png)

## ポータルでの API 定義の表示

1. [Azure プレビュー ポータル](https://portal.azure.com)で、先ほどデプロイした API アプリの **API アプリ** ブレードに戻ります。

4. **[API の定義]** をクリックします。
 
	アプリの **[API の定義]** ブレードに、アプリを作成したときに定義した API 操作の一覧が表示されます。

	![API の定義](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

次に、API 定義を変更し、その変更がポータルに反映されていることを確認します。

5. Visual Studio のプロジェクトに戻り、 **ContactsController.cs** ファイルに次のコードを追加します。   

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	このコードは、新しい `Contact` インスタンスを API にポストできる **Post** メソッドを追加します。

	Contacts クラスのコードが次のように表示されます。

		public class ContactsController : ApiController
		{
		    [HttpGet]
		    public IEnumerable<Contact> Get()
		    {
		        return new Contact[]{
		                    new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                    new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                    new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		                };
		    }
		
		    [HttpPost]
		    public HttpResponseMessage Post([FromBody] Contact contact)
		    {
		        // todo: save the contact somewhere
		        return Request.CreateResponse(HttpStatusCode.Created);
		    }
		}

7. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[発行]** を選択します。

9. **[プレビュー]** タブをクリックします。

10. **[プレビューの開始]** をクリックし、どのファイルが Azure にコピーされるかを確認します。

	![[Web の発行] ダイアログ](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. **[発行]** をクリックします。

6. 最初に発行したときのように、ゲートウェイを再起動します。

12. 発行プロセスが完了したら、ポータルに戻り、**[API の定義]** ブレードを閉じてから再び開きます。先ほど作成し、Azure サブスクリプションに直接デプロイした新しい API エンドポイントが表示されます。

	![API の定義](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## 次のステップ

ここまでは、Visual Studio の直接デプロイする機能を使用して、反復処理、迅速なデプロイ、および API が正常に機能しているかどうかのテストを簡単に実行する方法について説明しました。[次のチュートリアル](../app-service-dotnet-remotely-debug-api-app.md)では、Azure で実行中の API アプリをデバッグする方法について説明します。
 

<!---HONumber=August15_HO8-->