<properties 
	pageTitle="認証されたクライアントからの API アプリの呼び出し" 
	description="Azure Active Directory で認証された Web アプリ クライアントから Azure API アプリを呼び出す方法について説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2015" 
	ms.author="tdykstra"/>

# Azure Active Directory で認証された Web アプリ クライアントからの Azure API アプリの呼び出し

## 概要

このチュートリアルでは、Azure Active Directory (AAD) によって保護されている API アプリを、同じく AAD によって保護されている Web アプリから呼び出す方法について説明します。このチュートリアルを完了すると、Web アプリと API アプリが Azure サブスクリプションで実行されます。次のスクリーン ショットに示すように、Web アプリには、API アプリを呼び出すことで取得するデータが表示されます。

![](./media/app-service-api-authentication-client-flow/aboutpage.png)

ここでは、ユーザーが API アプリでの再ログインを求められないように、AAD 資格情報を Web アプリから API アプリに渡す方法について説明します。

以下の手順を実行します。

- API アプリを作成し、AAD 認証を使用するように構成します。
- Web アプリを作成し、AAD 認証を使用するように構成します。
- 保護されている API アプリを呼び出すためのコードを Web アプリに追加します。
- Web アプリと API アプリの両方を Azure にデプロイします。
- テストを実行して、Web アプリが API アプリを呼び出せることを確認します。

### クライアント フローの認証

Web アプリに追加するコードは、[クライアント フロー](../app-service/app-service-authentication-overview.md#client-flow)認証と呼ばれるプロセスを実装します。次の図は、AAD アクセス トークンを取得して、そのトークンを API アプリ (Zumo) トークンと交換するプロセスを示しています。

![](./media/app-service-api-authentication-client-flow/clientflow.png)

API アプリの認証における API アプリのゲートウェイのロールに慣れていない場合は、「[API Apps と Mobile Apps の認証](../app-service/app-service-authentication-overview.md)」を参照してください。

## 前提条件

このチュートリアルを開始する前に、[Visual Studio 2015](https://www.visualstudio.com/) と [Azure SDK for .NET](http://go.microsoft.com/fwlink/?linkid=518003) がインストールされていることを確認してください。この手順は、Visual Studio 2013 でも有効です。

このチュートリアルは、Visual Studio で Web プロジェクトを操作する方法を理解していることを前提としています。

## AAD での API アプリの作成とセキュリティ保護

1. API アプリ プロジェクトを作成またはダウンロードします。
 
	* プロジェクトを作成するには、「[API アプリの作成](app-service-dotnet-create-api-app.md)」の手順に従います。

	* プロジェクトをダウンロードするには、[ContactsList GitHub リポジトリ](https://github.com/tdykstra/ContactsList)から、そのプロジェクトを取得します。

	これで、連絡先データを返す Web API プロジェクトを利用できます。

	![](./media/app-service-api-authentication-client-flow/swaggerlocal.png)

2. Azure で、新しい API アプリに API アプリ プロジェクトをデプロイします。

	「[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)」の手順に従って操作します。

	[Azure プレビュー ポータル]の API アプリの **[API の定義]** ブレードに、デプロイした Web API プロジェクトの Get メソッドと Post メソッドが表示されます。

	![](./media/app-service-api-authentication-client-flow/apiappinportal.png)

4. Azure Active Directory (AAD) を ID プロバイダーとして使用して、API アプリをセキュリティで保護します。
 
	「[API アプリの保護](app-service-api-dotnet-add-authentication.md)」の AAD に関する手順に従って操作します。チュートリアルの「**Postman を使用した Post 要求の送信**」セクションの操作を行う必要はありません。

	終了したら、[Azure プレビュー ポータル]の API アプリの **[Azure Active Directory]** ブレードに、AAD アプリケーションのクライアント ID と AAD テナントが表示されます。

	![](./media/app-service-api-authentication-client-flow/aadblade.png)

	[Azure ポータル]の AAD アプリケーションの **[構成]** タブには、API アプリのアプリ ID URL と応答 URL が表示されます。

	![](./media/app-service-api-authentication-client-flow/aadconfig.png)

## AAD での Web アプリの作成とセキュリティ保護

このセクションでは、AAD 認証用に設定されている Web プロジェクトをダウンロードして構成します。プロジェクトの **[About]** ページには、ログオン ユーザーの信頼性情報が表示されます。

![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

このページには、API アプリから取得した連絡先情報を表示するセクションを後で追加します。

1. Web プロジェクトを [WebApp-GroupClaims-DotNet リポジトリ](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)からダウンロードします。
 
2. [Readme ファイル](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md)の「**How to run the sample (サンプルの実行方法)**」の手順に従います。これには、次の例外があります。
 
	* Readme ファイルでは Visual Studio 2013 を使用するよう指示していますが、Visual Studio 2015 を使用できます。 

	* 新しい ADD アプリケーションを作成するのではなく、作成済みの AAD アプリケーションを使用します。
 
	* **アプリ ID URI** は、既存の AAD アプリケーションの URI と同じものをそのまま使用します (Readme ファイルで指定された形式に変更しないでください)。
	
	* その他の AAD アプリケーション設定を指示どおりに変更します。特に、サインオン URL と応答 URL をサンプル アプリのベース URL に設定します。

Web アプリと API アプリの両方が同じ AAD アクセス トークンを使用できるように、API アプリ用に作成したアプリ ID URI をそのまま使用します。readme で規定された形式にアプリ ID URI を変更すると、その URI で Web アプリにアクセスすることはできますが、API アプリにはアクセスできません。その AAD トークンを API アプリのゲートウェイに渡して Zumo トークンを取得することはできません。ゲートウェイは、ゲートウェイ URL と "login/aad" で構成されているアプリ ID URI のトークンを想定しているからです。

## API アプリ用に生成されたクライアント コードの追加

このセクションでは、API アプリを呼び出すための型指定されたインターフェイスに対して、自動生成されたコードを追加します。

8.	Visual Studio の**ソリューション エクスプ ローラー**で、WebApp-GroupClaims-DotNet プロジェクトを右クリックし、**[追加]、[Azure API アプリ クライアント]** の順にクリックします。

9.	**[Microsoft Azure API アプリ クライアントの追加]** ダイアログ ボックスで、AAD を使用してセキュリティ保護した API アプリを選択します。

	コードの生成が完了すると、API アプリの名前が付いた新しいフォルダーが**ソリューション エクスプローラー**に表示されます。このフォルダーには、クライアント クラスとデータ モデルを実装するコードが含まれています。

10. *ContactsList/ContactsExtensions.cs* で、生成されたコードによるあいまいな参照を修正します。`Task.Factory.StartNew` の2 つのインスタンスを `System.Threading.Tasks.Task.Factory.StartNew` に変更します。
 
## AAD トークンを Zumo トークンと交換するためのコードを追加します。

1.	HomeController.cs で、App Service SDK と JSON シリアライザー用の `using` ステートメントを追加します。

		using Microsoft.Azure.AppService;
		using Newtonsoft.Json.Linq;

2. AAD アプリケーションのゲートウェイ URL とアプリ ID URI の定数を追加します。ゲートウェイ URL が http ではなく、https であることを確認します。

		private const string GATEWAY_URL = "https://clientflowgroupaeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string APP_ID_URI = GATEWAY_URL + "login/aad";

2.	API アプリを呼び出すためにクライアント オブジェクトを取得するメソッドを追加します。

		public async Task<AppServiceClient> GetAppServiceClient()
		{
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    string userObjectID = ClaimsPrincipal.Current.FindFirst
		        ("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		
		    var authContext = new AuthenticationContext
		        (ConfigHelper.Authority, new TokenDbCache(userObjectID));
		
		    ClientCredential credential = new ClientCredential
		        (ConfigHelper.ClientId, ConfigHelper.AppKey);
		
		    // Get the AAD token.
		    AuthenticationResult result = authContext.AcquireToken(APP_ID_URI, credential);
		    var aadToken = new JObject();
		    aadToken["access_token"] = result.AccessToken;
		
		    // Send the AAD token to the gateway and get a Zumo token
		    var appServiceUser = await appServiceClient.LoginAsync
		        ("aad", aadToken).ConfigureAwait(false);
		
		    return appServiceClient;
		}

	このコードでは、`ConfigHelper.Authority` が "https://login.microsoftonline.com/{テナント}"、たとえば "https://login.microsoftonline.com/contoso.onmicrosoft.com" に解決されます。

2.	API アプリを呼び出すコードを、`About` メソッドの末尾にある `return View()` ステートメントの直前に追加します (次の手順では `About` ビューにコードを追加して、返されたデータを表示します)。

		var appServiceClient = await GetAppServiceClient();
		var client = appServiceClient.CreateContactsList();
		var contacts = client.Contacts.Get();
		ViewData["contacts"] = contacts;

3. *Views/Home/About.cshtml* で、`h2` 見出しの直後に連絡先情報を表示するコードを追加します。

		<h3>Contacts</h3>
		<table class="table table-striped table-bordered table-condensed table-hover">
		    <tr>
		        <th>Name</th>
		        <th>Email</th>
		    </tr>
		
		    @foreach (WebAppGroupClaimsDotNet.Models.Contact contact in (IList<WebAppGroupClaimsDotNet.Models.Contact>)ViewData["contacts"])
		    {
		        <tr>
		            <td>@contact.Name</td>
		            <td>@contact.EmailAddress</td>
		        </tr>
		    }
		
		</table>


3. アプリケーションの Web.config ファイルで、次のバインド リダイレクトを `<assemblyBinding>` 開始タグの後ろに追加します。

		<dependentAssembly>
		  <assemblyIdentity name="System.Net.Http.Primitives" publicKeyToken="b03f5f7f11d50a3a" culture="neutral"/>
		  <bindingRedirect oldVersion="0.0.0.0-4.2.28.0" newVersion="4.2.28.0"/>
		</dependentAssembly>

	このバインド リダイレクトがないとアプリは失敗します。App Service SDK には、System.Net.Http.Primitives バージョン 1.5.0.0 への依存関係が含まれていますが、プロジェクトで使用されるバージョンは 4.2.28.0 だからです。
 
3. [Readme ファイル](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)の「**Deploy this sample to Azure (このサンプルを Azure にデプロイする)**」の手順に従います。

5. アプリケーションを実行します。

	![](./media/app-service-api-authentication-client-flow/homepage.png)

6. **[サインイン]** をクリックし、このアプリケーションに使用する AAD ドメインのユーザーの資格情報を入力します。

	![](./media/app-service-api-authentication-client-flow/signedin.png)

7. **[About]** をクリックします。

	About コントローラーとビューに追加したコードが実行され、API アプリへの認証が正常に行われ、Get メソッドを呼び出したことが表示されます。

	![](./media/app-service-api-authentication-client-flow/aboutpage.png)

## トラブルシューティング

### HTTP 400 エラー 

ゲートウェイ URL が http ではなく、https であることを確認します。Azure プレビュー ポータルから直接コピーした場合は、http が指定さている可能性があります。
 
### SQL サーバー エラー

アプリケーションは、GroupClaimContext の接続文字列で識別される SQL Server データベースにアクセスできる必要があります。デプロイされたサイトの接続文字列が、SQL Database インスタンスを指定していることを確認します。正しい接続文字列は、デプロイされた Web.config または Azure ランタイム構成設定に配置できます。

## 謝辞

このチュートリアルを作成するにあたり、ご協力いただいた Govind S. Yadav 氏 ([@matvelloso](https://twitter.com/govindsyadav)) に感謝いたします。

## 次のステップ

App Service API Apps のクライアント フローの認証方法は確認しました。他の方法で API アプリを認証する方法については、「[API Apps と Mobile Apps の認証](../app-service/app-service-authentication-overview.md)」を参照してください。

[Azure ポータル]: https://manage.windowsazure.com/
[Azure プレビュー ポータル]: https://portal.azure.com/

<!---HONumber=Oct15_HO1-->