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
	ms.date="11/28/2015"
	ms.author="tdykstra"/>

# Azure App Service での API Apps のサービス プリンシパル認証

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概要

このチュートリアルでは、Azure App Service の認証および承認機能を使用し、API アプリを保護する方法と、保護されている API アプリをサービス アカウントで使用する方法について説明します。このチュートリアルで示した認証プロバイダーは Azure Active Directory です。また、クライアントと API はどちらも、API アプリで実行される ASP.NET Web API です。

## App Service での認証と承認

このチュートリアルで使用している認証機能の概要については、このシリーズの前回のチュートリアル「[Azure App Service での API Apps の認証と承認](app-service-api-authentication.md)」を参照してください。

## このチュートリアルの使い方

このチュートリアルは、[API Apps と ASP.NET の入門シリーズの最初のチュートリアル](app-service-api-dotnet-get-started.md)でダウンロードして API アプリを作成するサンプル アプリケーションに基づいています。

## CompanyUsers.API サンプル プロジェクト

[ContactsList サンプル アプリケーション](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list)の CompanyUsers.API プロジェクトは、ハード コーディングされた連絡先リストを返す Get メソッドを 1 つ含む単純な Web API プロジェクトです。ContactsList.API の Get メソッドが、CompanyContacts.API の Get メソッドを呼び出して、取得した連絡先を独自のデータ ストア内の任意のものに追加し、次いで結合されたリストを返すサービス間シナリオの例を示します。

これが CompanyUsers.API の Get メソッドです。

		public async Task<IEnumerable<Contact>> Get()
		{
		    var contacts = new Contact[]{
		                new Contact { Id = 1, EmailAddress = "nancy@contoso.com", Name = "Nancy Davolio"},
		                new Contact { Id = 2, EmailAddress = "alexander@contoso.com", Name = "Alexander Carson"}
		            };
		
		    return contacts;
		}


また、これが CompanyContacts.API の呼び出し方と、返されたものを結果に追加する ContactsList.API の Get メソッドです。(ここではわかりやすくするために一部のコードは省いています)。

		private async Task<IEnumerable<Contact>> GetContacts()
		{
		    var contacts = await _storage.Get(FILENAME);
		
		    var contactsList = contacts.ToList<Contact>();
		    using (var client = CompanyContactsAPIClientWithAuth())
		    {
		        var results = await client.Contacts.GetAsync();
		        foreach (Contact c in results)
		        {
		            contactsList.Add(c);
		        }
		    }
		
		    return contactsList;
		}

上のコードで `CompanyContactsAPIClientWithAuth()` から返されるクライアント オブジェクトは、生成されたクライアント コードをベースとしていますが、承認トークンは HTTP 要求に追加します。

		private static CompanyContactsAPI CompanyContactsAPIClientWithAuth()
		{
		    var client = new CompanyContactsAPI(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		    client.HttpClient.DefaultRequestHeaders.Authorization =
		        new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		    return client;
		}

## Azure で作成した API アプリへの CompanyContacts.API プロジェクトのデプロイ

1. **ソリューション エクスプローラー**で CompanyContacts.API プロジェクトを右クリックし、**[発行]** をクリックします。

3.  **Web の発行**ウィザードの **[プロファイル]** ステップで、**[Microsoft Azure App Service]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/selectappservice.png)

4. まだ行っていない場合は Azure アカウントにサインインし、有効期限が切れている場合は資格情報を更新します。

4. **[App Service]** ダイアログ ボックスで、使用する Azure **サブスクリプション**を選択して、**[新規]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. **[App Service の作成]** ダイアログ ボックスの **[ホスティング]** タブで、**[種類の変更]**、**[API アプリ]** を順にクリックします。

4. *azurewebsites.net* ドメインに固有の **API アプリ名**を入力します。

6. **[リソース グループ]** ドロップダウン リストから、これらのチュートリアルで使用しているリソース グループを選択します。

4. **[App Service プラン]** ドロップダウン リストから、これらのチュートリアルで使用しているプランを選択します。

7. **[作成]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/createappservice.png)

	Visual Studio によって API アプリが作成され、新しい API アプリに必要なすべての設定が含まれる発行プロファイルが作成されます。

8. **Web の発行**ウィザードの **[接続]** タブで、**[発行]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/conntab.png)

	Visual Studio によって、プロジェクトに新しい API アプリがデプロイされ、ブラウザーで API アプリの URL が開きます。ブラウザーに "正常に作成されました" ページが表示されます。

9. ブラウザーを閉じます。

## ContactsList.API プロジェクトでの生成されたクライアント コードの更新

ContactsList.API プロジェクトには既に生成済みのクライアント コードがありますが、それを削除して、独自の API アプリから再生成します。

1. Visual Studio の**ソリューション エクスプローラー**の ContactsList.API プロジェクトで、*CompanyContactsAPI* フォルダーを削除します。

2. ContactsList.API プロジェクトを右クリックし、**[追加]、[REST API クライアント]** を順にクリックします。

3. **[REST API クライアントの追加]** ダイアログ ボックスで、**[Microsoft Azure API アプリからダウンロード]**、**[参照]** を順にクリックします。

8. **[App Service]** ダイアログ ボックスで、このチュートリアルで使用しているリソース グループを展開して、先ほど作成した API アプリを選択します。

	API アプリが一覧に表示されない場合、API アプリを作成するときに、アプリの種類を (Web アプリから API アプリに) 変更する手順を誤って省略した可能性があります。その場合は、先行する手順をやり直して新しい API アプリを作成してください。API アプリに別の名前を選択する必要があります。または、ポータルに移動して先に Web アプリを削除しておいてください。

10. **[OK]** をクリックします。

9. **[REST API クライアントの追加]** ダイアログ ボックスで **[OK]** をクリックします。

	Visual Studio が API アプリを元に命名したフォルダーを作成し、クライアント クラスを生成します。

## ContactsList.API のコードの更新とプロジェクトのデプロイ

前のチュートリアルで、CompanyContacts.API を呼び出す ContactsList.API 内のコードはコメント アウトされています。このセクションでは、そのコードのコメントを解除し、アプリをデプロイします。

1. ContactsList.API プロジェクトで *Controllers/ContactsController.cs* を開きます。

2. `ContactsController` クラス上部近くの、生成されたクライアント クラスを使用する認証トークンを追加するコードで、クラス名 `CompanyContactsAPI` を API アプリで生成したクラスの名前に置き換えます。

	たとえば、API アプリの名前が CompanyContactsAPI3 の場合、コードは次のようになります。

		 private static CompanyContactsAPI3 CompanyContactsAPIClientWithAuth()
		 {
		     var client = new CompanyContactsAPI3(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		     client.HttpClient.DefaultRequestHeaders.Authorization =
		         new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		     return client;
		 }
 
4. `Get` メソッドで、CompanyContacts.API を呼び出すコード ブロックのコメントを解除します。

		using (var client = CompanyContactsAPIClientWithAuth())
		{
		    var results = await client.Contacts.GetAsync();
		    foreach (Contact c in results)
		    {
		        contactsList.Add(c);
		    }
		}

2. ContactsList.API プロジェクトを右クリックし、**[発行]** をクリックします。

	**Web の発行**ウィザードが開き、以前に使用したプロファイルが発行されます。

3. **Web の発行**ウィザードで、**[発行]** をクリックします。

	Visual Studio によってプロジェクトがデプロイされ、API アプリのベース URL がブラウザー ウィンドウで開きます。このブラウザー ウィンドウを閉じます。

## Azure での新しい API アプリの認証と承認の設定

1. [Azure ポータル](https://portal.azure.com/)で、このチュートリアルで CompanyContacts.API プロジェクト用に作成した API アプリの API App ブレードに移動し、**[設定]** をクリックします。

2. **[機能]** セクションを探し **[認証/承認]** をクリックします。

3. **[認証/承認]** ブレードで、**[オン]** をクリックします。

4. **[要求が認証されていない場合のアクション]** ボックスの一覧の **[Azure Active Directory でログイン]** を選択します。

5. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。

6. **[Azure Active Directory 設定]** ブレードで **[Express]** をクリックします。

	Azure が AAD テナントに AAD アプリケーションを自動的に作成します。クライアント ID を取得するために Azure クラシック ポータルに後で移動して選択するために、新しい AAD アプリケーションの名前をメモします。

7. **[OK]** をクリックします。

10. **[認証/承認]** ブレードで、**[保存]** をクリックします。
 
11. [Azure クラシック ポータル](https://manage.windowsazure.com/)で **[Azure Active Directory]** に移動します。

12. **[ディレクトリ]** タブで AAD テナントをクリックします。

14. **[アプリケーション]、[自分の会社が所有するアプリケーション]** を順にクリックし、チェック マークをオンにします。

15. アプリケーションの一覧から、API アプリの認証を有効にしたときに Azure によって作成されたアプリケーション名をクリックします。

16. **[構成]** をクリックします。

17. チュートリアルの後の手順で、値をコピーして貼り付けることができるよう、このページは開いたままにします。

## ContactsList.API プロジェクト コードを実行する API アプリの設定の更新

3. 別のブラウザー ウィンドウで [Azure クラシック ポータル](https://manage.windowsazure.com/)に移動し、ContactsList.API API アプリ用に作成した AAD アプリケーションの **[構成]** タブに移動します。

5. **[キー]** の **[時間の選択]** ドロップダウン リストで **[1 年間]** を選択します。

6. **[保存]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. キー値をコピーします。

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

1. 別のブラウザー ウィンドウで [Azure ポータル](https://portal.azure.com/)に移動し、ContactsList.API プロジェクトをデプロイした API アプリ用の API アプリ ブレードに移動します。(これは呼び出し先ではなく呼び出し元の API アプリです。つまり、ContactsList.API であって、CompanyContacts.API ではありません。)

2. **[設定]、[アプリケーションの設定]** の順にクリックします。

3. **[アプリケーション設定]** セクションで "ida:ClientSecret" という名前のキーを追加し、[値] フィールドに作成したキーを貼り付けます。

3. "ida:ClientId" という名前のキーを追加し、その値フィールドに、同じ AAD **[構成]** ページからコピーしたクライアント ID を貼り付けます。

4. "ida:Authority" という名前のキーを追加し、その値フィールドに「https://login.windows.net/{該当するテナント}」と入力します (例: https://login.windows.net/contoso.onmicrosoft.com)。

3. Azure クラシック ポータルで、CompanyContacts.API API アプリ用に作成した AAD アプリケーションの **[構成]** タブに移動します。

4. クライアント ID をコピーします。

3. Azure ポータルの **[アプリケーションの設定]** ブレードの **[アプリケーション設定]** セクションで ida:Resource という名前のキーを追加し、[値] フィールドにコピーしたクライアント ID を貼り付けます。

4. "CompanyContactsAPIUrl" という名前のキーを追加し、その値フィールドに「https://{API アプリ名}.azurewebsites.net」と入力します (例: https://companycontactsapi.azurewebsites.net)。

6. [保存] をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Azure でのテスト

1. ContactsList.Angular.AAD プロジェクトをデプロイした Web アプリの URL に移動します。

2. **[連絡先]** タブをクリックし、ログインします。

	CompanyContacts.API API アプリから取得した追加の連絡先が含まれる [連絡先] ページが開きます。

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

前のチュートリアルと同様、この Visual Studio プロジェクトは localhost の SSL URL でセットアップし、アプリケーションをローカルで実行することもできます。その場合、Azure での実行を想定して Azure に保存した設定 (クライアント ID、クライアント シークレットなど) は、Web.config ファイルに保存できます。ただし、クライアント シークレットなどの機密情報を含んだ Web.config ファイルをソース管理にチェックインしないよう注意してください。詳細については、「[Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service (ASP.NET および Azure App Service にパスワードや機密データをデプロイするためのベスト プラクティス)](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)」を参照してください。

## ブラウザー アクセスから API アプリを保護する

このチュートリアルでは、サービス プリンシパル認証を使ってアクセスする API アプリに対し、Azure ポータルの [Express] オプションを使用して AAD 認証を設定しました。既定では、ブラウザーから API アプリの URL にアクセスしてログオンすることをユーザーに許可するような構成が、App Service によって新しい AAD アプリケーションに適用されます。つまり、サービス プリンシパルだけでなくエンド ユーザーも API にアクセスすることができます。API へのアクセスをサービス プリンシパルに限定する必要がある場合は、AAD アプリケーションの **[応答 URL]** を API アプリのベース URL とは異なる値に変更して、ブラウザー アクセスを無効にしてください。

### ブラウザー アクセスが有効であることを確認する

1. 新しいブラウザー ウィンドウから、CompanyContacts.API プロジェクト用に作成した API アプリの HTTPS URL にアクセスします。

	ブラウザーにログイン画面が表示されます。
	
2. AAD テナント内のユーザーの資格情報を使用してログインします。

3. 正常に作成されたことを示す API アプリの画面がブラウザーに表示されます。

	Swagger UI が有効になっていれば、`/swagger` URL にアクセスして、API を呼び出すことができます。この URL に `/api/contacts/get` を追加することによって、ブラウザーから API を呼び出すことができます。

### ブラウザー アクセスを無効にする

1. クラシック ポータルの **[構成]** タブで、CompanyContacts.API プロジェクト用に作成した AAD アプリケーションの **[応答 URL]** フィールドの値を、API アプリの URL 以外の有効な URL に変更します。
 
2. **[保存]** をクリックします。

### ブラウザー アクセスが無効になったことを確認する

1. 新しいブラウザー ウィンドウで、API アプリの URL にもう一度アクセスします。

2. ログインを求められたらログインします。

3. ログインは成功しますが、エラー ページにリダイレクトされます。

	サービス プリンシパルのトークンを使って API アプリにアクセスすることはできますが、AAD テナントのユーザーがブラウザーからログインして API を利用することはできません。

## 次のステップ

これは、API Apps の概要シリーズの最終のチュートリアルです。このセクションでは、API アプリを使用する方法に関するその他の推奨事項を示します。

* App Service アプリをデプロイするその他の方法

	Visual Studio の使用、または[ソース管理システム](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)からの[デプロイの自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)による Web プロジェクトの Web アプリへのデプロイの他の方法については、[Azure Web アプリをデプロイする方法](web-sites-deploy.md)に関するページを参照してください。

	Visual Studio により、デプロイを自動化するために使用可能な Windows PowerShell スクリプトも生成できます。詳細については、「[Automate Everything (Building Real-World Cloud Apps with Azure) (すべてを自動化 - Azure での実際のクラウド アプリケーションのビルド)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)」を参照してください。

* App Service アプリのトラブルシューティングの方法

	Visual Studio には、リアルタイムで生成される Azure ログの表示を容易にする機能があります。Azure では、リモートでデバッグ モードを使用して実行することもできます。詳細については、[Visual Studio での Azure Web アプリのトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)に関するページを参照してください。

* カスタム ドメイン名および SSL の追加方法

	SSL および独自のドメイン (contoso.azurewebsites.net の代わりに www.contoso.com など) の使用方法については、次のリソースを参照してください。

	* [Azure App Service のカスタム ドメイン名の構成](web-sites-custom-domain-name.md)
	* [Azure の Web サイトでの HTTPS の有効化](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1217_2015-->