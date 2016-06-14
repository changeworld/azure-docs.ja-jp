<properties
	pageTitle="Azure AD の署名キーのロールオーバー | Microsoft Azure"
	description="この記事では、Azure Active Directory の署名キーのロール オーバーのベスト プラクティスについて説明します"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

# Azure Active Directory の署名キーのロールオーバー

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

このトピックでは、Azure Active Directory (Azure AD) でセキュリティ トークンに署名するために使用される公開キーについて説明します。このキーは 6 週間ごとにロールオーバーされることに注意してください。緊急時には、6 週間が経過する前にロールオーバーされる可能性もあります。Azure AD を使用するすべてのアプリケーションには、プログラムからキーのロールオーバー プロセスを処理できる機能が必要です。この後の説明を読んで、キーの動作方法について、さらにキーのロールオーバーを処理するようにアプリケーションを更新する方法ついて理解してください。

## Azure AD での署名キーの概要

Azure AD では、業界標準に基づいて構築された公開キー暗号化を使って、キーと、キーを使用するアプリケーションの間の信頼を確立しています。具体的には、次のように機能します。Azure AD は、公開キーと秘密キーの組み合わせから構成される署名キーを使用します。認証に Azure AD を使用するアプリケーションにユーザーが署名すると、Azure AD はユーザーに関する情報を含むセキュリティ トークンを作成します。このトークンは、Azure AD によって秘密キーを使って署名されてから、アプリケーションに送り返されます。トークンが有効であり、実際に Azure AD から発行されたことを確認するには、アプリケーションは、テナントのフェデレーション メタデータ ドキュメントに含まれる Azure AD によって公開された公開キーを使用して、トークンの署名を検証する必要があります。この公開キーとその派生元である署名キーは、Azure AD のすべてのテナントに対して同じものが使用されます。

セキュリティのために、Azure AD の公開キーは 6 週間ごとにロールオーバーされます。緊急時には、6 週間が経過する前にロールオーバーされる可能性があります。Azure AD を組み込むすべてのアプリケーションは、発生頻度に関係なくキーのロールオーバー イベントを処理できるようになっている必要があります。アプリケーションがキーのロールオーバーを処理するために必要なロジックを備えているかどうかは、いつ、どの認証ライブラリを使って作成されたかによって決まります。このロジックを備えていないアプリケーションが期限切れの公開キーを使ってトークンの署名の検証を試みると、サインイン要求が失敗します。

キーはいつでもロールオーバーされる可能性があるため、フェデレーション メタデータ ドキュメントでは常に複数の有効な公開キーを使用できます。1 つのキーがすぐにロールされて別のキーに置き換えられる可能性があるので、アプリケーションは、このドキュメントで指定されているどのキーでも使用できるようになっている必要があります。アプリケーションでは、サインイン プロセスにおける Azure AD とのやり取りの効率を高めるため、および別のキーを使用した場合でもすぐにトークンを検証できるように、これらのキーをデータベースまたは構成ファイルにキャッシュすることをお勧めします。

## キーのロールオーバー ロジックを使用してアプリケーションを更新する方法

アプリケーションがキーのロールオーバーを処理する方法は、使用している Identity Framework、そのバージョン、アプリケーションの種類などの要素によって異なります。この後の各セクションで、一般的なアプリケーションの種類と構成について更新方法を示します。また、手順を追うことで、ロジックが適切に機能することを確認できます。

### Visual Studio 2013 で作成した Web アプリケーション

アプリケーションが Visual Studio 2013 の Web アプリケーション テンプレートを使用して作成されており、**[認証の変更]** メニューで **[組織アカウント]** を選択した場合は、アプリケーションには既にキーのロールオーバーを処理するために必要なロジックが組み込まれています。このロジックにより、プロジェクトに関連付けられた 2 つのデータベース テーブルに組織の一意識別子と署名キー情報が保存されます。このデータベースの接続文字列は、プロジェクトの Web.config ファイル内に格納されています。

認証を手動でソリューションに追加した場合、アプリケーションには必要なキーのロールオーバー ロジックが含まれていません。独自にそれを記述するか、「最新のキーを手動で取得し、アプリケーションを更新する」に記載の手順に従う必要があります。

アプリケーションでロジックが適切に機能するかどうかは、以下の手順によって確認できます。

1. Visual Studio 2013 で、ソリューションを開き、右側のウィンドウで **[サーバー エクスプローラー]** タブをクリックします。
2. **[データ接続]**、**[既定の接続]**、**[テーブル]** の順に展開します。**[IssuingAuthorityKeys]** テーブルを右クリックし、**[テーブル データの表示]** をクリックします。
3. **[IssuingAuthorityKeys]** テーブルには少なくとも 1 つの行があり、キーのサムプリントの値に対応しています。テーブル内の任意の行を削除します。
4. **[Tenants]** テーブルを右クリックし、**[テーブル データの表示]** をクリックします。
5. [**Tenants**] テーブルには少なくとも 1 つの行があり、一意のディレクトリ テナント ID に対応しています。テーブル内の任意の行を削除します。**[テナント]** テーブルと **[IssuingAuthorityKeys]** テーブルの両方で行を削除しなければ、実行時にエラーが表示されます。
6. アプリケーションをビルドし、実行します。アカウントにログインすると、アプリケーションを停止できます。
7. **[サーバー エクスプローラー]** に戻り、**[IssuingAuthorityKeys]** テーブルおよび **[Tenants]** テーブルの値を確認します。テーブルにはフェデレーション メタデータ ドキュメントから自動的に適切な情報が入力されています。

### Visual Studio 2012 で作成された Web アプリケーション

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
2. **<add thumbprint=””>** 設定で、どれか 1 文字を別の文字に置き換えてサムプリントの値を変更します。**Web.config** ファイルを保存します。

3. アプリケーションをビルドし、実行します。サインイン プロセスを完了できる場合、アプリケーションではディレクトリのフェデレーション メタデータ ドキュメントから必要な情報をダウンロードすることによってキーが正しく更新されています。サインインで問題が発生する場合は、「[Adding Sign-On to Your Web Application Using Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect)」 (Azure AD で Web アプリケーションにサインオンを追加する) を読むか、次のコード サンプル「[Multi-Tenant Cloud Application for Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b)」 (Azure Active Directory 向けのマルチテナント クラウド アプリケーション) をダウンロードし調べることによって、アプリケーションの変更が正しいことを確認します。


### Visual Studio 2008 または 2010、および NET 3.5 用 Windows Identity Foundation (WIF) v1.0 で作成された Web アプリケーション

WIF v1.0 でアプリケーションを作成した場合、新しいキーを使用するようにアプリケーションの構成を自動的に更新するメカニズムは用意されていません。キーを更新する最も簡単な方法は、WIF SDK に含まれている FedUtil ツールを使用することです。このツールでは、最新のメタデータ ドキュメントを取得し、構成を更新できます。この手順は、この後で示します。または、次のいずれかの操作を行うことができます。

- この後の「最新のキーを手動で取得し、アプリケーションを更新する」セクションの指示に従って、手順をプログラムで実行するロジックを記述する。
- System 名前空間に最新バージョンの WIF が含まれている .NET 4.5 にアプリケーションを更新する。その後、「[発行者名レジストリの検証 (VINR)](https://msdn.microsoft.com/library/dn205067.aspx)」を使用して、アプリケーションの構成の自動更新を実行できます。


1. Visual Studio 2008 または 2010 をデプロイしてあるマシンに WIF v1.0 SDK がインストールされていることを確認します。インストールされていない場合は、[ここからダウンロードできます](https://www.microsoft.com/ja-JP/download/details.aspx?id=4451)。
2. Visual Studio で、ソリューションを開き、該当するプロジェクトを右クリックして、**[フェデレーション メタデータの更新]** を選択します。このオプションが表示されない場合は、FedUtil と WIF v1.0 SDK の少なくともどちらかがインストールされていません。
3. プロンプトで **[更新]** を選択してフェデレーション メタデータの更新を開始します。アプリケーションがホストされているサーバー環境にアクセスできる場合は、FedUtil の [メタデータの自動更新スケジューラ](https://msdn.microsoft.com/library/ee517272.aspx)を使用することもできます。
4. **[完了]** をクリックして更新プロセスを完了します。

### JSON Web Tokens (JWT) を使用する Web API

Web API を呼び出す際に、Azure AD によって発行された JWT トークンを要求の承認に使用するアプリケーションの場合、JWT トークンはサインオン要求の承認と同じ方法で承認されます。つまり、Azure AD の公開キーを使用して署名が確認されます。Web API アプリケーションは、最終的にはトークンへの署名にも同じ X509 証明書を使用するので、キーのロールオーバーを処理するように対応する必要があります。

Visual Studio 2013 で Web API テンプレートを使用して Web API を作成し、**[認証の変更]** メニューで **[組織アカウント]** を選択した場合、アプリケーションには既に必要なロジックが含まれています。手動で認証を構成する場合は、以下の手順に従って、自動的にキー情報を更新するように Web API を構成する方法を確認してください。

次のコード スニペットは、フェデレーション メタデータ ドキュメントから最新のキーを取得し、[JWT トークン ハンドラー](https://msdn.microsoft.com/library/dn205065.aspx)を使用してトークンを検証する方法を示します。このコード スニペットは、今後 Azure AD から受け取るトークンを検証するために、データベース、構成ファイルなどの何らかの独自のキャッシュ メカニズムを使用してキーを保存することが前提です。

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

### 最新のキーを手動で取得し、アプリケーションを更新する

アプリケーションの種類またはプラットフォームで、キーを更新するための自動メカニズムが現在サポートされていない場合は、以下の手順を実行します。

1. ブラウザーで、https://manage.windowsazure.com にアクセスし、自分のアカウントでサインインし、左側のメニューにある Active Directory アイコンをクリックします。
2. アプリケーションが登録されているディレクトリをクリックしてから、コマンド バーの **[エンドポイントの表示]** リンクをクリックします。
3. シングル サインオンとディレクトリ エンドポイントの一覧から、**[フェデレーション メタデータ ドキュメント]** リンクをコピーします。
4. ブラウザーで新しいタブを開き、今コピーした URL に移動します。フェデレーション メタデータ XML ドキュメントの内容が表示されます。このドキュメントの詳細については、フェデレーション メタデータに関するトピックを参照してください。
5. 新しいキーを使用するようにアプリケーションを更新するには、**<RoleDescriptor>** ブロックを 1 つずつ探し、各ブロックの **<X509Certificate>** 要素の値をコピーします。次に例を示します。
```
<RoleDescriptor xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType">
      <KeyDescriptor use="signing">
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
                <X509Data>
                    <X509Certificate>MIIDPjC…BcXWLAIarZ</X509Certificate>
```
6. **<X509Certificate>** 要素の値をコピーしたら、テキスト エディターを起動し、値を貼り付けます。必ず末尾のスペースを削除してから、**.cer** 拡張子で保存します。

これで、Azure AD の公開キーとして使用される X509 証明書が作成されました。サムプリントや有効期限などの証明書の詳細を使用すると、アプリケーションで現在使用されている証明およびサムプリントが有効であるかどうかを手動またはプログラムによってチェックすることができます。

<!---HONumber=AcomDC_0601_2016-->