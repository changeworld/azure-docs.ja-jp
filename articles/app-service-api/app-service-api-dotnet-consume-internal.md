<properties 
	pageTitle=".NET クライアントからの Azure App Service での内部 API アプリの使用" 
	description="App Service SDK を使用して同じリソース グループ内の .NET API アプリから API アプリを使用する方法について説明します。" 
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
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# .NET クライアントからの Azure App Service での内部 API アプリの使用 

## 概要

このチュートリアルでは、**内部**アクセス レベル用に構成されている別の API アプリを呼び出す ASP.NET [API アプリ](app-service-api-apps-why-best-platform.md)のコードを作成する方法について説明します。どちらの API アプリも同じリソース グループに属している必要があります。同じコードを使用して、[モバイル アプリ](../app-service-mobile/app-service-mobile-value-prop-preview.md)から内部 API アプリを呼び出すこともできます。

このチュートリアルでは、ContactNames Web API を作成します。この Web API の Get メソッドは、ContactsList API アプリを呼び出し、ContactsList API アプリによって提供される連絡先情報から名前だけを返します。次に示すのは、ContactNames Get メソッドの呼び出しが成功したときの Swagger UI 画面です。

![](./media/app-service-api-dotnet-consume-internal/tryitout.png)

**パブリック (匿名)** または**パブリック (認証済み)** アクセス レベル用に構成されている API アプリを呼び出す方法については、「[Azure App Service で .NET クライアントから API アプリ使用する](app-service-api-dotnet-consume.md)」を参照してください。

## 前提条件

このチュートリアルでは、Visual Studio でプロジェクトを作成してコードを追加する方法と、[Azure プレビュー ポータルで API アプリを管理する](../app-service-api-apps-manage-in-portal.md)方法について詳しく理解していることを前提としています。

この記事のプロジェクトとコードのサンプルは、次の記事で作成およびデプロイした API アプリ プロジェクトに基づいています。

- [API アプリの作成](app-service-dotnet-create-api-app.md)
- [API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

このチュートリアルでは、Azure SDK for .NET 2.6 以降のバージョンが必要です。

### ターゲット API アプリの設定

1. まだ完了していない場合は、「[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)」チュートリアルの手順に従って、Azure サブスクリプションの API アプリに ContactsList サンプル プロジェクトをデプロイします。

2. [Azure プレビュー ポータル](https://portal.azure.com/)で、前にデプロイした ContactsList API アプリの **[API アプリ]** ブレードにおいて、**[設定]、[アプリケーション設定]** の順にクリックし、**[アクセス レベル]** を **[内部]** に設定して、**[保存]** をクリックします。

	![](./media/app-service-api-dotnet-consume-internal/setinternal.png)
 
## 既存の API アプリを呼び出す新しい API アプリを作成します。

- Visual Studio で、Azure API アプリ プロジェクト テンプレートを使用して、ContactNames という名前の API アプリ プロジェクトを作成します。

	これは「[Azure App Service の API アプリの作成](app-service-dotnet-create-api-app.md)」と同じプロセスですが、このチュートリアルでは後でプロジェクトに異なるコードを追加します。
 
## App Service SDK で生成されたクライアント コードの追加

以下の手順については、「[Azure App Service で .NET クライアントから API アプリ使用する](app-service-api-dotnet-consume.md)」で詳しく説明されています。

3. **ソリューション エクスプローラー**で、プロジェクト (ソリューションではありません) を右クリックします。**[追加]、[Azure API アプリ クライアント]** の順にクリックします。 

3. **[Azure API アプリ クライアントの追加]** ダイアログ ボックスで、**[Azure API アプリからダウンロード]** をクリックします。

5. ドロップダウン リストから、呼び出す API アプリを選択します。このチュートリアルでは、前に作成した ContactsList API アプリを選択します。

7. **[OK]** をクリックします。

## Swagger UI の有効化

既定では、API アプリ プロジェクトは [Swagger](http://swagger.io/ "公式の Swagger 情報") メタデータの自動生成が有効になりますが、Azure API アプリの新しいプロジェクト テンプレートでは API テスト ページが無効になります。このセクションでは、テスト ページを有効にします。

1. *App_Start/SwaggerConfig.cs* ファイルを開き、**EnableSwaggerUI** を探します。

2. 次のコード行をコメント解除します。

	        })
	    .EnableSwaggerUi(c =>
	        {

## コントローラーの追加

5. **[コントローラー]** フォルダーを右クリックしてから、**[追加]、[コントローラー]** の順にクリックします。 

6. **[スキャフォールディングの追加]** ダイアログで、**[Web API 2 コントローラー - 空]** を選択し、**[追加]** をクリックします。

7. コントローラーに **ContactNamesController** という名前を付け、**[追加]** をクリックします。

## API アプリを呼び出すコードの追加

アクセス レベルを**内部**に設定することによって保護されている API アプリを呼び出すには、内部認証ヘッダーを HTTP 要求に追加する必要があります。これらのヘッダーは、呼び出し元が同じリソース グループ内から呼び出しているピア API アプリであることを対象の API アプリに通知します。

App Service SDK によって生成されるクライアント クラスを使用すると、API アプリを呼び出すコードを簡単に記述できます。**パブリック (匿名)** API アプリを呼び出すには、次の例のように、クライアント オブジェクトを作成し、そのオブジェクトに対してメソッドを呼び出す必要があります。

		var client = new ContactsList();
		var contacts = await client.Contacts.GetAsync();

ただし、認証ヘッダーを追加するには `HttpRequestMessage` オブジェクトにアクセスする必要がありますが、ここにはありません。要求にアクセスしてヘッダーを追加するには、`DelegatingHandler` クラスを作成し、そのインスタンスを生成されたクライアントのコンストラクターに渡す必要があります。

1. *InternalCredentialHandler.cs* という名前のクラス ファイルをプロジェクトに追加し、テンプレート コードを次のコードに置き換えます。

		using Microsoft.Azure.AppService.ApiApps.Service;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace ContactNames
		{
		    public class InternalCredentialHandler : DelegatingHandler
		    {
		        protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		        {
		            Runtime.FromAppSettings(request).SignHttpRequest(request);
		            return base.SendAsync(request, cancellationToken);
		        }
		    }
		}

	このコードは、`SignHttpRequest` を呼び出して、生成されたクライアント クラスによって送信されるすべての要求に認証ヘッダーを追加します。

		Runtime.FromAppSettings(this.Request).SignHttpRequest

1. *ContactNamesController.cs* で、テンプレート コードを次のコードに置き換えます。

		using ContactNames.Models;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactNames.Controllers
		{
		    public class ContactNamesController : ApiController
		    {
		        [HttpGet]
		        public async Task<IEnumerable<string>> Get()
		        {
		            var names = new List<string>();

		            var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });
		            var contacts = await client.Contacts.GetAsync();
		
		            foreach (Contact contact in contacts)
		            {
		                names.Add(contact.Name);
		            }		
		            return names;
		        }
		    }
		}

	このコードは、生成されたクライアント クラスのコンストラクターにハンドラーを渡します。

		var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });

### デプロイ

ローカルに実行したのではテストできません。コードをデプロイし、Azure API アプリ内で実行する必要があります。そうしないと、適切な認証ヘッダーを追加できず、呼び出しは破棄されます。

以下のデプロイ手順については、「[Azure App Service の API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)」で詳しく説明されています。

1. ContactNames API アプリを作成します。

	* **ソリューション エクスプローラー**で、プロジェクト (ソリューションではありません) を右クリックしてから、**[発行]** をクリックします。 

	* **[プロファイル]** タブ、**[Microsoft Azure API Apps]** の順にクリックします。

	* **[新規]** をクリックして、Azure サブスクリプションに新しい API アプリをプロビジョニングします。

	* **[API アプリの作成]** ダイアログで、**[API アプリの名前]** として「ContactNames」と入力します。

	* **[API アプリの作成]** ダイアログの他の値については、前に「[Azure App Service の API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)」で入力したものと同じ値を入力します。

		最も重要なのは、後で呼び出す API アプリと同じリソース グループ内に新しい API アプリを作成することです。

	* **[OK]** をクリックします。

2. 新しい API アプリにコードをデプロイします。

	* API アプリがプロビジョニングされたら、**ソリューション エクスプローラー**でプロジェクトを右クリックして **[発行]** をクリックします。発行ダイアログが再び開きます。

	* **[Web の発行]** ダイアログで、**[発行]** をクリックしてデプロイ プロセスを開始します。

### テスト

このセクションでは、Swagger UI を使用して新しい API アプリをテストし、前に作成した API アプリを呼び出せることを確認します。

1. ブラウザーで新しい API アプリの URL を開きます。

	発行の既定の設定では、Visual Studio は発行プロセスを完了すると、自動的にブラウザーで API アプリの URL を開きます。自動的に開かない場合、または開いたブラウザー ウィンドウを閉じた場合は、次の手順で同じ URL を開きます。

	* Azure プレビュー ポータルで、新しい ContactsName API アプリの API アプリ ブレードに移動します。

	* **URL** をクリックします。

		![](./media/app-service-api-dotnet-consume-internal/clickurl.png)
  
5. ブラウザーのアドレス バーで、URL の最後に `/swagger` を追加して、Enter キーを押します。

	たとえば、結果の URL は次のようになります。

		https://microsoft-apiapp214f26e673e5449a214f26e673e5449a.azurewebsites.net/swagger

1. [Swagger UI] ページで、**[ContactNames]、[Get]、[Try it out!]** の順にクリックします。

	![](./media/app-service-api-dotnet-consume-internal/tryitout.png)
  
	[Response Body] セクションに連絡先の名前が表示され、ContactNames API アプリが ContactsList API アプリからデータを正しく取得したことを確認できます。

	**内部**の設定によって ContactsList API アプリが保護されていることを確認する場合は、*ContactNamesController.cs* で `SignHttpRequest` の呼び出しをコメントにし、再デプロイして、Swagger の **[Try it now]** を再度実行します。エラー メッセージが表示されます。


## HttpClient を使用して API アプリを呼び出すコードの追加
 
App Service SDK は、Swagger API の定義を利用してクライアント クラスを生成します。Swagger API 定義を実装していない API アプリを呼び出す場合は、`HttpClient` を使用して行うことができます。この場合、`SignHttpRequest` メソッドを使用しますが、`HttpRequestMessage` オブジェクトで直接このメソッドを呼び出します。

1. *ContactNamesController.cs* で、`Get` メソッドの `return names;` ステートメントの前に、以下のコードを追加します。

		var httpClient = new HttpClient();
		HttpRequestMessage httpRequest = new HttpRequestMessage();
		httpRequest.Method = HttpMethod.Get;
		httpRequest.RequestUri = new Uri("https://{yourapiappurl}/api/contacts");
		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);
		var response = await httpClient.SendAsync(httpRequest); 
		var contacts2 = await response.Content.ReadAsAsync<List<Contact>>();
		foreach (Contact contact in contacts2)
		{
		    names.Add(contact.Name);
		}

	このコードは、受信要求オブジェクトを `SignHttpRequest` メソッドに渡して、送信要求オブジェクトに署名します。

		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);

2. Azure プレビュー ポータルで、ContactsList API アプリの API アプリ ブレードに移動し、URL をコピーします。

	![](./media/app-service-api-dotnet-consume-internal/clurl.png)
 
4. コントローラー コードのプレースホルダー文字列 "{yourapiappurl}" を、実際の URL に置き換えます。終了すると、そのコード行は次の例のようになります。

		httpRequest.RequestUri = new Uri("https://microsoft-apiappd99e684d99e684d99e684d99e684.azurewebsites.net/api/contacts");

### デプロイとテスト

1. 前に API アプリ コードをデプロイしたときと同じ手順に従います。

	**ヒント:** **[Web の発行]** ダイアログをバイパスし、ツールバーの 1 つのボタンをクリックして再デプロイできます。Visual Studio で、**[表示]、[ツールバー]** の順にクリックし、**[Web の 1 クリック発行]** ツールバーを有効にします。
 
2. Swagger UI を使用するために前に行った手順に従います。

	HttpClient コードをそのままにしてあるので、今度の出力では重複した名前のセットが表示されます。

	![](./media/app-service-api-dotnet-consume-internal/dupnames.png)
  
## 次のステップ

この記事では、.NET クライアントから内部 API アプリを使用する方法を説明しました。**パブリック (認証済み)** または**パブリック (匿名)** アクセス レベルに設定されている API アプリを使用する方法については、「[Azure App Service で .NET クライアントから API アプリ使用する](app-service-api-dotnet-consume.md)」を参照してください。

.NET クライアントから API アプリを呼び出すコードのその他の例については、[Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample) サンプル アプリケーションをダウンロードしてください。

App Service での認証については、[API Apps と Mobile Apps の認証](../app-service/app-service-authentication-overview.md)に関するページを参照してください。
 

<!---HONumber=July15_HO4-->