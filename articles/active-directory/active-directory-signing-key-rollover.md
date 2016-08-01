<properties
	pageTitle="Azure AD の署名キーのロールオーバー | Microsoft Azure"
	description="この記事では、Azure Active Directory の署名キーのロール オーバーのベスト プラクティスについて説明します"
	services="active-directory"
	documentationCenter=".net"
	authors="gsacavdm"
	manager="krassk"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="gsacavdm"/>

# Azure Active Directory の署名キーのロールオーバー

このトピックでは、Azure Active Directory (Azure AD) でセキュリティ トークンに署名するために使用される公開キーについて説明します。これらのキーは定期的にロールオーバー (交換) されるほか、緊急時にはその場ですぐロールオーバーすることもできます。Azure AD を使用するすべてのアプリケーションには、プログラムからキーのロールオーバー プロセスを処理できる機能が必要です。ここではキーのしくみについて説明すると共に、アプリケーションへのロールオーバーの影響を評価する方法と、キーのロールオーバーに必要に応じて対処できるようアプリケーションを更新する方法について説明しています。

> [AZURE.IMPORTANT] 次の署名キーのロールオーバーは、2016 年 8 月 15 日を予定しています。これは、Azure AD アプリケーション ギャラリー (カスタムを含む) から追加されたアプリケーション、アプリケーション プロキシ経由で発行されたオンプレミス アプリケーション、Azure AD B2C テナント内のアプリケーション、ACS または ADFS と統合されているアプリケーションには影響**しません**。

## Azure AD での署名キーの概要

Azure AD では、業界標準に基づいて構築された公開キー暗号化を使って、キーと、キーを使用するアプリケーションの間の信頼を確立しています。具体的には、次のように機能します。Azure AD は、公開キーと秘密キーの組み合わせから構成される署名キーを使用します。認証に Azure AD を使用するアプリケーションにユーザーが署名すると、Azure AD はユーザーに関する情報を含むセキュリティ トークンを作成します。このトークンは、Azure AD によって秘密キーを使って署名されてから、アプリケーションに送り返されます。トークンが有効であり、実際に Azure AD から発行されたことを確認するには、アプリケーションは、テナントの [OpenID Connect Discovery ドキュメント](http://openid.net/specs/openid-connect-discovery-1_0.html)または SAML/WS-Fed の[フェデレーション メタデータ ドキュメント](active-directory-federation-metadata.md)に含まれる Azure AD によって公開された公開キーを使用して、トークンの署名を検証する必要があります。

Azure AD の署名キーは、セキュリティ上の理由から定期的に交換されるほか、緊急時にはその場ですぐにロールオーバーすることもできます。Azure AD を組み込むすべてのアプリケーションは、発生頻度に関係なくキーのロールオーバー イベントを処理できるようになっている必要があります。このロジックを備えていないアプリケーションが期限切れのキーを使ってトークンの署名の検証を試みると、サインイン要求が失敗します。

OpenID Connect Discovery ドキュメントとフェデレーション メタデータ ドキュメントには、利用できる有効なキーが常に複数存在します。1 つのキーがすぐにロールされて別のキーに置き換えられる可能性があるので、アプリケーションは、このドキュメントで指定されているどのキーでも使用できるようになっている必要があります。

## アプリケーションに影響が波及するかどうかの評価とその対処法

キー ロールオーバーへのアプリケーション側の対応は、アプリケーションの種類や使用されている ID プロトコル、ライブラリなどさまざまな要因によって異なります。以下の各セクションでは、ごく一般的なアプリケーションにおけるキー ロールオーバーへの影響の有無を評価し、キーの自動ロールオーバーまたは手動更新に対応するようにアプリケーションを更新するうえでの指針を取り上げています。

* [リソースにアクセスするネイティブ クライアント アプリケーション](#nativeclient)
* [リソースにアクセスする Web アプリケーション/API](#webclient)
* [Azure App Service を使用して構築された、リソースを保護する Web アプリケーション/API](#appservices)
* [.NET OWIN OpenID Connect、WS-Fed、WindowsAzureActiveDirectoryBearerAuthentication のいずれかのミドルウェアを使用し、リソースを保護する Web アプリケーション/API](#owin)
* [.NET Core OpenID Connect と JwtBearerAuthentication のいずれかのミドルウェアを使用し、リソースを保護する Web アプリケーション/API](#owincore)
* [Node.js passport-azure-ad モジュールを使用し、リソースを保護する Web アプリケーション/API](#passport)
* [Visual Studio 2015 を使用して作成された、リソースを保護する Web アプリケーション/API](#vs2015)
* [Visual Studio 2013 を使用して作成された、リソースを保護する Web アプリケーション](#vs2013)
* [Visual Studio 2013 を使用して作成された、リソースを保護する Web API](#vs2013_webapi)
* [Visual Studio 2012 を使用して作成された、リソースを保護する Web アプリケーション](#vs2012)
* [Visual Studio 2010/2008 または Windows Identity Foundation を使用して作成された、リソースを保護する Web アプリケーション](#vs2010)
* [その他のライブラリが使用されているか、サポートされているプロトコルが手動で実装された、リソースを保護する Web アプリケーション/API](#other)

### <a name="nativeclient"></a>リソースにアクセスするネイティブ クライアント アプリケーション

リソース (つまり、Microsoft Graph、KeyVault、Outlook API、その他の Microsoft API) にアクセスするだけのアプリケーションは、通常、トークンを取得してリソース所有者に渡すだけです。アプリケーションでリソースを保護しない場合は、トークンを調べることはないため、トークンへの適切な署名は必要はありません。

デスクトップかモバイルかを問わず、ネイティブ クライアント アプリケーションはこのカテゴリに分類され、ロールオーバーの影響を受けません。

### <a name="webclient"></a>リソースにアクセスする Web アプリケーション/API

リソース (つまり、Microsoft Graph、KeyVault、Outlook API、その他の Microsoft API) にアクセスするだけのアプリケーションは、通常、トークンを取得してリソース所有者に渡すだけです。アプリケーションでリソースを保護しない場合は、トークンを調べることはないため、トークンへの適切な署名は必要はありません。

アプリ専用のフロー (クライアント資格情報/クライアント証明書) を使用する Web アプリケーションと Web API はこのカテゴリに分類され、ロールオーバーの影響を受けません。

### <a name="appservices"></a>Azure App Service を使用して構築された、リソースを保護する Web アプリケーション/API

Azure App Service の認証/承認 (EasyAuth) 機能には、キーのロールオーバーを自動的に処理するうえで必要なロジックがあります。

### <a name="owin"></a>.NET OWIN OpenID Connect、WS-Fed、WindowsAzureActiveDirectoryBearerAuthentication のいずれかのミドルウェアを使用し、リソースを保護する Web アプリケーション/API

アプリケーションで .NET OWIN OpenID Connect、WS-Fed、WindowsAzureActiveDirectoryBearerAuthentication のいずれかのミドルウェアを使用している場合、キーのロールオーバーに自動的に対処するうえで必要なロジックがあらかじめ用意されています。

それらがアプリケーションで使用されているかどうかは、アプリケーションの Startup.cs または Startup.Auth.cs から次のスニペットを探すことで確認できます。

```
app.UseOpenIdConnectAuthentication(
	 new OpenIdConnectAuthenticationOptions
	 {
		 // ...
	 });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
	 // ...
 	});
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
	 new WindowsAzureActiveDirectoryBearerAuthenticationOptions
	 {
	 // ...
	 });
```

### <a name="owincore"></a>.NET Core OpenID Connect と JwtBearerAuthentication のいずれかのミドルウェアを使用し、リソースを保護する Web アプリケーション/API

アプリケーションで .NET Core OWIN OpenID Connect と JwtBearerAuthentication のいずれかのミドルウェアを使用している場合、キーのロールオーバーに自動的に対処するうえで必要なロジックがあらかじめ用意されています。

それらがアプリケーションで使用されているかどうかは、アプリケーションの Startup.cs または Startup.Auth.cs から次のスニペットを探すことで確認できます。

```
app.UseOpenIdConnectAuthentication(
	 new OpenIdConnectAuthenticationOptions
	 {
		 // ...
	 });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
	 // ...
 	});
```

### <a name="passport"></a>Node.js passport-azure-ad モジュールを使用し、リソースを保護する Web アプリケーション/API

アプリケーションで Node.js passport-ad モジュールを使用している場合、キーのロールオーバーに自動的に対処するうえで必要なロジックがあらかじめ用意されています。

アプリケーションで passport-ad が使用されているかどうかは、アプリケーションの app.js から次のスニペットを探すことで確認できます。

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
	//...
));
```

### <a name="vs2015"></a>Visual Studio 2015 を使用して作成された、リソースを保護する Web アプリケーション/API

アプリケーションが Visual Studio 2015 の Web アプリケーション テンプレートを使用して作成されており、**[認証の変更]** メニューで **[会社用および学校用のアカウント]** を選択した場合は、キーのロールオーバーに自動的に対処するうえで必要なロジックがあらかじめ用意されています。このロジックは OpenID Connect Discovery ドキュメントからキーを取得してキャッシュし、定期的にそれらを更新するもので、OWIN OpenID Connect ミドルウェアに組み込まれています。

認証を手動でソリューションに追加した場合は、キーのロールオーバーに対応するうえで必要なロジックがアプリケーションに備わっていない可能性があります。必要なロジックを独自に作成するか、[その他のライブラリが使用されているか、サポートされているプロトコルが手動で実装された Web アプリケーション/API](#other)に関するセクションの手順に従ってください。

### <a name="vs2013"></a>Visual Studio 2013 を使用して作成された、リソースを保護する Web アプリケーション

アプリケーションが Visual Studio 2013 の Web アプリケーション テンプレートを使用して作成されており、**[認証の変更]** メニューで **[組織アカウント]** を選択した場合、キーのロールオーバーに自動的に対処するうえで必要なロジックがあらかじめ用意されています。このロジックにより、プロジェクトに関連付けられた 2 つのデータベース テーブルに組織の一意識別子と署名キー情報が保存されます。このデータベースの接続文字列は、プロジェクトの Web.config ファイル内に格納されています。

認証を手動でソリューションに追加した場合は、キーのロールオーバーに対応するうえで必要なロジックがアプリケーションに備わっていない可能性があります。必要なロジックを独自に作成するか、[その他のライブラリが使用されているか、サポートされているプロトコルが手動で実装された Web アプリケーション/API](#other)に関するセクションの手順に従ってください。

アプリケーションでロジックが適切に機能するかどうかは、以下の手順によって確認できます。

1. Visual Studio 2013 で、ソリューションを開き、右側のウィンドウで **[サーバー エクスプローラー]** タブをクリックします。
2. **[データ接続]**、**[既定の接続]**、**[テーブル]** の順に展開します。**[IssuingAuthorityKeys]** テーブルを右クリックし、**[テーブル データの表示]** をクリックします。
3. **[IssuingAuthorityKeys]** テーブルには少なくとも 1 つの行があり、キーの拇印の値に対応しています。テーブル内の任意の行を削除します。
4. **[Tenants]** テーブルを右クリックし、**[テーブル データの表示]** をクリックします。
5. **[Tenants]** テーブルには少なくとも 1 つの行があり、一意のディレクトリ テナント ID に対応しています。テーブル内の任意の行を削除します。**[Tenants]** テーブルと **[IssuingAuthorityKeys]** テーブルの両方で行を削除しないと、実行時にエラーが表示されます。
6. アプリケーションをビルドし、実行します。アカウントにログインすると、アプリケーションを停止できます。
7. **[サーバー エクスプローラー]** に戻り、**[IssuingAuthorityKeys]** テーブルおよび **[Tenants]** テーブルの値を確認します。テーブルにはフェデレーション メタデータ ドキュメントから自動的に適切な情報が入力されています。

### <a name="vs2013"></a>Visual Studio 2013 を使用して作成された、リソースを保護する Web API

Visual Studio 2013 で Web API テンプレートを使用して Web API を作成し、**[認証の変更]** メニューで **[組織アカウント]** を選択した場合、アプリケーションには既に必要なロジックが含まれています。手動で認証を構成する場合は、以下の手順に従って、自動的にキー情報を更新するように Web API を構成する方法を確認してください。

次のコード スニペットは、フェデレーション メタデータ ドキュメントから最新のキーを取得し、[JWT トークン ハンドラー](https://msdn.microsoft.com/library/dn205065.aspx)を使用してトークンを検証する方法を示しています。このコード スニペットは、今後 Azure AD から受け取るトークンを検証するために、データベース、構成ファイルなどの何らかの独自のキャッシュ メカニズムを使用してキーを保存することが前提です。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Visual Studio 2012 を使用して作成された、リソースを保護する Web アプリケーション

アプリケーションが Visual Studio 2012 で作成されている場合、通常、アプリケーションは ID およびアクセス ツールを使用して構成されています。また、通常は[発行者名レジストリの検証 (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) が使用されています。VINR の役割は、信頼済みの ID プロバイダー (Azure AD) に関する情報、およびプロバイダーが発行するトークンを検証するために使用されるキーに関する情報を維持することです。また、VINR を使用すると、ディレクトリに関連付けられている最新のフェデレーション メタデータ ドキュメントをダウンロードし、構成が期限切れになっているかどうかを確認し、必要に応じて新しいキーでアプリケーションを更新することにより、Web.config ファイルに保存されているキー情報を自動的に更新することが容易になります。

Microsoft から提供されたコード サンプルまたはチュートリアルのいずれかを使用してアプリケーションを作成した場合、キーのロールオーバー ロジックは既にプロジェクトに含まれています。以下に示すコードがプロジェクト内に存在していることを確認できます。アプリケーションにこのロジックが含まれていない場合は、以下の手順を実行してロジックを追加し、正常に機能することを確認してください。

1. **[ソリューション エクスプローラー]** で、適切なプロジェクトの **[System.IdentityModel]** アセンブリへの参照を追加します。
2. **Global.asax.cs** ファイルを開き、次の using ディレクティブを追加します。
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. 次のメソッドを **Global.asax.cs** ファイルに追加します。
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. 次に示すように、**Global.asax.cs** 内の **Application\_Start()** メソッドで **RefreshValidationSettings()** メソッドを呼び出します。
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

この手順を実行すると、アプリケーションの Web.config は、最新のキーなど、フェデレーション メタデータ ドキュメントの最新情報で更新されます。この更新は、IIS のアプリケーション プールがリサイクルされるたびに行われます。IIS の既定では、アプリケーションは 29 時間ごとにリサイクルされます。

キーのロールオーバー ロジックが機能していることを確認するには、次の手順に従います。

1. アプリケーションで上記のコードが使用されていることを確認したら、**Web.config** ファイルを開き、**<issuerNameRegistry>** ブロックに移動して、以下の行を見つけます。
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. **<add thumbprint=””>** 設定で、どれか 1 文字を別の文字に置き換えて拇印の値を変更します。**Web.config** ファイルを保存します。

3. アプリケーションをビルドし、実行します。サインイン プロセスを完了できる場合、アプリケーションではディレクトリのフェデレーション メタデータ ドキュメントから必要な情報をダウンロードすることによってキーが正しく更新されています。サインインで問題が発生する場合は、[Azure AD を使用した Web アプリケーションへのサインオンの追加](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect)に関するページを読むか、コード サンプル ([Multi-Tenant Cloud Application for Microsoft Azure Active Directory (Microsoft Azure Active Directory 向けのマルチテナント クラウド アプリケーション)](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b)) をダウンロードして調べることによって、アプリケーションの変更が正しいことを確認します。


### <a name="vs2010"></a>Visual Studio 2008/2010 および .NET 3.5 用 Windows Identity Foundation (WIF) v1.0 で作成された、リソースを保護する Web アプリケーション

WIF v1.0 でアプリケーションを作成した場合、新しいキーを使用するようにアプリケーションの構成を自動的に更新するメカニズムは用意されていません。キーを更新する最も簡単な方法は、WIF SDK に含まれている FedUtil ツールを使用することです。このツールでは、最新のメタデータ ドキュメントを取得し、構成を更新できます。この手順は、この後で示します。または、次のいずれかの操作を行うことができます。

- この後の「最新のキーを手動で取得し、アプリケーションを更新する」セクションの指示に従って、手順をプログラムで実行するロジックを記述する。
- System 名前空間に最新バージョンの WIF が含まれている .NET 4.5 にアプリケーションを更新する。その後、[発行者名レジストリの検証 (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) を使用して、アプリケーションの構成の自動更新を実行できます。


1. Visual Studio 2008 または 2010 をデプロイしてあるマシンに WIF v1.0 SDK がインストールされていることを確認します。インストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/ja-JP/download/details.aspx?id=4451)。
2. Visual Studio でソリューションを開き、該当するプロジェクトを右クリックして、**[Update federation metadata (フェデレーション メタデータの更新)]** を選択します。このオプションが表示されない場合は、FedUtil と WIF v1.0 SDK の少なくともどちらかがインストールされていません。
3. プロンプトで **[更新]** を選択してフェデレーション メタデータの更新を開始します。アプリケーションがホストされているサーバー環境にアクセスできる場合は、FedUtil の[メタデータの自動更新スケジューラ](https://msdn.microsoft.com/library/ee517272.aspx)を使用することもできます。
4. **[完了]** をクリックして更新プロセスを完了します。

### <a name="other"></a>その他のライブラリが使用されているか、サポートされているプロトコルが手動で実装された、リソースを保護する Web アプリケーション/API

その他何らかのライブラリを使用している場合またはサポートされているプロトコルを手動で実装した場合、OpenID Connect Discovery ドキュメントまたはフェデレーション メタデータ ドキュメントから確実にキーが取得されるようそのライブラリまたは実装内容を再確認する必要があります。この点はたとえば、自分が記述したコードやライブラリのコードから、OpenID のディスカバリー ドキュメントまたはフェデレーション メタデータ ドキュメントの呼び出しを検索することによって確認できます。

どこかにキーを保存している場合や、アプリケーションにキーをハードコーディングしている場合は、必要なキーを手動で取得して適宜更新してください。今後 Azure AD のロールオーバー頻度が高まったり、予定されていない緊急のロールオーバーが発生したりした場合の中断やオーバーヘッドを避けるために、この記事に書かれているいずれかの手法を使用して**自動ロールオーバーをアプリケーションでサポートするよう強くお勧めします**。

OpenID Discovery ドキュメントから最新のキーを手動で取得するには、次の手順に従います。

1. Web ブラウザーで `https://login.microsoftonline.com/your_directory_name/.well-known/openid-configuration` に移動します。OpenID Connect Discovery ドキュメントの内容が表示されます。このドキュメントの詳細については、[OpenID ディスカバリー ドキュメントの仕様](http://openid.net/specs/openid-connect-discovery-1_0.html)を参照してください。
2. jwks\_uri の値にあるリンクをコピーします。
3. ブラウザーで新しいタブを開き、今コピーした URL に移動します。JSON Web Key Set ドキュメントの内容が表示されます。
4. 新しいキーを使用するようにアプリケーションを更新するには、**x5c** 要素を 1 つずつ探し、各要素の値をコピーします。次に例を示します。
```
keys: [
	{
		kty: "RSA",
		use: "sig",
		kid: "MnC_VZcATfM5pOYiJHMba9goEKY",
		x5t: "MnC_VZcATfM5pOYiJHMba9goEKY",
		n: "vIqz-4-ER_vNW...ixLUQ",
		e: "AQAB",
		x5c: [
			"MIIC4jCCAcqgAw...dhXsIIKvJQ=="
		]
	},
```
5. **<X509Certificate>** 要素の値をコピーしたら、テキスト エディターを起動し、値を貼り付けます。必ず末尾のスペースを削除してから、**.cer** 拡張子で保存します。

フェデレーション メタデータ ドキュメントから最新のキーを手動で取得するには、次の手順に従います。

1. Web ブラウザーで `https://login.microsoftonline.com/your_directory_name/federationmetadata/2007-06/federationmetadata.xml` に移動します。フェデレーション メタデータ XML ドキュメントの内容が表示されます。このドキュメントの詳細については、[フェデレーション メタデータ](active-directory-federation-metadata.md)に関するトピックを参照してください。
2. 新しいキーを使用するようにアプリケーションを更新するには、**<RoleDescriptor>** ブロックを 1 つずつ探し、各ブロックの **<X509Certificate>** 要素の値をコピーします。次に例を示します。
```
<RoleDescriptor xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType">
      <KeyDescriptor use="signing">
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
                <X509Data>
                    <X509Certificate>MIIDPjC…BcXWLAIarZ</X509Certificate>
```
3. **<X509Certificate>** 要素の値をコピーしたら、テキスト エディターを起動し、値を貼り付けます。必ず末尾のスペースを削除してから、**.cer** 拡張子で保存します。

これで、Azure AD の公開キーとして使用される X509 証明書が作成されました。サムプリントや有効期限などの証明書の詳細を使用すると、アプリケーションで現在使用されている証明およびサムプリントが有効であるかどうかを手動またはプログラムによってチェックすることができます。

## アプリケーションをテストして、影響を受けるかどうかを判別する

[この GitHub リポジトリ](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)からスクリプトをダウンロードし、指示に従って、アプリケーションで自動キー ロールオーバーがサポートされているかどうかを検証できます。

<!---HONumber=AcomDC_0720_2016-->