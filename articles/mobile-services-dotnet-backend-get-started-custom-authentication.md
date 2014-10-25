<properties linkid="mobile-services-dotnet-backend-get-started-custom-authentication" urlDisplayName="Get started with custom authentication" pageTitle="Get started with custom authentication | Mobile Dev Center" metaKeywords="" description="Learn how to authenticate users with a username and password." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with custom authentication" authors="mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="mahender"></tags>

# カスタム認証の使用

このトピックでは、独自の Mobile Services 認証トークンを発行して、Azure Mobile Services .NET バックエンドでユーザーを認証する方法について説明します。このチュートリアルでは、アプリケーションのカスタム ユーザー名とパスワードを使用して、クイック スタート プロジェクトに認証を追加します。

> [WACOM.NOTE] このチュートリアルでは、カスタムの資格情報を使用して Mobile Services を認証する高度な方法を説明します。多くのアプリケーションでは、代わりに組み込みのソーシャル ID プロバイダーを使用する方法が最適であり、ユーザーは Facebook、Twitter、Google、Microsoft アカウント、および Azure Active Directory を介してログインすることができます。Mobile Services の認証を初めて使用する場合は、「[ユーザー認証の使用][ユーザー認証の使用]」チュートリアルを参照してください。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1.  [アカウント テーブルを設定する][アカウント テーブルを設定する]
2.  [登録エンドポイントを作成する][登録エンドポイントを作成する]
3.  [LoginProvider を作成する][LoginProvider を作成する]
4.  [ログイン エンドポイントを作成する][ログイン エンドポイントを作成する]
5.  [認証を要求するようにモバイル サービスを構成する][認証を要求するようにモバイル サービスを構成する]
6.  [テスト クライアントを使用して、ログイン フローをテストする][テスト クライアントを使用して、ログイン フローをテストする]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

> [WACOM.NOTE] このチュートリアルの目的は、Mobile Services の認証トークンを発行する方法を説明することです。このチュートリアルは、セキュリティに関するガイダンスを示すものではありません。アプリケーションを開発する際には、パスワードの保存がセキュリティに及ぼす影響について認識し、ブルート フォース攻撃に対応する戦略を用意する必要があります。

## <a name="table-setup"></a>アカウント テーブルを設定する

別の ID プロバイダーを使用しないでカスタム認証を使用するため、ユーザーのログイン情報を保存する必要があります。このセクションでは、アカウント用のテーブルを作成し、基本的なセキュリティ メカニズムを設定します。アカウント テーブルには、ユーザー名と、ソルトを使用してハッシュ化されたパスワードが保存されます。また、必要に応じて追加のユーザー情報も保存できます。

1.  バックエンド プロジェクトの `DataObjects` フォルダーで、`Account` という名前の新しいエンティティを作成します。

            public class Account : EntityData
            {
                public string Username { get; set; }
                public byte[] Salt { get; set; }
                public byte[] SaltedAndHashedPassword { get; set; }
            }

    これは、新しいテーブルの行を表します。これにはユーザー名、そのユーザーのソルト、および安全に保存されたパスワードが格納されます。

2.  `Models` フォルダーには、モバイル サービスの名前が付けられた `DbContext` クラスがあります。このチュートリアルの残りの部分では、例として `todoContext` を使用し、例に合わせてコード スニペットを更新する必要があります。コンテキストを開き、次のコードを含めて、データ モデルにアカウント テーブルを追加します。

        public DbSet<Account> Accounts { get; set; }

3.  次に、このデータを操作するためにセキュリティ関数を設定します。新しい長いソルトを生成する手段、ソルトを使用したパスワードをハッシュ化する機能、2 つのハッシュを比較する安全な方法が必要です。`CustomLoginProviderUtils` という名前のクラスを作成し、このクラスに次のメソッドを追加します。

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

## <a name="register-endpoint"></a>登録エンドポイントを作成する

この時点で、ユーザー アカウントの作成を開始するのに必要なものはすべて揃いました。このセクションでは、登録エンドポイントを設定して、新しい登録の要求を処理します。ここで新しいユーザー名ポリシーとパスワード ポリシーを適用して、ユーザー名が取得されないようにします。その後、データベースにユーザー情報を安全に保存します。

1.  次のように、受信する登録要求を表すオブジェクトを作成します。

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    登録時に他の情報を収集する場合は、ここで、このオブジェクトを含めることができます。

2.  Mobile Services バックエンド プロジェクトで、CustomRegistrationController という名前の新しいカスタム コントローラーを追加して、次のコードを貼り付けます。

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
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Username already exists");
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

    次のコードを使用してコントローラーを修飾し、このエンドポイントに対するすべてのトラフィックを許可してください。

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

## <a name="login-provider"></a>LoginProvider を作成する

Mobile Services 認証パイプラインの基本的なコンストラクトの 1 つに `LoginProvider` があります。このチュートリアルでは、独自の `CustomLoginProvider` を作成します。これは、組み込みのプロバイダーのようにパイプラインにプラグインされるのではなく、いくつかの便利な機能を提供します。

1.  次のように `LoginProvider` から派生する新しいクラス `CustomLoginProvider` を作成します。

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

    `LoginProvider` には、この他にも 3 つの抽象メソッドがありますが、これらは後で実装します。

2.  `CustomLoginProviderCredentials` という名前の新しいクラスを作成します。これは、ユーザーに関する情報を表し、`ServiceUser.getIdentitiesAsync()` を介してバックエンドで利用可能になります。カスタム クレームを追加する場合は、このオブジェクトで取得されることを確認します。

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

3.  抽象メソッド `ConfigureMiddleware` の次の実装を、`CustomLoginProvider` に追加します。`CustomLoginProvider` を認証パイプラインに統合しないため、このメソッドはここでは何も行いません。

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

4.  抽象メソッド `ParseCredentials` の次の実装を、`CustomLoginProvider` に追加します。このメソッドは、受信する認証トークンのユーザー情報をバックエンドが逆シリアル化するのを許可します。

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

5.  抽象メソッド `CreateCredentials` の次の実装を、`CustomLoginProvider` に追加します。このメソッドは、`ClaimsIdentity` を認証トークン発行フェーズで使用される `ProviderCredentials` オブジェクトに変換します。ここで、追加のクレームがあれば、もう一度取得できます。

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

## <a name="login-endpoint"></a>ログイン エンドポイントを作成する

次に、ユーザーがログインするエンドポイントを作成します。受信するユーザー名とパスワードをデータベースに対して確認するために、最初にユーザーのソルトを適用し、パスワードをハッシュ化し、受信する値とデータベースの値が一致することを確認します。一致している場合は、`ClaimsIdentity` を作成し、`CustomLoginProvider` に渡します。クライアント アプリケーションがユーザー ID と認証アクセス トークンを受信して、モバイル サービスにさらにアクセスできます。

1.  Mobile Services バックエンド プロジェクトで、次のように、受信するログイン要求を表すオブジェクトを作成します。

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

2.  `CustomLoginController` という名前の新しいカスタム コントローラーを追加して、次のコードを貼り付けます。

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomLoginController : ApiController
        {
            public ApiServices Services { get; set; }
            public IServiceTokenHandler handler { get; set; }

            // POST api/CustomLogin
            public HttpResponseMessage Post(LoginRequest loginRequest)
            {
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == loginRequest.username).SingleOrDefault();
                if (account != null)
                {
                    byte[] incoming = CustomLoginProviderUtils.hash(loginRequest.password, account.Salt);

                    if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
                    {
                        ClaimsIdentity claimsIdentity = new ClaimsIdentity();
                        claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
                        LoginResult loginResult = new CustomLoginProvider(handler).CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
                        return this.Request.CreateResponse(HttpStatusCode.OK, loginResult);
                    }
                }
                return this.Request.CreateResponse(HttpStatusCode.Unauthorized, "Invalid username or password");
            }
        }

    次のコードを使用してコントローラーを修飾し、このエンドポイントに対するすべてのトラフィックを許可してください。

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

> [WACOM.NOTE] 運用で使用する `CustomLoginController` には、ブルート フォース攻撃を検出する戦略を盛り込む必要もあります。そうしない場合、作成したログイン ソリューションが攻撃に対して脆弱になる可能性があります。

## <a name="require-authentication"></a>認証を要求するようにモバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

## <a name="test-login"></a>テスト クライアントを使用して、ログイン フローをテストする

クライアント アプリケーションで、カスタム ログイン画面を開発する必要があります。この画面では、ユーザー名とパスワードを取得して、登録エンドポイントとログイン エンドポイントに JSON ペイロードとして送信します。このチュートリアルを完了するには、代わりに Mobile Services .NET バックエンド用の組み込みのテスト クライアントを使用します。

> [WACOM.NOTE] Mobile Services SDK では、HTTPS によりサービスと通信します。直接の REST 呼び出しを介してこのエンドポイントにアクセスする場合は、パスワードがプレーンテキストで送信されるため、モバイル サービスの呼び出しに HTTPS を使用していることを確認する必要があります。

1.  Visual Studio で、Mobile Services バックエンド プロジェクトを右クリックし、**[デバッグ]、[新しいインスタンスを開始]** の順に選択して、プロジェクトの新しいデバッグ インスタンスを開始します。

    ![][]

2.  **[試してみる]** をクリックします。

    ![][1]

3.  登録エンドポイントを選択します。API の基本的なドキュメントを参照できます。**[試してみる]** をクリックします。

    ![][2]

4.  本文で、以前指定した基準に合うユーザー名とパスワードにサンプル文字列を置き換えます。**[送信]** をクリックします。応答は **"201/作成済み"**になります。

    ![][3]

5.  ログイン エンドポイントについても、このプロセスを繰り返します。以前登録した同じユーザー名とパスワードを送信すると、ユーザー ID と認証トークンを受信します。

    ![][4]



  [ユーザー認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [アカウント テーブルを設定する]: #table-setup
  [登録エンドポイントを作成する]: #register-endpoint
  [LoginProvider を作成する]: #login-provider
  [ログイン エンドポイントを作成する]: #login-endpoint
  [認証を要求するようにモバイル サービスを構成する]: #require-authentication
  [テスト クライアントを使用して、ログイン フローをテストする]: #test-login
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  []: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
  [1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
  [2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
  [3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
  [4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png
