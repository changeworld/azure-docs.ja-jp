<properties
	pageTitle="Azure App Service での API Apps のサービス プリンシパルの認証 |Microsoft Azure"
	description="Azure App Service でサービス間シナリオで API アプリを保護する方法について説明します。"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016" 
	ms.author="tdykstra"/>

# Azure App Service での API Apps のサービス プリンシパル認証

[AZURE.INCLUDE [セレクター](../../includes/app-service-api-auth-selector.md)]

## 概要

この記事では、App Service 認証を使用して API アプリへの[内部的](app-service-api-authentication.md#internal)なアクセスを実現する方法について説明します。ここでいう "内部的" とは、特定の API アプリを自分のアプリケーション コードからしか利用できないようにすることを指します。このシナリオを App Service で実装する最も簡単な方法は、呼び出し先の API アプリを Azure AD で保護することです。Azure AD にアプリケーション ID (サービス プリンシパル) の資格情報を提示することによって取得したベアラー トークンを使って保護対象の API アプリを呼び出します。

この記事では、次の内容について説明します。

* Azure Active Directory (Azure AD) を使用し、認証されていないアクセスから API アプリを保護する方法。
* API アプリ、Web アプリ、モバイル アプリから Azure AD のサービス プリンシパル (アプリ ID) の資格情報を使用して、保護対象の API アプリを使用する方法。ロジック アプリから API を利用する方法については、「[App Service でホストされたカスタム API のロジック アプリでの使用](../app-service-logic/app-service-logic-custom-hosted-api.md)」を参照してください。
* ログオンしたユーザーのブラウザーから、保護対象の API アプリを呼び出すことができないようにする方法。
* 保護対象の API アプリを特定の Azure AD サービス プリンシパルからのみ呼び出すことができるようにする方法。

この記事には 2 つのセクションがあります。

* 「[Azure App Service でサービス プリンシパル認証を構成する方法](#authconfig)」セクションでは、一般的に、API アプリの認証を構成する方法と、保護対象の API アプリを使用する方法について説明します。このセクションは、.NET、Node.js、Java など、App Service でサポートされるすべてのフレームワークに同様に適用されます。

* 「[.NET 入門チュートリアルの続行](#tutorialstart)」セクション以降では、App Service で動作する .NET サンプル アプリケーションに対して "内部的なアクセス" を構成する手順を説明します。

## <a id="authconfig"></a> Azure App Service でサービス プリンシパル認証を構成する方法

ここでは、すべての API アプリに当てはまる一般的な手順を説明します。To Do List .NET サンプル アプリケーション固有の手順については、「[.NET 入門チュートリアルの続行](#tutorialstart)」を参照してください。

1. [Azure ポータル](https://portal.azure.com/)で、保護する API アプリの **[API アプリ]** ブレードに移動し、**[設定]** をクリックします。

2. **[設定]** ブレードで、**[機能]** セクションを探し、**[認証/承認]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. **[認証/承認]** ブレードで、**[オン]** をクリックします。

4. **[要求が認証されていない場合のアクション]** ボックスの一覧の **[Azure Active Directory でログイン]** を選択します。

5. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. **[Azure Active Directory の設定]** ブレードを構成して新しい Azure AD アプリケーションを作成するか、使用する機能が既に含まれている Azure AD アプリケーションがある場合はそれを使用します。

	通常、内部シナリオには、API アプリを呼び出す API アプリが含まれます。各 API アプリに別の Azure AD アプリケーションを使用するか、1 つの Azure AD アプリケーションのみを使用することができます。

	このブレードの詳細な手順については、「[Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)」を参照してください。

7. 認証プロバイダーの構成ブレードを完了したら、**[OK]** をクリックします。

7. **認証/承認**ブレードで、**[保存]** をクリックします。

この作業が完了すると、App Service は、構成されている Azure AD テナント内の呼び出し元から送信された要求のみを許可するようになります。保護対象の API アプリでは、認証または承認コードは必要ありません。API アプリには、使用頻度の高い要求と共にベアラー トークンが HTTP ヘッダーに格納されて渡されます。その情報をコード内で読み取ることによって、特定の呼び出し元 (サービス プリンシパルなど) から送信された要求であることを確認できます。

この認証機能は、.NET、Node.js、Java など、App Service がサポートするすべての言語に対して同様に動作します。

#### 保護対象の API アプリを使用する方法

呼び出し元は、API の呼び出しに Azure AD ベアラー トークンを示す必要があります。呼び出し元がサービス プリンシパル資格情報を使用してベアラー トークンを取得するには、Active Directory Authentication Library (ADAL for [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)、[Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)、または [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)) を使用します。トークンを取得するには、ADAL を呼び出すコードから、次の情報を ADAL に示します。

* Azure AD テナントの名前。
* 呼び出し元に関連付けられている Azure AD アプリのクライアント ID とクライアント シークレット (アプリ キー)。
* 保護対象の API アプリに関連付けられている Azure AD アプリケーションのクライアント ID(1 つの Azure AD アプリケーションのみが使用されている場合、呼び出し元と同じクライアント ID です)。

これらの値は、[Azure クラシック ポータル](https://manage.windowsazure.com/)の Azure AD ページで確認できます。

トークンを取得したら、呼び出し元で Authorization ヘッダーの HTTP 要求に含めます。App Service はトークンを検証し、保護対象の API アプリに到達する要求を許可します。

#### 同じテナント内のユーザーからのアクセスに対して API アプリを保護する方法

同じテナント内のユーザーのベアラー トークンは、保護対象の API アプリでは有効と見なされます。保護対象となる API アプリの呼び出しをサービス プリンシパルに限定するには、トークンに含まれている以下の要求を検証するコードを保護対象の API アプリに追加します。

* `appid` が、呼び出し元に関連付けられている Azure AD アプリケーションのクライアント ID であること。 
* `oid` (`objectidentifier`) が、呼び出し元のサービス プリンシパル ID であること。 

App Service から提供される `objectidentifier` 要求は、X-MS-CLIENT-PRINCIPAL-ID ヘッダーにも格納されます。

### ブラウザー アクセスから API アプリを保護する方法

保護対象の API アプリのコードで要件を検証せず、保護対象の API アプリに別の Azure AD アプリケーションを使用する場合は、Azure AD アプリケーションの Reply URL が API アプリのベース URL と同じにならないようにします。Reply URL が保護対象の API アプリを直接示している場合、同じ Azure AD テナント内のユーザーは、API アプリの閲覧、ログオン、API の呼び出しを実行できるようになります。

## <a id="tutorialstart"></a>.NET 入門チュートリアルの続行

API アプリの Node.js または Java の入門シリーズを読んでいる場合は、「[次のステップ](#next-steps)」セクションに進みます。

この記事では、引き続き API アプリの .NET 入門シリーズについて説明します。また、[ユーザー認証のチュートリアル](app-service-api-user-principal-auth.md)を完了し、ユーザー認証を有効にして Azure でサンプル アプリケーションを実行している前提で話を進めます。

## Azure で認証を設定する

このセクションでは、データ層 API アプリに到達できる HTTP 要求のみが有効な Azure AD ベアラー トークンを持つように App Service を構成します。

次のセクションでは、アプリケーションの資格情報を Azure AD に送信し、ベアラー トークンを取得し、ベアラー トークンをデータ層 API アプリに送信する中間層 API アプリを構成します。このプロセスを次の図に示します。

![](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

1. [Azure ポータル](https://portal.azure.com/)で、ToDoListDataAPI (データ層) API アプリ用に作成した API アプリの [API アプリ] ブレードに移動し、**[設定]** をクリックします。

2. **[設定]** ブレードで、**[機能]** セクションを探し、**[認証/承認]** をクリックします。

3. **[認証/承認]** ブレードで、**[オン]** をクリックします。

4. **[要求が認証されていない場合のアクション]** ドロップダウン リストで、**[Azure Active Directory でログイン]** を選択します。

	この設定を使用すると、App Service では認証済みの要求のみが API アプリに到達するようになります。有効なベアラー トークンがある要求の場合、App Service はトークンを API アプリに渡し、一般的に使用される要求が指定された HTTP ヘッダーを生成します。その結果、コードから使いやすい情報になります。

5. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。

6. **[Azure Active Directory 設定]** ブレードで **[Express]** をクリックします。

	**[Express]** オプションを選択すると、Azure は Azure AD [テナント](https://msdn.microsoft.com/ja-JP/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)に AAD アプリケーションを自動的に作成できます。

	すべての Azure アカウントにテナントが自動的に作成されるので、ユーザーがテナントを作成する必要はありません。

7. **[管理モード]** で **[新しい AD アプリを作成する]** をクリックします。

	ポータルは **[アプリの作成]** 入力ボックスを既定値と結びつけます。
	
	![](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

	既定では、Azure AD アプリケーションの名前は API アプリと同じです。必要に応じて、別の名前を入力できます。

	また、もう 1 つの方法として、呼び出し元の API アプリと保護対象の API アプリの両方に 1 つの Azure AD アプリケーションを使用することもできます。この方法を選択する場合、既に前のユーザー認証チュートリアルで Azure AD アプリケーションを作成しているので、ここでは **[新しい AD アプリを作成する]** オプションを選択する必要はありません。このチュートリアルでは、呼び出し元の API アプリと保護対象の API アプリには別の Azure AD アプリケーションを使用します。

8. **[アプリの作成]** 入力ボックスの値を書き留めておきます。後で、この AAD アプリケーションを Azure クラシック ポータルで検索します。

7. **[OK]** をクリックします。

10. **認証/承認**ブレードで、**[保存]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

	**[サインオン URL]** と **[応答 URL]** に API アプリの URL が自動的に設定された Azure Active Directory アプリケーションが App Service により作成されます。[応答 URL] を使用すると、AAD テナントのユーザーは API アプリにログインしてアクセスできるようになります。

### API アプリが保護されていることを確認します。

1. ブラウザーで API アプリの URL に移動します。Azure ポータルの **[API アプリ]** ブレードで、**[URL]** の下にあるリンクをクリックします。 

	認証されていない要求は API アプリへのアクセスを許可されないため、ログイン画面にリダイレクトされます。

	ブラウザーに Swagger UI が表示される場合、ブラウザーでログオンが既に完了している可能性があります。この場合は、[InPrivate] または [Incognito] ウィンドウを開き、Swagger UI の URL にアクセスします。

18. AAD テナントで、ユーザーの資格情報を使用してログインします。

	ログオンすると、ブラウザーに "正常に作成されました" ページが表示されます。

## Azure AD トークンを取得して送信するように ToDoListAPI プロジェクトを構成します。

このセクションでは、次のタスクを実行します。

* 中間層 API アプリに、Azure AD アプリケーションの資格情報を使用してトークンを取得し、HTTP 要求と共にデータ層 API アプリに送信するコードを追加します。
* Azure AD から必要な資格情報を取得します。
* 中間層 API アプリで Azure App Service ランタイム環境の設定に資格情報を入力します。 

### Azure AD トークンを取得して送信するように ToDoListAPI プロジェクトを構成します。

Visual Studio で、ToDoListAPI プロジェクトを次のように変更します。

1. *ServicePrincipal.cs* ファイルに含まれるすべてのコードのコメントを解除します。

	これは、ADAL for .NET を使用して Azure AD ベアラー トークンを取得するコードです。このコードでは、後で Azure ランタイム環境に設定するいくつかの構成値を使用します。コードは次のとおりです。

		public static class ServicePrincipal
		{
		    static string authority = ConfigurationManager.AppSettings["ida:Authority"];
		    static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
		    static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
		    static string resource = ConfigurationManager.AppSettings["ida:Resource"];
		
		    public static AuthenticationResult GetS2SAccessTokenForProdMSA()
		    {
		        return GetS2SAccessToken(authority, resource, clientId, clientSecret);
		    }
		
		    static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
		    {
		        var clientCredential = new ClientCredential(clientId, clientSecret);
		        AuthenticationContext context = new AuthenticationContext(authority, false);
		        AuthenticationResult authenticationResult = context.AcquireToken(
		            resource,
		            clientCredential);
		        return authenticationResult;
		    }
		}

	**注:** このコードには、ADAL for .NET NuGet パッケージ (Microsoft.IdentityModel.Clients.ActiveDirectory) が必要です。このバージョンはプロジェクトに既にインストールされています。一からこのプロジェクトを作成している場合は、このパッケージをインストールする必要があります。API app new-project テンプレートでは、このパッケージは自動的にインストールされません。

2. *Controllers/ToDoListController* で、承認ヘッダーで HTTP 要求にトークンを追加する `NewDataAPIClient` メソッドのコードのコメントを解除します。

		client.HttpClient.DefaultRequestHeaders.Authorization =
		    new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. ToDoListAPI プロジェクトをデプロイします(プロジェクトを右クリックし、**[発行]、[発行]** の順にクリックします)。

4. プロジェクトが正常にデプロイされた後に開くブラウザー ウィンドウを閉じます。

### Azure AD の構成値を取得する

11. [Azure クラシック ポータル](https://manage.windowsazure.com/)で **[Azure Active Directory]** に移動します。

12. **[ディレクトリ]** タブで AAD テナントをクリックします。

14. **[アプリケーション]、[自分の会社が所有するアプリケーション]** の順にクリックし、チェック マークをクリックします。

15. アプリケーションの一覧から、ToDoListDataAPI (データ層) API アプリの認証を有効にしたときに Azure によって作成されたアプリケーション名をクリックします。

16. [**構成**] タブをクリックします。

5. **[クライアント ID]** の値をコピーし、後で取得できる場所に保存します。

8. Azure クラシック ポータルで、**[自分の会社が所有するアプリケーション]** の一覧に戻り、ToDoListAPI (中間層) API アプリ用に作成した AAD アプリケーションをクリックします。

16. [**構成**] タブをクリックします。

5. **[クライアント ID]** の値をコピーし、後で取得できる場所に保存します。

6. **[キー]** の **[時間の選択]** ドロップダウン リストで **[1 年間]** を選択します。

6. **[保存]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. キー値をコピーし、後で取得できる場所に保存します。

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### 中間層 API アプリのランタイム環境で Azure AD 設定を構成する

1. [Azure ポータル](https://portal.azure.com/)を開き、TodoListAPI (中間層) プロジェクトをホストする API アプリの **[API アプリ]** ブレードに移動します。

2. **[設定]、[アプリケーションの設定]** の順にクリックします。

3. **[アプリ設定]** セクションで、次のキーと値を追加します。

	|キー|値|例
	|---|---|---|
	|ida:Authority|https://login.microsoftonline.com/{your Azure AD テナント名}|https://login.microsoftonline.com/contoso.onmicrosoft.com|
	|ida:ClientId|呼び出し元のアプリケーションのクライアント ID (ToDoListAPI)|960adec2-b74a-484a-960adec2-b74a-484a|
	|ida:ClientSecret|呼び出し元のアプリケーションのアプリ キー (ToDoListAPI)|oCgdj3EYLfnR0p6iR3UvHFAfkn+zQB+0VqZT/6=
	|ida:Resource|呼び出し先のアプリケーション (ToDoListDataAPI) のクライアント ID|e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8|

	呼び出し元 API アプリと保護対象の API アプリの両方に 1 つの Azure AD アプリケーションを使用した場合、`ida:ClientId` と `ida:Resource` の両方に同じ値を使用します。

	このコードでは、ConfigurationManager を使用してこれらの値を取得するので、プロジェクトの Web.config ファイルまたは Azure ランタイム環境に保存できます。ASP.NET アプリケーションは Azure App Service で実行されますが、環境設定は、Web.config の設定よりも自動的に優先されます。一般的に、[機密情報を保存する場合、環境設定の方が Web.config ファイルよりも安全](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)です。

6. **[保存]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### アプリケーションをテストする

1. ブラウザーで、AngularJS フロント エンド Web アプリの HTTPS URL にアクセスします。

2. **[To Do List]** タブをクリックし、ユーザーの資格情報を使用して Azure AD テナントにログインします。

4. to-do 項目を追加し、アプリケーションが動作していることを確認します。

	![](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

	期待どおりにアプリケーションが動作しない場合は、Azure ポータルで入力したすべての設定を確認します。すべての設定が正しいと考えられる場合は、このチュートリアルの後半にある「[トラブルシューティング](#troubleshooting)」セクションを参照してください。

## ブラウザー アクセスから API アプリを保護する

このチュートリアルでは、ToDoListDataAPI (データ層) API アプリ用に別の Azure AD アプリケーションを作成しました。これまで説明したように、App Service で AAD アプリケーションを作成すると、ユーザーがブラウザーで API アプリの URL にアクセスしてログオンできるように AAD アプリケーションが構成されます。つまり、サービス プリンシパルだけでなく、Azure AD テナントのエンド ユーザーも API にアクセスすることができます。

保護対象の API アプリにコードを作成せずに、ブラウザーによるアクセスを防ぐには、AAD アプリケーションの **[応答 URL]** を変更して、API アプリのベース URL とは異なる URL にします。

### ブラウザー アクセスを無効にする

1. クラシック ポータルの **[構成]** タブで、TodoListService 用に作成した AAD アプリケーションの **[応答 URL]** フィールドの値を、API アプリの URL 以外の有効な URL に変更します。
 
2. **[保存]** をクリックします。

### ブラウザー アクセスが無効になったことを確認する

以前のセクションでは、ブラウザーで API アプリの URL にアクセスし、個々のユーザーの資格情報を使用してログオンできることを確認しました。このセクションでは、同様の方法でログオンできないことを確認します。

1. 新しいブラウザー ウィンドウで、API アプリの URL にもう一度アクセスします。

2. ログインを求められたらログインします。

3. ログインは成功しますが、エラー ページにリダイレクトされます。

	AAD テナントのユーザーがブラウザーからログインして API にアクセスできないように AAD アプリを構成しました。サービス プリンシパル トークンを使用して API アプリにアクセスすることはできます。確認のために、Web アプリの URL にアクセスし、to-do 項目を追加してみてください。

## 特定のサービス プリンシパルに対するアクセスを制限する  

現在のところ、Azure AD テナントのユーザーまたはサービス プリンシパルのトークンを取得できる呼び出し元は、TodoListDataAPI (データ層) API アプリを呼び出すことができます。たとえば、データ層 API アプリで、TodoListAPI (中間層) API アプリからの呼び出しや、特定のサービス プリンシパルからの呼び出しのみを受け入れるようにすることができます。

このような制限を追加するには、着信呼び出しの `appid` と `objectidentifier` の各要求を検証するコードを追加します。

このチュートリアルでは、コントローラー アクションでアプリ ID とサービス プリンシパル ID を直接検証するコードを設定します。または、カスタムの `Authorize` 属性を使用するか、スタートアップ シーケンスでこの検証を実行します (OWIN ミドルウェアなど)。後者の例については、[こちらのサンプル アプリケーション](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs)を参照してください。

TodoListDataAPI プロジェクトを次のように変更します。

2. *Controllers/TodoListController.cs* ファイルを開きます。

3. `trustedCallerClientId` と `trustedCallerServicePrincipalId` を設定する行のコメントを解除します。

		private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
		private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. CheckCallerId メソッドのコードのコメントを解除します。このメソッドは、コントローラーでアクション メソッドが開始されるたびに呼び出されます。

		private static void CheckCallerId()
		{
		    string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
		    string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		    if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
		    {
		        throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
		    }
		}

5. Azure App Service に ToDoListDataAPI プロジェクトを再デプロイします。

6. ブラウザーで AngularJS フロントエンド Web アプリの HTTPS URL にアクセスし、ホーム ページの **[To Do List]** タブをクリックします。

	バック エンドの呼び出しは失敗しているので、アプリケーションは動作していません。新しいコードは、実際の appid と objectidentifier を確認していますが、確認に使用する正しい値はまだ取得していません。ブラウザーの開発者ツール コンソールには、サーバーから HTTP 401 エラーが返されているというレポートが表示されます。

	![](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

	次の手順では、目的の値を構成します。

8. Azure AD PowerShell を使用して、TodoListWebApp プロジェクト用に作成した Azure AD アプリケーションのサービス プリンシパル値を取得します。

	a.Azure PowerShell をインストールし、サブスクリプションに接続する手順については、「[Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

	b.サービス プリンシパルの一覧を取得するために、`Login-AzureRmAccount` コマンド、`Get-AzureRmADServicePrincipal` コマンドの順に実行します。

	c.TodoListAPI アプリケーションのサービス プリンシパルの objectid を検索し、後でコピーできる場所に保存します。

7. Azure ポータルで、ToDoListAngular プロジェクトをデプロイした Web アプリの Web アプリ ブレードに移動します。

9. **[設定]、[アプリケーションの設定]** の順にクリックします。

3. **[アプリ設定]** セクションで、次のキーと値を追加します。

	|キー|値|例
	|---|---|---|
	|todo:TrustedCallerServicePrincipalId|呼び出し元アプリケーションのサービス プリンシパル ID|4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072|
	|todo:TrustedCallerClientId|呼び出し元アプリケーションのクライアント ID (TodoListAPI AAD アプリケーションからコピー)|960adec2-b74a-484a-960adec2-b74a-484a|

6. **[保存]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. ブラウザーで Web アプリの URL に戻り、ホーム ページの **[To Do List]** タブをクリックします。

	今回は、信頼済みの呼び出し元アプリ ID とサービス プリンシパル ID が期待される値なので、アプリケーションは期待どおりに動作します。

	![](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## 一からのプロジェクトのビルド

**Azure API アプリ** プロジェクト テンプレートを使用し、既定の Values コントローラーを ToDoList コントローラーに置き換えることによって、2 つの Web API プロジェクトを作成しました。ToDoListAPI プロジェクトで Azure AD サービス プリンシパル トークンを取得するために、[Active Directory Authentication Library (ADAL) for .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) NuGet パッケージをインストールしました。
 
ToDoListAngular のような Web API バックエンドで AngularJS 単一ページ アプリケーションを作成する方法については、「[Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js (ハンズオン ラボ: ASP.NET Web API と Angular.js で単一ページ アプリケーション (SPA) を構築する)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs)」を参照してください。Azure AD 認証コードを追加する方法については、「[Azure AD で AngularJS シングル ページ アプリをセキュリティで保護する](../active-directory/active-directory-devquickstarts-angular.md)」を参照してください。

## トラブルシューティング

認証なしでは正常に動作するアプリケーションが、認証を実装すると動作しなくなる場合、通常は、構成の設定が正しくないか、一貫していないことが問題です。まず、Azure App Service および Azure Active Directory ですべての設定を再確認します。具体的なヒントを次に示します。

* プロジェクトでコードを構成した後、他のプロジェクトではなくそのプロジェクトを再デプロイしたことを確認します。
* Azure ポータルの **[アプリケーション設定]** ブレードで構成した設定について、設定を入力したときに正しい API アプリまたは Web アプリを選択したことを確認します。
* ブラウザーで HTTP URL ではなく HTTPS URL にアクセスしていることを確認します。
* 中間層 API アプリで CORS がまだ有効になっていて、フロントエンド HTTPS URL から中間層への呼び出しが許可されることを確認します。問題が CORS 関連である可能性がある場合は、許可される配信元 URL として "*" を試してみます。**重要:** 本当の問題がデータ層の認証にある場合、一部のブラウザーの開発者ツール コンソールでは、CORS エラー メッセージがレポートされることがあります。この問題を確認するには、ToDoListDataAPI API アプリの認証を一時的に無効にしてみます。
* [customErrors モードを Off](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview) に設定して、可能な限り多くの情報がエラー メッセージで得られるようにします。
* 他の方法が失敗する場合は、[リモート デバッグ セッション](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)を試して、ToDoListAPI でベアラー トークンを取得するコードと、ToDoListDataAPI で受け取る Azure AD 値を検証するコードに渡される変数値を確認します。コードはさまざまなソースの構成値を選択することができるので、意外な結果になることがあります。たとえば、App Service 設定を構成するときに `ida:ClientId` を `ida:ClientID` と名前を間違えると、App Service 設定が無視され、Web.config ファイルが検索されて、`ida:ClientId` 値が取得される可能性があります。 

## 次のステップ

これは、API Apps 入門シリーズの最終回の記事です。

Azure Active Directory の詳細については、次のリソースを参照してください。

* [Azure AD 開発者ガイド](http://aka.ms/aaddev)
* [Azure AD のシナリオ](http://aka.ms/aadscenarios)
* [Azure AD のサンプル](http://aka.ms/aadsamples)。[WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) サンプルは、このチュートリアルで紹介した内容と似ていますが、App Service 認証は使用しません。

Visual Studio を使用するか、[ソース管理システム](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)から[デプロイを自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)して、Visual Studio プロジェクトを API アプリにデプロイする他の方法については、「[Azure App Service へのアプリのデプロイ](web-sites-deploy.md)」を参照してください。

<!---HONumber=AcomDC_0211_2016-->