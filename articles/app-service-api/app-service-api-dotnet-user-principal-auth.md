<properties
	pageTitle="Azure App Service での API Apps のユーザー認証 |Microsoft Azure"
	description="認証されたユーザーのみがアクセスできるよう Azure App Service で API アプリを保護する方法について説明します。"
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
	ms.topic="hero-article"
	ms.date="01/26/2016"
	ms.author="tdykstra"/>

# Azure App Service の API Apps でのユーザー認証

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概要

この記事では、次の内容について説明します。

* 認証されたユーザーのみが呼び出せるように App Service API アプリを保護する方法。
* 認証プロバイダーを構成する方法と、Azure Active Directory (Azure AD) の詳細。
* [Active Directory Authentication Library (ADAL) for JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) を使用して保護された API アプリを使用する方法。

この記事には 2 つのセクションがあります。

* 「[Azure App Service でユーザー認証を構成する方法](#authconfig)」セクションでは、API アプリ用にユーザー認証を構成する一般的な方法について説明します。.NET、Node.js、Java など、App Service でサポートされるすべてのフレームワークに適用されます。

* [記事の残りの部分](#tutorialstart)では、App Service で実行する .NET サンプル アプリケーションを、ユーザー認証に Azure Active Directory を使用するように構成する方法について説明します。

## <a id="authconfig"></a>Azure App Service でユーザー認証を構成する方法

ここでは、すべての API アプリに当てはまる一般的な手順を説明します。To Do List .NET サンプル アプリケーション固有の手順については、「[.NET 入門チュートリアルの続行](#tutorialstart)」を参照してください。

1. [Azure ポータル](https://portal.azure.com/)で、保護する API アプリの **[API アプリ]** ブレードに移動し、**[設定]** をクリックします。

2. **[設定]** ブレードで、**[機能]** セクションを探し、**[認証/承認]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. **[認証/承認]** ブレードで、**[オン]** をクリックします。

4. **[要求が認証されていない場合のアクション]** ドロップダウン リストから、いずれかのオプションを選択します。

	* 認証された呼び出しのみが API アプリにアクセスできるようにする場合は、**[... でのログイン]** オプションのいずれかを選択します。このオプションを指定すると、API アプリで実行するコードを記述することなく、API アプリを保護できます。

	* すべての呼び出しが API アプリにアクセスできるようにする場合は、**[要求の許可 (操作不要)]** を選択します。このオプションを使用すると、認証されていない呼び出し元に認証プロバイダーを選択させることができます。このオプションを使用する場合は、承認を処理するコードを記述する必要があります。

	詳細については、「[Azure App Service での API Apps の認証と承認](app-service-api-authentication.md#multiple-protection-options)」を参照してください。

5. **[認証プロバイダー]** を 1 つ以上選択します。

	下の図では、すべての呼び出し元が Azure AD によって認証される必要がある選択肢が示されています。
 
	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

	認証プロバイダーを選択すると、そのプロバイダーの構成ブレードが表示されます。

	認証プロバイダー構成ブレードの構成方法の詳細については、「[Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)」を参照してください(リンクで Azure AD に関する記事に移動しますが、記事自体に他の認証プロバイダーの記事にリンクするタブが含まれています)。

7. 認証プロバイダーの構成ブレードを完了したら、**[OK]** をクリックします。

7. **認証/承認**ブレードで、**[保存]** をクリックします。

これが終わると、すべての API 呼び出しは API アプリに到達する前に App Service によって認証されるようになります。認証サービスの動作は、.NET、Node.js、Java など、App Service がサポートするすべての言語に対して同じです。

呼び出し元が認証された API 呼び出しを行うには、認証プロバイダーの OAuth 2.0 ベアラー トークンを、HTTP 要求の承認ヘッダーに組み込みます。トークンは、認証プロバイダーの SDK を使用して取得できます。

## <a id="tutorialstart"></a>.NET 入門チュートリアルの続行

API アプリの Node.js または Java 入門シリーズに従って学習している場合は、次の記事「[Azure App Service での API Apps のサービス プリンシパル認証](app-service-api-dotnet-service-principal-auth.md)」に進みます。

API アプリの .NET 入門シリーズに従って学習していて、[1 番目](app-service-api-dotnet-get-started.md)と [2 番目](app-service-api-cors-consume-javascript.md)のチュートリアルで説明されているようにサンプル アプリケーションを既にデプロイしている場合は、「[認証を構成する](#azureauth)」セクションに進みます。

1 番目と 2 番目のチュートリアルを行っていない場合で、このチュートリアルを進めたい場合は、最初にシリーズのすべての[前提条件](app-service-api-dotnet-get-started.md#prerequisites)を満たしていることを確認してください。その後、次の手順に従ってサンプル アプリケーションをダウンロードしてデプロイします。以下の手順は最初の 2 つのチュートリアルで行ったものと同じですが、ここで簡単に示しておきます。

1. サンプル アプリケーションをダウンロードします。

	a.[Azure-Samples/app-service-api-dotnet-todo-list](https://github.com/Azure-Samples/app-service-api-dotnet-to-do-list) リポジトリからダウンロードします。

	a.ToDoList ソリューションを Visual Studio 2015 で開き、ソリューションをビルドして NuGet パッケージを復元します。

2. 新しい API アプリに ToDoListDataAPI プロジェクトをデプロイします。

	a.ToDoListDataAPI プロジェクトで *App\_Start/SwaggerConfig.cs* ファイルを開き、**EnableSwaggerUi** コードのコメントを解除します。

	b.**ソリューション エクスプローラー**で ToDoListDataAPI プロジェクトを右クリックし、**[発行]** をクリックします。

	c.**Web の発行**ウィザードの **[プロファイル]** ステップで、**[Microsoft Azure App Service]** をクリックします。

	d.**[App Service]** ダイアログ ボックスで、使用する Azure **サブスクリプション**を選択して、**[新規]** をクリックします。

	e.**[App Service の作成]** ダイアログ ボックスの **[ホスティング]** タブで、**[種類の変更]**、**[API アプリ]** を順にクリックします。

	f.**[API アプリの名前]** に、ToDoListDataAPI などの名前に *azurewebsites.net* ドメインでアプリを一意にする番号を付加したもの (例: ToDoListDataAPI1230) を入力します。

	g.**[リソース グループ]** ドロップダウンで、TodoListGroup などの名前を入力して新しいリソース グループを作成します。

	h.**[App Service プラン]** ドロップダウンで **[新規]** をクリックし、**[App Service プランの構成]** ダイアログ ボックスで必要な情報を入力します。

	i.**[作成]** をクリックします。

	j.**[発行]** をクリックします。

3. 新しい API アプリに ToDoListAPI プロジェクトをデプロイします。

	a.ToDoListAPI プロジェクトで *Controllers\\ToDoListController.cs* を開き、`http://localhost:45914` を `https://{your ToDoListDataAPI app name}.azurewebsites.net` に変更します。

	b.ToDoListDataAPI プロジェクトのときと同じ手順に従って ToDoListAPI プロジェクトをデプロイします。忘れずに種類を **[API アプリ]** に変更します。

4. 新しい Web アプリに ToDoListAngular プロジェクトをデプロイします。

	a.ToDoListAngular プロジェクトで *app/scripts/todoListSvc.js* ファイルを開きます。

	b.`apiEndpoint` を localhost の URL に設定する行をコメント化し、`apiEndPoint` を azurewebsites.net の URL に設定する行のコメントを解除して、プレースホルダーを ToDoListAPI 用に作成した API アプリの実際の名前に置き換えます。

	c.ToDoListDataAPI プロジェクトのときと同じ手順に従って ToDoListAPI プロジェクトをデプロイします。**ただし、種類を Web アプリから API アプリには変更しません。**

5. Azure で API アプリ用に CORS を構成します。

	a.[Azure ポータル](https://portal.azure.com/)にアクセスし、ToDoListAPI プロジェクト用に作成した API アプリに移動します。

	b.**[API アプリ]** ブレードで、**[設定]** をクリックします。

	c.**[API]** セクションを探し、**[CORS]** をクリックします。

	d.テキスト ボックスに、呼び出し元として許可する URL を入力します。このチュートリアルでは、ToDoListAngular プロジェクトで作成した Web アプリの URL です。たとえば、「https://todolistangular.azurewebsites.net」などと入力します。

	e.**[保存]** をクリックします。

6. ブラウザーで Web アプリの HTTPS URL を開き、To Do 項目を表示、追加、編集、削除できることを確認します。

## <a id="azureauth"></a>Azure で認証を設定する

現在は、Azure App Service で実行しているアプリケーションはユーザーの認証を必要としません。このセクションでは、次のタスクを実行して認証を追加します。

* 中間層 API アプリを呼び出すために Azure Active Directory (Azure AD) 認証を必要とするように App Service を構成します。
* Azure AD アプリケーションを作成します。
* AngularJS フロントエンドにログオンした後でベアラー トークンを送信するように Azure AD アプリケーションを構成します。 

### App Service で認証を構成する

1. [Azure ポータル](https://portal.azure.com/)で、ToDoListAPI プロジェクト用に作成した API アプリの **[API アプリ]** ブレードに移動します。

2. **[設定]** をクリックします

2. **[設定]** ブレードで、**[機能]** セクションを探し、**[認証/承認]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. **[認証/承認]** ブレードで、**[オン]** をクリックします。

4. **[要求が認証されていない場合のアクション]** ドロップダウン リストで、**[Azure Active Directory でログイン]** を選択します。

	このオプションを指定すると、認証されていない要求は API アプリに到達しなくなります。

5. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. **[Azure Active Directory 設定]** ブレードで **[Express]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	**[Express]** オプションを選択すると、App Service は Azure AD [テナント](https://msdn.microsoft.com/ja-JP/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)に Azure AD アプリケーションを自動的に作成できます。

	すべての Azure アカウントにテナントが自動的に作成されるので、ユーザーがテナントを作成する必要はありません。

7. **[管理モード]** で **[新しい AD アプリを作成する]** をクリックします。

	ポータルは **[アプリの作成]** 入力ボックスを既定値と結びつけます。
	
	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

8. **[アプリの作成]** 入力ボックスの値を書き留めておきます。後で、この AAD アプリケーションを Azure クラシック ポータルで検索します。

	Azure が Azure AD テナントに Azure AD アプリケーションを自動的に作成します。既定では、Azure AD アプリケーションの名前は API アプリと同じです。必要に応じて、別の名前を入力できます。
 
7. **[OK]** をクリックします。

7. **認証/承認**ブレードで、**[保存]** をクリックします。

これで、Azure AD テナントのユーザーだけが API アプリを呼び出せるようになりました。

### 省略可能: API アプリをテストする

1. ブラウザーで API アプリの URL に移動します。Azure ポータルの **[API アプリ]** ブレードで、**[URL]** の下にあるリンクをクリックします。  

	認証されていない要求は API アプリへのアクセスを許可されないため、ログイン画面にリダイレクトされます。

	ブラウザーに "正常に作成されました" ページが表示される場合、ブラウザーは既にログインしている可能性があります。その場合は、InPrivate または Incognito ウィンドウを開き、API アプリの URL に移動します。

2. Azure AD テナントのユーザーの資格情報を使用してログオンします。

	ログオンすると、ブラウザーに "正常に作成されました" ページが表示されます。

9. ブラウザーを閉じます。

### Azure AD アプリケーションを構成する

Azure AD 認証を構成すると、App Service によって Azure AD アプリケーションが自動的に作成されました。既定では、新しい Azure AD アプリケーションは API アプリの URL にベアラー トークンを提供するように構成されます。このセクションでは、中間層 API アプリに直接提供するのではなく、AngularJS フロントエンドにベアラー トークンを提供するように、Azure AD アプリケーションを構成します。AngularJS フロントエンドは、API アプリを呼び出すときに、トークンを API アプリに送信します。

11. [Azure クラシック ポータル](https://manage.windowsazure.com/)で **[Azure Active Directory]** に移動します。

	アクセスする必要のある特定の Azure Active Directory 設定が現在の Azure ポータルでまだ利用できないため、クラシック ポータルを使用する必要があります。

12. **[ディレクトリ]** タブで AAD テナントをクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. **[アプリケーション]、[自分の会社が所有するアプリケーション]** の順にクリックし、チェック マークをクリックします。

	新しいアプリケーションを確認するには、ページを更新する必要がある場合があります。

15. アプリケーションの一覧から、API アプリの認証を有効にしたときに Azure によって作成されたアプリケーション名をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. **[構成]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. **[サインオン URL]** に、AngularJS Web アプリの URL から末尾のスラッシュを除いたものを設定します。

	次に例を示します。https://todolistangular.azurewebsites.net

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. **[応答 URL]** に、Web アプリの URL から末尾のスラッシュを除いたものを設定します。

	次に例を示します。https://todolistsangular.azurewebsites.net

16. **[保存]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. ページ下部の **[マニフェストの管理] > [マニフェストのダウンロード]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. 編集できる場所にファイルをダウンロードします。

16. ダウンロードしたマニフェスト ファイル内で、 `oauth2AllowImplicitFlow` プロパティを検索します。このプロパティの値を `false` から `true` に変更し、ファイルを保存します。

	この設定は、JavaScript のシングル ページ アプリケーションからのアクセスに必要です。これにより、Oauth 2.0 ベアラー トークンが URL フラグメントで返されるようになります。

16. **[マニフェストの管理]、[マニフェストのアップロード]** の順にクリックし、前の手順で更新したファイルをアップロードします。

17. **[クライアント ID]** の値をコピーし、後で取得できる場所に保存します。

## 認証を使用するように ToDoListAngular プロジェクトを構成する

このセクションでは、AngularJS フロントエンドを、ログオンしているユーザーのベアラー トークンを Active Directory Authentication Library (ADAL) for JS を使用して Azure AD から取得するように変更します。次の図で示すように、コードには中間層に送信される HTTP 要求のトークンが含まれます。

![](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

ToDoListAngular プロジェクトのファイルを次のように変更します。

1. *index.html* ファイルを開きます。

2. Active Directory Authentication Library (ADAL) for JS スクリプトを参照している行のコメントを外します。

		<script src="app/scripts/adal.js"></script>
		<script src="app/scripts/adal-angular.js"></script>

1. *app/scripts/app.js* ファイルを開きます。

2. "without authentication" とマークされているコード ブロックをコメント化し、"with authentication" とマークされているコード ブロックのコメントを解除します。

	この変更により、ADAL JS 認証プロバイダーが参照されるようになり、その構成値が提供されます。次の手順では、API アプリと Azure AD アプリケーションの構成値を設定します。

8. `endpoints` 変数を設定するコードで、ToDoListAPI プロジェクトに作成した API アプリの URL を API URL に設定し、Azure クラシック ポータルからコピーしたクライアント ID を Azure AD アプリケーション ID に設定します。

	コードは、現在次の例のようです。

		var endpoints = {
		    "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. `adalProvider.init` の呼び出しで、`tenant` にテナント名を設定し、`clientId` に前のステップで使用したのと同じ値を設定します。

	コードは、現在次の例のようです。

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

	`app.js` に対するこれらの変更では、呼び出し元のコードと呼び出される API の両方が同じ Azure AD アプリケーションに存在していることを指定します。

1. *app/scripts/homeCtrl.js* ファイルを開きます。

2. "without authentication" とマークされているコード ブロックをコメント化し、"with authentication" とマークされているコード ブロックのコメントを解除します。

1. *app/scripts/indexCtrl.js* ファイルを開きます。

2. "without authentication" とマークされているコード ブロックをコメント化し、"with authentication" とマークされているコード ブロックのコメントを解除します。

### ToDoListAngular プロジェクトを Azure にデプロイする

8. **ソリューション エクスプローラー**で ToDoListAngular プロジェクトを右クリックし、**[発行]** をクリックします。

9. **[発行]** をクリックします。

	Visual Studio によってプロジェクトがデプロイされ、Web アプリのベース URL がブラウザーで開きます。

	アプリケーションをテストする前に、中間層 API アプリを変更する必要があります。

10. ブラウザーを閉じます。

## 認証を使用するように ToDoListAPI プロジェクトを構成する

現在、ToDoListAPI プロジェクトは `owner` の値として "*" を ToDoListDataAPI に送信します。このセクションでは、ログオン ユーザーの ID を送信するようにコードを変更します。

ToDoListAPI プロジェクトで次の変更を行います。

1. *Controllers/ToDoListController.cs* ファイルを開き、各アクション メソッドで `owner` を Azure AD の `NameIdentifier` 要求値に設定する行のコメントを解除します。次に例を示します。

		owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

### ToDoListAPI プロジェクトを Azure にデプロイする

8. **ソリューション エクスプローラー**で ToDoListAPI プロジェクトを右クリックし、**[発行]** をクリックします。

9. **[発行]** をクリックします。

	Visual Studio によってプロジェクトがデプロイされ、API アプリのベース URL がブラウザーで開きます。

10. ブラウザーを閉じます。

### アプリケーションをテストする

9. **HTTP ではなく HTTPS を使用して**、Web アプリの URL にアクセスします。

8. **[To Do List]** タブをクリックします。

	ログインを求められます。

9. AAD テナントのユーザー資格情報でログインします。

10. **[To Do List]** ページが表示されます。

	![](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

	これまではすべてが所有者 "*" に対するものであったため、To Do 項目は表示されません。現在、中間層はログオン ユーザーの項目を要求するようになっていますが、まだ何も作成されていません。

11. 新しい To Do 項目を追加し、アプリケーションが動作していることを確認します。

12. 別のブラウザー ウィンドウで、ToDoListDataAPI API の Swagger UI URL に移動し、**[ToDoList] > [Get]** をクリックします。`owner` パラメーターにアスタリスクを入力し、**[Try it out]** をクリックします。

	応答では、新しい To Do 項目に、Owner プロパティの実際の Azure AD ユーザー ID が設定されていることが示されます。

	![](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## 一からのプロジェクトのビルド

**Azure API アプリ** プロジェクト テンプレートを使用し、既定の Values コントローラーを ToDoList コントローラーに置き換えることによって、2 つの Web API プロジェクトを作成しました。

Web API 2 バックエンドで AngularJS 単一ページ アプリケーションを作成する方法については、「[Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js (ハンズオン ラボ: ASP.NET Web API と Angular.js で単一ページ アプリケーション (SPA) を構築する)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs)」を参照してください。Azure AD 認証コードを追加する方法については、次のリソースを参照してください。

* [Azure AD で AngularJS シングル ページ アプリをセキュリティで保護する](../active-directory/active-directory-devquickstarts-angular.md)。
* [Introducing ADAL JS v1 (ADAL JS v1 の概要)](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## トラブルシューティング

認証なしでは正常に動作するアプリケーションが、認証を実装すると動作しなくなる場合、通常は、構成の設定が正しくないか、一貫していないことが問題です。まず、Azure App Service および Azure Active Directory ですべての設定を再確認します。具体的なヒントを次に示します。

* Azure AD の **[構成]** タブで、**[応答 URL]** を再確認します。
* Azure AD でマニフェストをダウンロードし、`oauth2AllowImplicitFlow` が `true` に正しく変更されていることを確認します。 
* AngularJS のソース コードで、中間層 API のアプリ URL および Azure AD クライアント ID を再確認します。
* プロジェクトでコードを構成した後、他のプロジェクトではなくそのプロジェクトを再デプロイしたことを確認します。
* ブラウザーで HTTP URL ではなく HTTPS URL にアクセスしていることを確認します。
* 中間層 API アプリで CORS がまだ有効になっていて、フロントエンド HTTPS URL から中間層への呼び出しが許可されることを確認します。問題が CORS 関連である可能性がある場合は、許可される配信元 URL として "*" を試してみます。
* [customErrors モードを Off](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview) に設定して、可能な限り多くの情報がエラー メッセージで得られるようにします。
* ブラウザーの開発者ツール コンソールのタブには、多くの場合、さらに多くのエラー情報があり、[ネットワーク] タブでは HTTP 要求を調べることができます。

## 次のステップ

このチュートリアルでは、API アプリで App Service 認証を使用する方法、および ADAL JS ライブラリを使用して API アプリを呼び出す方法を説明しました。次のチュートリアルでは、[サービス間シナリオで API アプリへのアクセスをセキュリティで保護する](app-service-api-dotnet-service-principal-auth.md)方法を学習します。

<!---HONumber=AcomDC_0128_2016-->