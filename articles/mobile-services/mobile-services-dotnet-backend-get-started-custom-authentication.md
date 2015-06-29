<properties 
	pageTitle="カスタム認証の使用 | モバイル デベロッパー センター" 
	description="ユーザー名とパスワードを使用してユーザーを認証する方法について説明します。" 
	documentationCenter="Mobile" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="mahender"/>

# カスタム認証の使用

## 概要
このトピックでは、独自の Mobile Services 認証トークンを発行して、Azure Mobile Services .NET バックエンドでユーザーを認証する方法について説明します。このチュートリアルでは、アプリケーションのカスタム ユーザー名とパスワードを使用して、クイック スタート プロジェクトに認証を追加します。

>[AZURE.NOTE]このチュートリアルでは、カスタムの資格情報を使用して Mobile Services を認証する高度な方法を説明します。多くのアプリケーションでは、代わりに組み込みのソーシャル ID プロバイダーを使用する方法が最適であり、ユーザーは Facebook、Twitter、Google、Microsoft アカウント、および Azure Active Directory を介してログインすることができます。Mobile Services の認証を初めて使用する場合は、「[アプリへの認証の追加]」チュートリアルを参照してください。

このチュートリアルは、Mobile Services のクイック スタートに基づいています。先にチュートリアル「[Mobile Services の使用]」を完了している必要があります。

>[AZURE.IMPORTANT]このチュートリアルの目的は、Mobile Services の認証トークンを発行する方法を説明することです。このチュートリアルは、セキュリティに関するガイダンスを示すものではありません。アプリケーションを開発する際には、パスワードの保存がセキュリティに及ぼす影響について認識し、ブルート フォース攻撃に対応する戦略を用意する必要があります。

## アカウント テーブルを設定する

別の ID プロバイダーを使用しないでカスタム認証を使用するため、ユーザーのサインイン情報を保存する必要があります。このセクションでは、アカウント用のテーブルを作成し、基本的なセキュリティ メカニズムを設定します。アカウント テーブルには、ユーザー名と、ソルトを使用してハッシュ化されたパスワードが保存されます。また、必要に応じて追加のユーザー情報も保存できます。

1. バックエンド プロジェクトの **DataObjects** フォルダーで、`Account` という名前の新しいエンティティを追加します。

2. 次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.Mobile.Service;  

3. クラス定義を次のコードで置き換えます。

	    public class Account : EntityData
	    {
	        public string Username { get; set; }
	        public byte[] Salt { get; set; }
	        public byte[] SaltedAndHashedPassword { get; set; }
	    }
    
    これは新しい Account テーブルの行を表し、ユーザー名、そのユーザーのソルト、および安全に格納されているパスワードを表します。

2. **Models** フォルダーには、モバイル サービスの名前が付けられた **DbContext** 派生クラスがあります。コンテキストを開き、次のコードを含めて、データ モデルにアカウント テーブルを追加します。

        public DbSet<Account> Accounts { get; set; }

	>[AZURE.NOTE]このチュートリアルのコード スニペットでは、コンテキスト名として `todoContext` を使用します。コード スニペットは、実際のプロジェクトのコンテキストに合わせて更新する必要があります。そのうえで、このデータの取り扱いに必要なセキュリティ機能を設定します。
 
5. `CustomLoginProviderUtils` という名前のクラスを作成し、次の `using` ステートメントを追加します。

		using System.Security.Cryptography;

6. 次のコードのメソッドを新しいクラスに追加します。


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }

	これにより、新しい長いソルトを生成し、ソルトを使用したパスワードをハッシュ化する機能を追加し、2 つのハッシュを比較する安全な方法を提供できます。

## 登録エンドポイントを作成する

この時点で、ユーザー アカウントの作成を開始するのに必要なものはすべて揃いました。このセクションでは、登録エンドポイントを設定して、新しい登録の要求を処理します。ここで新しいユーザー名ポリシーとパスワード ポリシーを適用して、ユーザー名が取得されないようにします。その後、データベースにユーザー情報を安全に保存します。

1. 受信する登録要求を表す次のような新しいクラスを作成します。

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    登録時にその他の情報を収集して保存する必要がある場合は、ここで行う必要があります。

2. モバイル サービスのバックエンド プロジェクトで、**[コントローラー]** を右クリックし、**[追加]**、**[コントローラー]** の順にクリックして、`CustomRegistrationController` という名前の新しい **Microsoft Azure Mobile Services カスタム コント ローラー**を作成した後、次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Text.RegularExpressions;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

	上記のコードで、プレースホルダーを実際のプロジェクトの名前空間に置き換えます。
 
4. クラス定義を次のコードで置き換えます。

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomRegistrationController : ApiController
	    {
	        public ApiServices Services { get; set; }
	
	        // POST api/CustomRegistration
	        public HttpResponseMessage Post(RegistrationRequest registrationRequest)
	        {
	            if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
	            }
	            else if (registrationRequest.password.Length < 8)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
	            }
	
	            todoContext context = new todoContext();
	            Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "That username already exists.");
	            }
	            else
	            {
	                byte[] salt = CustomLoginProviderUtils.generateSalt();
	                Account newAccount = new Account
	                {
	                    Id = Guid.NewGuid().ToString(),
	                    Username = registrationRequest.username,
	                    Salt = salt,
	                    SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
	                };
	                context.Accounts.Add(newAccount);
	                context.SaveChanges();
	                return this.Request.CreateResponse(HttpStatusCode.Created);
	            }
	        }
	    }   

    忘れずに、*todoContext* 変数をプロジェクトの **DbContext** の名前に置き換えます。このコントローラーは、次の属性を使用してこのエンドポイントへのすべてのトラフィックを許可することに注意してください。

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]この登録エンドポイントは、HTTP 経由で任意のクライアントからアクセスできます。このサービスを運用環境に発行する前に、SMS や電子メール ベースの検証などの登録を検証するためのスキーマの一種を実装する必要があります。これにより、悪意のあるユーザーが不正な登録を作成することを防ぐことができます。

## LoginProvider を作成する

Mobile Services 認証パイプラインの基本的なコンストラクトの 1 つに **LoginProvider** があります。このセクションでは、独自の `CustomLoginProvider` を作成します。これは、組み込みのプロバイダーのようにパイプラインにプラグインされるのではなく、いくつかの便利な機能を提供します。

1. **LoginProvider** の新しい派生クラス `CustomLoginProvider` を作成し、次の `using` ステートメントを追加します。

	    using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Newtonsoft.Json.Linq;
		using Owin;
		using System.Security.Claims;
 
3. **CustomLoginProvider** クラスの定義を、次のコードに置き換えます。

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       ここでプロジェクトをビルドしようとすると失敗します。`LoginProvider` の 3 つの抽象メソッドを実装する必要があります。これは後で行います。

2. `CustomLoginProviderCredentials` という名前の新しいクラスを同じコード ファイルに作成します。

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

	これは、ユーザーに関する情報を表し、[GetIdentitiesAsync](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.serviceuser.getidentitiesasync.aspx) を介してバックエンドで利用可能になります。カスタム クレームを追加する場合は、このオブジェクトで取得されることを確認します。

3. 抽象メソッド `ConfigureMiddleware` の次の実装を、**CustomLoginProvider** に追加します。

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

	**CustomLoginProvider** を認証パイプラインに統合しないため、このメソッドは実装しません。

4. 抽象メソッド `ParseCredentials` の次の実装を、**CustomLoginProvider** に追加します。

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

	このメソッドは、受信する認証トークンのユーザー情報をバックエンドが逆シリアル化するのを許可します。

5. 抽象メソッド `CreateCredentials` の次の実装を、**CustomLoginProvider** に追加します。

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

	このメソッドは、[ClaimsIdentity] を認証トークン発行フェーズで使用される [ProviderCredentials] オブジェクトに変換します。このメソッドでは、追加のクレームがあれば、もう一度取得できます。
	
6. App_Start フォルダーにある WebApiConfig.cs プロジェクト ファイルを開くと、**ConfigOptions** の後に次のコード行が作成されています。
		
		options.LoginProviders.Add(typeof(CustomLoginProvider));

	

## サインイン エンドポイントを作成する

次に、ユーザーがサインインするためのエンドポイントを作成します。受信するユーザー名とパスワードをデータベースに対して確認するために、最初にユーザーのソルトを適用し、パスワードをハッシュ化し、受信する値とデータベースの値が一致することを確認します。一致している場合は、[ClaimsIdentity] を作成し、**CustomLoginProvider** に渡します。クライアント アプリケーションがユーザー ID と認証アクセス トークンを受信して、モバイル サービスにさらにアクセスできます。

1. モバイル サービス バックエンド プロジェクトで、次の新しい `LoginRequest` クラスを作成します。

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

	このクラスは、着信サインインの試行を表します。

2. 次の新しい `CustomLoginResult` クラスを作成します。

	    public class CustomLoginResult
	    {
	        public string UserId { get; set; }
	        public string MobileServiceAuthenticationToken { get; set; }
	
	    }

	このクラスは、正常なログインとユーザー ID および認証トークンを表します。このクラスはクライアントの MobileServiceUser クラスと同じ形であり、それによって厳密に型指定されたクライアントでのログイン応答の処理が簡単になることに注意してください。

2. **[コントローラー]** を右クリックし、**[追加]**、**[コントローラー]** の順にクリックして、`CustomLoginController` という名前の新しい **Microsoft Azure Mobile Services カスタム コント ローラー**を作成した後、次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Security.Claims;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

3. **CustomLoginController** クラスの定義を、次のコードに置き換えます。
 
	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomLoginController : ApiController
	    {
	        public ApiServices Services { get; set; }
	        public IServiceTokenHandler handler { get; set; }
	
	        // POST api/CustomLogin
	        public HttpResponseMessage Post(LoginRequest loginRequest)
	        {
	            todoContext context = new todoContext();
	            Account account = context.Accounts
	                .Where(a => a.Username == loginRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                byte[] incoming = CustomLoginProviderUtils
	                    .hash(loginRequest.password, account.Salt);
	
	                if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
	                {
	                    ClaimsIdentity claimsIdentity = new ClaimsIdentity();
	                    claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
	                    LoginResult loginResult = new CustomLoginProvider(handler)
	                        .CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
	                    var customLoginResult = new CustomLoginResult()
	                    {
	                        UserId = loginResult.User.UserId,
	                        MobileServiceAuthenticationToken = loginResult.AuthenticationToken
	                    };
	                    return this.Request.CreateResponse(HttpStatusCode.OK, customLoginResult);
	                }
	            }
	            return this.Request.CreateResponse(HttpStatusCode.Unauthorized,
	                "Invalid username or password");
	        }
	    }

       忘れずに、*todoContext* 変数をプロジェクトの **DbContext** の名前に置き換えます。このコントローラーは、次の属性を使用してこのエンドポイントへのすべてのトラフィックを許可することに注意してください。

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]運用で使用する `CustomLoginController` には、ブルート フォース攻撃を検出する戦略を盛り込む必要もあります。そうしない場合、作成したサインイン ソリューションが攻撃に対して脆弱になる可能性があります。

## 認証を要求するようにモバイル サービスを構成する

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## テスト クライアントを使用してサインイン フローをテストする

クライアント アプリケーションで、カスタム サインイン画面を開発する必要があります。この画面では、ユーザー名とパスワードを取得して、登録エンドポイントとサインイン エンドポイントに JSON ペイロードとして送信します。このチュートリアルを完了するには、代わりに Mobile Services .NET バックエンド用の組み込みのテスト クライアントを使用します。

1. Visual Studio で、モバイル サービス プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順にクリックします。  

	これにより、モバイル サービス バックエンド プロジェクトの新しいデバッグ インスタンスが開始します。サービスが正常に開始した後、**このモバイル サービスは稼働している**という開始ページが表示されます。

2. サービスの開始ページで、**[試してみる]** をクリックし、web.config ファイルで **MS_ApplicationKey** アプリケーション設定に対して設定したパスワードと空のユーザー名を認証ダイアログ ボックスに入力します。

3. ヘルプ ページで、**CustomRegistration** エンドポイントをクリックし、**[試してみる]** をクリックします。

    ![][2]

4. 本文で、以前指定した基準に合うユーザー名とパスワードにサンプル文字列を置き換えて、**[送信]** をクリックします。

    ![][3]

	応答は **"201/作成済み"**になります。

5. ブラウザーの戻るボタンをクリックし、前の手順で登録したものと同じユーザー名とパスワードを使用して **CustomLogin** エンドポイントに対して手順 2 と 3 を繰り返します。

    ![][4]

	*userId* と *authenticationToken* の両方を含む **user** JSON オブジェクトが本文に含まれる応答メッセージを受け取ります。これは、カスタム認証によって生成された Mobile Services 認証トークンです。このトークンは、TodoItem エンドポイントへのアクセスをクライアント アプリに許可するのに十分です。

	*authenticationToken* 値のコピーを作成します。これを使用して、制限付きの TodoItem エンドポイントにアクセスします。

6. ブラウザーの戻るボタンをクリックし、API ドキュメント ページで、**GetTables** をクリックして、**[試してみる]** をクリックします。

7. GET 要求ダイアログで、**[ヘッダー]** の横にあるプラス記号をクリックし、左のボックスに値「`X-ZUMO-AUTH`」を入力し、コピーした *authenticationToken* を右のボックスに貼り付けて、**[送信]** をクリックします。

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-endpoint.png)

	モバイル サービスは、エンドポイントへのアクセスを許可し、**200/OK** ステータスと共にテーブルの TodoItems の一覧を返します。

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-success.png)

>[AZURE.IMPORTANT]テストのためにこのモバイル サービス プロジェクトも Azure に発行することを選択する場合は、サインインおよび認証プロバイダーが攻撃に対して脆弱であることに注意してください。プロバイダーが適切に強化されているか、または保護されているテスト データが重要ではないことを確認してください。カスタム認証方式を使用して運用環境のサービスを保護する前に、十分に注意してください。

## クライアントからカスタム認証を使用してサインインする

このセクションでは、クライアントからカスタム認証エンドポイントにアクセスしてモバイル サービスへのアクセスに必要な認証トークンを取得するために必要な手順について説明します。必要な特定のクライアント コードはクライアントによって異なり、ここで提供するガイダンスはプラットフォームに依存しないものです。

>[AZURE.NOTE]Mobile Services クライアント ライブラリは、HTTPS でサービスと通信します。このソリューションではパスワードをプレーン テキストとして送信する必要があるため、直接 REST 要求を使用してこれらのエンドポイントを呼び出すときは、HTTPS を使用することを確認する必要があります。

1. ユーザーがユーザー名とパスワードを入力するのに必要な UI 要素をクライアント アプリで作成します。

2. クライアント ライブラリの **MobileServiceClient** で適切な **invokeApi** メソッドを使用して **CustomRegistration** エンドポイントを呼び出し、実行時に提供されるユーザー名とパスワードをメッセージ本文で渡します。

	Accounts テーブルにユーザー ログイン情報を保持するなら、特定のユーザーについて **CustomRegistration** エンドポイントを呼び出してアカウントを作成するのは 1 回だけで済みます。サポートされているさまざまなクライアント プラットフォームでカスタム API を呼び出す方法の例については、記事「[Custom API in Azure Mobile Services – client SDKs (Azure Mobile Services でのカスタム API - クライアント SDK)](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx)」を参照してください。
	 
	> [AZURE.IMPORTANT]このユーザー プロビジョニング手順が実行されるのは 1 回だけなので、なんらかの帯域外の方法でユーザー アカウントを作成することを考える必要があります。パブリック登録エンドポイントの場合、SMS ベースまたは電子メール ベースの検証プロセス、または不正なアカウントの生成を防ぐための他のなんらかの保護対策の実装を検討することも必要です。Twilio を使用して Mobile Services から SMS メッセージを送信できます。詳細については、「[方法: SMS メッセージを送信する](partner-twilio-mobile-services-how-to-use-voice-sms.md#howto_send_sms)」を参照してください。SendGrid を使用して Mobile Services から電子メールを送信することもできます。詳細については、「[SendGrid を使用した Mobile Services からの電子メールの送信](store-sendgrid-mobile-services-send-email-scripts.md)」を参照してください。
	
3. 適切な **invokeApi** メソッドを再び使用して今度は **CustomLogin** エンドポイントを呼び出し、実行時に提供されるユーザー名とパスワードをメッセージ本文で渡します。

	今度は、ログイン成功後に応答オブジェクトで返される *userId* と *authenticationToken* の値を取得する必要があります。
	
4. 返された *userId* と *authenticationToken* の値を使用して新しい **MobileServiceUser** オブジェクトを作成し、**MobileServiceClient** インスタンスの現在のユーザーとしてそれを設定します (トピック「[既存の Azure Mobile Services アプリへの認証の追加](mobile-services-dotnet-backend-ios-get-started-users.md)」を参照)。CustomLogin の結果は **MobileServiceUser** オブジェクトと同じ形なので、結果を直接キャストできます。

このチュートリアルはこれで終わりです。


<!-- Anchors. -->


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[アプリへの認証の追加]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Mobile Services の使用]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

[ClaimsIdentity]: https://msdn.microsoft.com/library/system.security.claims.claimsidentity(v=vs.110).aspx
[ProviderCredentials]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.providercredentials.aspx
 

<!---HONumber=58_postMigration-->