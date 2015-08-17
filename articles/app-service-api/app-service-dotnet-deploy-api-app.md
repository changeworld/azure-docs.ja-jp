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
	ms.date="07/31/2015" 
	ms.author="bradyg"/>

# Azure App Service での API アプリのデプロイ 

## 概要

このチュートリアルでは、[前のチュートリアル](app-service-dotnet-create-api-app.md)で作成した Web API プロジェクトを新しい [API アプリ](app-service-api-apps-why-best-platform.md)にデプロイします。Visual Studio を使用して [Azure App Service](../app-service/app-service-value-prop-what-is.md) で API アプリ リソースを作成し、その Azure API アプリに Web API コードをデプロイします。

### その他のデプロイ オプション

API アプリをデプロイする方法は他にも多数あります。API アプリは、Web サービスをホストするための追加機能を備えた [Web アプリ](../app-service-web/app-service-web-overview.md)で、[Web アプリで利用できるデプロイ方法](../app-service-web/web-sites-deploy.md)はすべて API アプリでも使用できます。API アプリをホストする Web アプリは、Azure プレビュー ポータルでは API アプリ ホストと呼ばれ、[API アプリ ホスト] ポータル ブレードを使用してデプロイを構成できます。[API アプリ ホスト] ブレードの詳細については、[API アプリの管理](app-service-api-manage-in-portal.md)に関するページを参照してください。

API アプリが Web アプリをベースにしているという事実は、ASP.NET 以外のプラットフォーム用に記述されたコードを API アプリにデプロイできることを意味しています。Git を使用して Node.js コードを API アプリにデプロイする例については、[Azure App Service での Node.js API アプリの作成](app-service-api-nodejs-api-app.md)に関するページを参照してください。
 
## <a id="provision"></a>Azure での API アプリの作成 

このセクションでは、Visual Studio の **Web の発行**ウィザードを使用して、Azure の API アプリを作成します。API アプリの名前を入力するよう要求されたら、「*ContactsList*」と入力します。

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>新しい API アプリにコードをデプロイする

同じ **Web の発行**ウィザードを使用して、新しい API アプリにコードをデプロイします。

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Azure プレビュー ポータルでのアプリの表示

このセクションでは、ポータルで API Apps に使用できる基本設定を確認し、API アプリに何度か繰り返し変更を加えます。デプロイするたびに、API アプリに加えた変更がポータルに反映されます。

1. [Azure プレビュー ポータル](https://portal.azure.com)で、先ほどデプロイしたAPI アプリの **API アプリ** ブレードに移動します。

4. **[API の定義]** をクリックします。
 
	アプリの **[API の定義]** ブレードに、アプリを作成したときに定義した API 操作の一覧が表示されます。

	![API の定義](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. ここで Visual Studio のプロジェクトに戻り、**ContactsController.cs** ファイルに次のコードを追加します。

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![コントローラーへの Post メソッドの追加](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

	このコードは、新しい `Contact` インスタンスを API にポストできる **Post** メソッドを追加します。

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
 

<!---HONumber=August15_HO6-->