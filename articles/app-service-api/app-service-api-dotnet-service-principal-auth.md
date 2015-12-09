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

このチュートリアルでは、Azure App Service の認証および承認機能を使用し、API アプリを保護する方法と、保護されている API アプリをサービス アカウントで使用する方法について説明します。サービス アカウントは*サービス プリンシパル*とも呼ばれ、このようなアカウントでの認証は、*サービス間*シナリオとも呼ばれます。このチュートリアルでは、認証および .NET クライアントからの API の使用に Azure Active Directory を使用し、サービス間シナリオで API アプリを保護します。

このチュートリアルでは、呼び出すクライアントと呼び出された API の両方で ASP.NET Web API を使用していますが、示しているテクニックは、Azure App Service でサポートされている他の言語やフレームワークでも使用できます。ここに示すクライアント コードは、サービス アカウント用のベアラー トークンを取得して渡すための標準の Azure Active Directory コードです。Mobile Services Zumo トークンの処理で使用されていたような Azure 専用の特殊なコードは不要です。

これは、Azure App Service で API アプリを使用する方法を示すチュートリアルのシリーズの 4 回目です。このシリーズの詳細については、1 回目のチュートリアルの「[Azure App Service で API Apps と ASP.NET を使ってみる](app-service-api-dotnet-get-started.md)」を参照してください。Azure App Service での認証と承認については、シリーズの前のチュートリアルの「[Azure App Service での API Apps のユーザー認証](app-service-api-dotnet-user-principal-auth.md)」を参照してください。

## サービス間認証のその他のオプション

クライアントの証明書を使用する場合など、App Service 認証および承認を使用せずにサービス間シナリオを処理する場合、「[次のステップ](#next-steps)」セクションを参照してください。

## CompanyUsers.API サンプル プロジェクト

このチュートリアルでは、[このシリーズの 1 回目のチュートリアル](app-service-api-dotnet-get-started.md)でダウンロードしたサンプル プロジェクトと、前のチュートリアルで作成した Azure リソース (API アプリと Web アプリ) を使用します。

CompanyUsers.API プロジェクトは、ハード コーディングされた連絡先リストを返す Get メソッドを 1 つ含む単純な Web API プロジェクトです。ContactsList.API の Get メソッドが、CompanyContacts.API の Get メソッドを呼び出して、取得した連絡先を独自のデータ ストア内の任意のものに追加し、次いで結合されたリストを返すサービス間シナリオの例を示します。

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

CompanyContacts.API のクライアント オブジェクトは、HTTP 要求にトークンを追加する、生成された API アプリのクライアント コードの変更です。

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

4. [App Service] ダイアログ ボックスで、使用する Azure **サブスクリプション**を選択して、**[新規]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. **[App Service の作成]** ダイアログ ボックスの **[ホスティング]** タブで、**[種類の変更]**、**[API App]** を順にクリックします。

4. *azurewebsites.net* ドメインに固有の **API App 名** を入力します。

6. **[リソース グループ]** ドロップダウン リストから、これらのチュートリアルで使用しているリソース グループを選択します。

4. **[App Service プラン]** ドロップダウン リストから、これらのチュートリアルで使用しているプランを選択します。

7. **[作成]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/createappservice.png)

	Visual Studio によって API アプリが作成され、新しい API アプリに必要なすべての設定が含まれる発行プロファイルが作成されます。

8. **Web の発行**ウィザードの **[発行]** タブで、**[次へ]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/conntab.png)

	Visual Studio によって、プロジェクトに新しい API アプリがデプロイされ、ブラウザーで API アプリの URL が開きます。ブラウザーに "正常に作成されました" ページが表示されます。

9. ブラウザーを閉じます。

## ContactsList.API プロジェクトでの生成されたクライアント コードの更新

ContactsList.API プロジェクトには既に生成済みのクライアント コードがありますが、それを削除して、独自の API アプリから再生成します。

1. Visual Studio の**ソリューション エクスプローラー**の ContactsList.API プロジェクトで、*CompanyContactsAPI* フォルダーを削除します。

2. ContactsList.API プロジェクトを右クリックし、**[追加]、[REST API クライアント]** を順にクリックします。

3. **[REST API クライアントの追加]** ダイアログ ボックスで、**[Microsoft Azure API アプリからダウンロード]**、**[参照]** を順にクリックします。

8. **[App Service]** ダイアログ ボックスで、このチュートリアルで使用しているリソース グループを展開して、先ほど作成した API アプリを選択します。

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
 
4. Get メソッド内で `CompanyContactsAPIClientWithAuth` で返されるクライアント オブジェクトを使用するコードのブロックをコメント解除します。

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

## Azure での新しい API アプリの認証と承認の設定

1. [Azure ポータル](https://portal.azure.com/)で、このチュートリアルで CompanyContacts.API プロジェクト用に作成した API アプリの API App ブレードに移動し、**[設定]** をクリックします。

2. **[機能]** セクションを探し **[認証/承認]** をクリックします。

3. **認証/承認**ブレードで、**[オン]** をクリックします。

4. **[要求が認証されていない場合のアクション]** ドロップダウン リストで、**[Azure Active Directory でログイン]** を選択します。

5. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。

6. **Azure Active Directory 設定**ブレードで **[Express]** をクリックします。

	Azure が AAD テナントに AAD アプリケーションを自動的に作成します。クライアント ID を取得するために Azure クラシック ポータルに後で移動して選択するために、新しい AAD アプリケーションの名前をメモします。

7. **[OK]** をクリックします。

10. **認証/承認**ブレードで、**[保存]** をクリックします。
 
11. [Azure クラシック ポータル](https://manage.windowsazure.com/)で **[Azure Active Directory]** に進みます。

12. **[ディレクトリ]** タブで AAD テナントをクリックします。

14. **[アプリケーション]、[自分の会社が所有するアプリケーション]** を順にクリックし、チェック マークをオンにします。

15. アプリケーションの一覧から、API アプリの認証を有効にしたときに Azure によって作成されたアプリケーション名をクリックします。

16. **[構成]** をクリックします。

15. ページ下部で **[マニフェストの管理]、[マニフェストのダウンロード]** をクリックし、編集できる場所にファイルを保存します。

16. ダウンロードしたマニフェスト ファイル内で、 `oauth2AllowImplicitFlow` プロパティを検索します。このプロパティの値を `false` から `true` に変更し、ファイルを保存します。

16. **[マニフェストの管理]、[マニフェストのアップロード]** をクリックし、前の手順で更新したファイルをアップロードします。

17. チュートリアルの後の手順で、値をコピーして貼り付けることができるよう、このページは開いたままにします。

## ContactsList.API プロジェクト コードを実行する API アプリの設定の更新

1. [Azure ポータル](https://portal.azure.com/)で ContactsList.API プロジェクトをデプロイした API アプリ用の API アプリ ブレードに移動します。これは、このチュートリアルで作成したばかりの呼び出される API アプリではなく、呼び出し元の API アプリです。)

2. **[設定]、[アプリケーションの設定]** の順にクリックします。

	ここではいくつかの設定を追加しますが、Azure クラシック ポータルの別のページからそれを取得する必要があります。

3. [Azure クラシック ポータル](https://manage.windowsazure.com/)から ContactsList.API API アプリ用に作成した AAD アプリケーションの **[構成]** タブに移動します。

5. **[キー]** の **[時間の選択]** ドロップダウン リストで **[1 年間]** を選択します。

6. **[保存]** をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)


7. キー値をコピーします。

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

3. Azure ポータルの **アプリケーションの設定**ブレードの **[アプリケーション設定]** セクションで ida: ClientSecret という名前のキーを追加し、[値] フィールドに作成したキーを貼り付けます。

3. 従来の Azure ポータルで、CompanyContacts.API API アプリ用に作成した AAD アプリケーションの **[構成]** タブに移動します。

4. クライアント ID をコピーします。

3. Azure ポータルの **アプリケーションの設定**ブレードの **[アプリケーション設定]** セクションで ida:Resource という名前のキーを追加し、[値] フィールドにコピーしたクライアント ID を貼り付けます。

4. CompanyContactsAPIUrl という名前のキーを追加し、[値] フィールドに、https://{your api app name}.azurewebsites.net と入力します (例: https://companycontactsapi.azurewebsites.net)。

6. [保存] をクリックします。

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Azure でのテスト

1. ContactsList.Angular.AAD プロジェクトをデプロイした Web アプリの URL に移動します。

2. **[連絡先**] タブをクリックし、ログインします。

	CompanyContacts.API API アプリから取得した追加の連絡先が含まれる [連絡先] ページが開きます。

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

## 次のステップ

これは、API Apps の概要シリーズの最終のチュートリアルです。このセクションでは、API アプリを使用する方法に関するその他の推奨事項を示します。

* Azure App Service の認証と承認によって保護されている API アプリを使用するその他の方法。

	この記事では、API アプリを保護する方法、および別の API アプリで実行されているコードから呼び出す方法を説明しています。ロジック アプリから保護されている API アプリを呼び出す方法については、「[App Service でホストされたカスタム API のロジック アプリでの使用](../app-service-logic/app-service-logic-custom-hosted-api.md)」を参照してください。

* サービス間シナリオで API アプリを保護する方法

	API アプリを保護する App Service 認証と承認の代わりに、クライアント証明書または基本認証を使用できます。Azure のクライアント証明書の詳細については、「[Web Apps の TLS 相互認証を構成する方法](../app-service-web/app-service-web-configure-tls-mutual-auth.md)」を参照してください。

* App Service アプリをデプロイするその他の方法

	Visual Studio の使用、または[ソース管理システム](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)からの[デプロイメントの自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)による Web プロジェクトの Web アプリへのデプロイの他の方法については、[Azure Web アプリをデプロイする方法](web-sites-deploy.md)に関するページを参照してください。

	Visual Studio により、デプロイを自動化するために使用可能な Windows PowerShell スクリプトも生成できます。詳細については、「[Automate Everything (Building Real-World Cloud Apps with Azure) (すべてを自動化 - Azure での実際のクラウド アプリケーションのビルド)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)」を参照してください。

* App Service アプリのトラブルシューティングの方法

	Visual Studio には、リアルタイムで生成される Azure ログの表示を容易にする機能があります。Azure では、リモートでデバッグ モードを使用して実行することもできます。詳細については、[Visual Studio での Azure Web アプリのトラブルシューティング](web-sites-dotnet-troubleshoot-visual-studio.md)に関するページを参照してください。

* カスタム ドメイン名および SSL の追加方法

	SSL および独自のドメイン (contoso.azurewebsites.net の代わりに www.contoso.com など) の使用方法については、次のリソースを参照してください。

	* [Azure App Service のカスタム ドメイン名の構成](web-sites-custom-domain-name.md)
	* [Azure の Web サイトでの HTTPS の有効化](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1203_2015-->