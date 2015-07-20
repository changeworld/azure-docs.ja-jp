<properties 
	pageTitle="ユーザーの代理としての SharePoint へのアクセス | モバイル デベロッパー センター" 
	description="ユーザーの代理として SharePoint への呼び出しを行う方法について説明します。" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="mahender"/>

# ユーザーの代理としての SharePoint へのアクセス

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このトピックでは、現在ログインしているユーザーの代理として、SharePoint API にアクセスする方法について説明します。</p>
<p>右側のクリップを見ると、このチュートリアルと同じ手順をビデオで確認できます。ビデオでは、Windows ストア アプリを更新して SharePoint Online とやり取りする手順について、Mat Velloso が説明します。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('http://media.ch9.ms/ch9/f217/3f8cbf94-f36b-4162-b3da-1c00339ff217/AzureMobileServicesAADO365AuthenticationIdentityA_960.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">12:51:00</span></div>
</div>

このチュートリアルでは、「Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証」チュートリアルのアプリケーションを更新し、新しい TodoItem が追加されたときに SharePoint Online で Word 文書を作成するようにします。

このチュートリアルでは、SharePoint への代理アクセスを有効にするための、次の基本的な手順について説明します。

1. [SharePoint への委任アクセスのためにアプリケーションを登録する]
2. [モバイル サービスに SharePoint 情報を追加する]
3. [アクセス トークンを取得し、SharePoint API を呼び出す]
4. [Word 文書を作成し、アップロードする]
5. [アプリケーションをテストする]

このチュートリアルには、次のものが必要です。

* Windows 8.1 で実行されている Visual Studio 2013
* 有効な [SharePoint Online] サブスクリプション
* 「[Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証]」チュートリアルの完了。SharePoint サブスクリプションによって提供されるテナントを使用する必要があります。

## <a name="configure-permissions"></a>SharePoint への委任アクセスのためにアプリケーションを構成する
既定では、AAD から受け取るトークンは、アクセス許可が制限されています。サードパーティのリソースまたは SharePoint Online などの SaaS アプリケーションにアクセスするには、明示的にアクセスを許可する必要があります。

1. **Azure 管理ポータル**の [[Active Directory]] セクションに移動し、テナントを選択します。モバイル サービス用に作成した Web アプリケーションに移動します。

    ![][0]

2. **[構成]** タブで、[他のアプリケーションに対するアクセス許可] セクションまでページを下へスクロールします。**[Office 365 SharePoint Online]** を選択し、**[ユーザーのファイルを編集または削除]** 委任アクセス許可を付与します。その後、**[保存]** をクリックします。

    ![][1]

これで、SharePoint アクセス トークンをモバイル サービスに発行するよう AAD を構成しました。

## <a name="store-credentials"></a>モバイル サービスに SharePoint 情報を追加する

SharePoint への呼び出しを作成するには、モバイル サービスが通信する必要があるエンドポイントを指定する必要があります。また、モバイル サービスの ID も証明できるようにする必要があります。これは、クライアント ID とクライアント シークレットのペアを使用して実行します。AAD ログインのセットアップの間に、既にモバイル サービスのクライアント ID を取得し、保存しています。これらは、機密性の高い資格情報であるため、コードではプレーンテキストで保存しないでください。代わりに、これらの値を Microsoft のモバイル サービスのアプリケーション設定として設定できます。

1. テナントの [AAD アプリケーション] タブに戻り、モバイル サービスの Web アプリケーションを選択します。

2. [構成] で、[キー] まで下へスクロールします。新しいキーを生成すると、クライアント シークレットが取得されます。一度キーを作成してページを離れると、もう一度ポータルからキーを取得できなくなることに注意してください。作成したら、この値をコピーして安全な場所に保存する必要があります。キーの期間を選択してから [保存] をクリックして、作成したキーを完全にコピーします。

    ![][2]

3. 管理ポータルの [Mobile Services] セクションで、[構成] タブに移動し、[アプリ設定] まで下へスクロールします。ここで、キーと値のペアを入力すると、必要な資格情報の参照に役立ちます。

    ![][3]

4. SP_Authority を、AAD テナントの機関のエンドポイントになるよう設定します。これは、クライアント アプリケーションに使用する機関の値と同じにする必要があります。形式は、https://login.windows.net/contoso.onmicrosoft.com のようになります。

5. SP_ClientSecret を、前に取得したクライアント シークレットの値になるよう設定します。

6. SP_SharePointURL を、SharePoint サイトの URL になるよう設定します。形式は、https://contoso-my.sharepoint.com のようになります。

ApiServices.Settings を使用すると、コード内でこれらの値をもう一度取得できます。

## <a name="obtain-token"></a>アクセス トークンを取得し、SharePoint API を呼び出す

SharePoint にアクセスするには、SharePoint を対象ユーザーとする特別なアクセス トークンが必要です。このトークンを取得するには、モバイル サービスの ID とユーザーに発行されたトークンを使用して AAD にコールバックする必要があります。

1. Visual Studio で、Mobile Services バックエンド プロジェクトを開きます。

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../../includes/mobile-services-dotnet-adal-install-nuget.md)]

2. Mobile Services バックエンド プロジェクトで、SharePointUploadContext という名前の新しいクラスを作成します。このクラスで、次のコードを追加します。

        private String accessToken;
        private String mySiteApiPath;
        private String clientId;
        private String clientSecret;
        private String sharepointURL;
        private String authority;
        private const string getFilesPath = "/getfolderbyserverrelativeurl('Documents')/Files";

        public static async Task<SharePointUploadContext> createContext(ServiceUser user, ServiceSettingsDictionary settings)
        {
            //Get the current access token
            AzureActiveDirectoryCredentials creds = (await user.GetIdentitiesAsync()).OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            string userToken = creds.AccessToken;
            return new SharePointUploadContext(userToken, settings);
        }

        private SharePointUploadContext(string userToken, ServiceSettingsDictionary settings)
        {
            //Call ADAL and request a token to SharePoint with the access token
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new ClientCredential(clientId, clientSecret), new UserAssertion(userToken));
            accessToken = ar.AccessToken;
            string upn = ar.UserInfo.UserId;
            mySiteApiPath = "/personal/" + upn.Replace('@','_').Replace('.','_') + "/_api/web"; 
            clientId = settings.AzureActiveDirectoryClientId;
            clientSecret = settings["SP_ClientSecret"];
            sharepointURL = settings["SP_SharePointURL"];
            authority = settings["SP_Authority"];
        }

3. ここで、次のようにユーザーのドキュメント ライブラリにファイルを追加するメソッドを作成します。

        public async Task<bool> UploadDocument(string docName, byte[] document)
        {
            string uploadUri = sharepointURL + mySiteApiPath + getFilesPath + string.Format(@"/Add(url='{0}.docx', overwrite=true)", docName);
            using (HttpClient client = new HttpClient())
            {
                Func<HttpRequestMessage> requestCreator = () =>
                {
                    HttpRequestMessage UploadRequest = new HttpRequestMessage(HttpMethod.Post, uploadUri);
                    UploadRequest.Content = new System.Net.Http.ByteArrayContent(document);
                    UploadRequest.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                    UploadRequest.Content.Headers.ContentType.Parameters.Add(new NameValueHeaderValue("odata", "verbose"));
                    return UploadRequest;
                };
                using (HttpRequestMessage uploadRequest = requestCreator.Invoke())
                {
                    uploadRequest.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
                    HttpResponseMessage uploadResponse = await client.SendAsync(uploadRequest);
                }
            }
            return true;
        }

## <a name="create-document"></a>Word 文書を作成し、アップロードする

Word 文書を作成するには、OpenXML NuGet パッケージを使用します。NuGet マネージャーを開き、DocumentFormat.OpenXml を検索して、このパッケージをインストールします。

1. 次のコードを TodoItemController に追加します。これにより、TodoItem に基づいて Word 文書が作成されます。文書のテキストが、このアイテムの名前になります。

        private static byte[] CreateWordDocument(TodoItem todoItem)
        {
            byte[] document;
            using (MemoryStream generatedDocument = new MemoryStream())
            {
                using (WordprocessingDocument package = WordprocessingDocument.Create(generatedDocument, WordprocessingDocumentType.Document))
                {
                    MainDocumentPart mainPart = package.MainDocumentPart;
                    if (mainPart == null)
                    {
                        mainPart = package.AddMainDocumentPart();
                        new Document(new Body()).Save(mainPart);
                    }
                    Body body = mainPart.Document.Body;
                    Paragraph p =
                        new Paragraph(
                            new Run(
                                new Text(todoItem.Text)));
                    body.Append(p);
                    mainPart.Document.Save();
                }
                document = generatedDocument.ToArray();
            }
            return document;
        }

2. PostTodoItem を次のコードに置き換えます。

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            
            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);
            
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>アプリケーションをテストする

1. 変更をバックエンドに発行し、クライアント アプリケーションを実行します。メッセージが表示されたらログインし、新しい TodoItem を挿入します。

2. SharePoint サイトに移動し、同じユーザーでログインします。

3. [OneDrive] タブをクリックします。[ドキュメント フォルダー] に、GUID タイトルが付いた Word 文書が表示されます。この文書を開くと、TodoItem のテキストがあることがわかります。

    ![][4]


<!-- Images. -->

[0]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-web-application.png
[1]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-sharepoint-permissions.png
[2]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-manage-secret-key.png
[3]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/mobile-services-app-settings-sharepoint.png
[4]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/sharepoint-document-created.png

<!-- Anchors. -->

[SharePoint への委任アクセスのためにアプリケーションを登録する]: #configure-permissionss
[モバイル サービスに SharePoint 情報を追加する]: #store-credentials
[アクセス トークンを取得し、SharePoint API を呼び出す]: #obtain-token
[Word 文書を作成し、アップロードする]: #create-document
[アプリケーションをテストする]: #test-application

<!-- URLs. -->
[[Active Directory]]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/sharepoint/
[Active Directory 認証ライブラリのシングル サインオンによるアプリケーションの認証]: http://azure.microsoft.com/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
 

<!---HONumber=July15_HO2-->