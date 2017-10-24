---
title: "Azure Active Directory B2C: HTTP 基本認証を使用して RESTful サービスをセキュリティで保護する"
description: "サンプル: HTTP 基本認証を使用して Azure AD B2C でのカスタム REST API 要求交換をセキュリティで保護する方法のサンプル"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 226ecbe7e4d9d95fd4ba3255c95ec6baa1a86576
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-http-basic-authentication"></a>Azure Active Directory B2C: HTTP 基本認証を使用して RESTful サービスをセキュリティで保護する
[関連記事](active-directory-b2c-custom-rest-api-netfw.md)では、Azure AD B2C のユーザー体験を認証なしで統合する RESTful サービス (Web API) を作成しました。 この記事では、RESTful サービスに HTTP 基本認証を追加して、B2C を含む検証済みのユーザーのみが API にアクセスできるようにする方法を示します。 HTTP 基本認証では、ユーザー資格情報を使用するための資格情報 (アプリ ID とアプリ シークレット) をカスタム ポリシーに設定します。 

> [!NOTE]
>
>この記事では、[ASP.NET Web API における基本認証](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/basic-authentication)の動作方法について詳しく説明します。

## <a name="prerequisites"></a>前提条件
[REST API 要求交換の Azure AD B2C ユーザー体験への統合](active-directory-b2c-custom-rest-api-netfw.md)に関する記事の手順を完了していること。

## <a name="step-1-add-authentication-support"></a>手順 1: 認証サポートを追加する

### <a name="step-11-add-application-settings-to-projects-webconfig-file"></a>手順 1.1. アプリケーション設定をプロジェクトの web.config ファイルに追加する
1. 作成済みの Visual Studio プロジェクトを開きます。 
2. 次のアプリケーション設定を web.config ファイルの `appSettings` 要素の下に追加します。

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. パスワードを作成し、`WebApp:ClientSecret` 値を設定します。

    パスワードを作成するには、次の PowerShell を実行できます。 ただし、任意の値を使用できます。

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>手順 1.2. OWIN ライブラリをインストールする
まず、Visual Studio Package Manager Console を使用して、OWIN ミドルウェア NuGet パッケージをプロジェクトに追加します。

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-authentication-middleware-class"></a>手順 1.3 認証ミドルウェア クラスを追加する
`ClientAuthMiddleware.cs` クラスを `App_Start` フォルダーの下に追加します。 `App_Start` フォルダーを右クリックし、**[追加]** を選択し、**[クラス]** を選択します。

![App_Start フォルダーの下の ClientAuthMiddleware.cs クラスを追加します。](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

クラス名として「`ClientAuthMiddleware.cs`」と入力します。

![新しい C# クラスを作成する](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

`App_Start\ClientAuthMiddleware.cs` ファイルを開き、ファイルの内容を次のコードに置き換えます。


```C#

using Microsoft.Owin;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using System.Security.Principal;
using System.Text;
using System.Threading.Tasks;
using System.Web;

namespace Contoso.AADB2C.API
{
    /// <summary>
    /// Class to create a custom owin middleware to check for client authentication
    /// </summary>
    public class ClientAuthMiddleware
    {
        private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
        private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];

        /// <summary>
        /// Gets or sets the next owin middleware
        /// </summary>
        private Func<IDictionary<string, object>, Task> Next { get; set; }

        /// <summary>
        /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
        /// </summary>
        /// <param name="next"></param>
        public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
        {
            this.Next = next;
        }

        /// <summary>
        /// Invoke client authentication middleware during each request.
        /// </summary>
        /// <param name="environment">Owin environment</param>
        /// <returns></returns>
        public Task Invoke(IDictionary<string, object> environment)
        {
            // Get wrapper class for the environment
            var context = new OwinContext(environment);

            // Check whether the authorization header is available. This contains the credentials.
            var authzValue = context.Request.Headers.Get("Authorization");
            if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
            {
                // Process next middleware
                return Next(environment);
            }

            // Get credentials
            var creds = authzValue.Substring("Basic ".Length).Trim();
            string clientId;
            string clientSecret;

            if (RetrieveCreds(creds, out clientId, out clientSecret))
            {
                // Set transaction authenticated as client
                context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
            }

            return Next(environment);
        }

        /// <summary>
        /// Retrieve credentials from header
        /// </summary>
        /// <param name="credentials">Authorization header</param>
        /// <param name="clientId">Client identifier</param>
        /// <param name="clientSecret">Client secret</param>
        /// <returns>True if valid credentials were presented</returns>
        private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
        {
            string pair;
            clientId = clientSecret = string.Empty;

            try
            {
                pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
            }
            catch (FormatException)
            {
                return false;
            }
            catch (ArgumentException)
            {
                return false;
            }

            var ix = pair.IndexOf(':');
            if (ix == -1)
            {
                return false;
            }

            clientId = pair.Substring(0, ix);
            clientSecret = pair.Substring(ix + 1);

            // Return whether credentials are valid
            return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
        }
    }
}
```

### <a name="step-14-add-an-owin-startup-class"></a>手順 1.4. OWIN Startup クラスを追加する
OWIN Startup クラス (`Startup.cs`) を API に追加します。 プロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順に選択して、"OWIN" を検索します。

![OWIN Startup クラスを追加する](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

`Startup.cs` ファイルを開き、ファイルの内容を次のコードに置き換えます。

```C#
using Microsoft.Owin;
using Owin;

[assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
namespace Contoso.AADB2C.API
{
    public class Startup
    {
        public void Configuration(IAppBuilder app)
        {
                app.Use<ClientAuthMiddleware>();
        }
    }
}
```

### <a name="step-15-protect-identity-api-class"></a>手順 1.5. ID API クラスを保護する
Controllers\IdentityController.cs を開き、`[Authorize]` タグをコントローラー クラスに追加します。 `[Authorize]` タグは、コントローラーへのアクセスを承認要件を満たすユーザーに制限します。

![コントローラーに [Authorize] タグを追加する](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>手順 2: Azure に発行する
プロジェクトを発行するには、**ソリューション エクスプローラー**で **Contoso.AADB2C.API** プロジェクトを右クリックし、**[発行]** を選択します。

## <a name="step-3-add-the-restful-services-app-id--app-secret-to-azure-ad-b2c"></a>手順 3: RESTful サービス アプリのアプリ ID とアプリ シークレットを Azure AD B2C に追加する
RESTful サービスがクライアント ID (ユーザー名) とクライアント シークレットで保護された後、Azure AD B2C テナントに資格情報を格納する必要があります。 RESTful サービスの呼び出し中に、カスタム ポリシーによって資格情報が提供されます。  

### <a name="step-31-add-restful-services-client-id"></a>手順 3.1. RESTful サービスのクライアント ID を追加する
1.  Azure AD B2C テナントに移動し、**[B2C Settings]\(B2C 設定\)**  >  **[Identity Experience Framework]** の順に選択します。
2.  **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3.  **[+ 追加]** をクリックします。
4.  **[オプション]** には **[Manual] \(手動)** を使用します。
5.  **[名前]** には `B2cRestClientId` を使用します。  
    プレフィックス `B2C_1A_` が自動的に追加される場合があります。
6.  **[シークレット]**ボックスに、前に定義したアプリ ID を入力します。
7.  **[キー使用法]** には **[シークレット]** を使用します。
8.  **[作成]**
9.  キー `B2C_1A_B2cRestClientId` を作成したことを確認します。

### <a name="step-32-add-restful-services-client-secret"></a>手順 3.2. RESTful サービスのクライアント シークレットを追加する
1.  Azure AD B2C テナントに移動し、**[B2C Settings]\(B2C 設定\)**  >  **[Identity Experience Framework]** の順に選択します。
2.  **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。
3.  **[+ 追加]** をクリックします。
4.  **[オプション]** には **[Manual] \(手動)** を使用します。
5.  **[名前]** には `B2cRestClientSecret` を使用します。  
    プレフィックス `B2C_1A_` が自動的に追加される場合があります。
6.  **[シークレット]** ボックスに、前に定義したアプリ シークレットを入力します。
7.  **[キー使用法]** には **[シークレット]** を使用します。
8.  **[作成]**
9.  キー `B2C_1A_B2cRestClientSecret` を作成したことを確認します。

## <a name="step-4-change-the-technicalprofile-to-support-basic-authentication-in-your-extension-policy"></a>手順 4: 拡張ポリシーで基本認証をサポートするように `TechnicalProfile` を変更する
1.  作業ディレクトリから拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。
2.  `Id="REST-API-SignUp"` を含む `<TechnicalProfile>` ノードを見つけます
3.  `<Metadata>` 要素を見つけます
4.  `AuthenticationType` を `Basic` に変更します。
```xml
<Item Key="AuthenticationType">Basic</Item>
```
5.  `<Metadata>` 要素の直後に次の XML スニペットを追加します。  

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
XML スニペットを追加した後の `TechnicalProfile` は次のようになります。

![基本認証の XML 要素を追加する](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>手順 5: ポリシーをテナントにアップロードする

1.  [Azure ポータル](https://portal.azure.com)で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替え、**[Azure AD B2C]** をクリックします。
2.  **[Identity Experience Framework]** を選択します。
3.  **[すべてのポリシー]** をクリックします。
4.  **[Upload Policy]**(ポリシーのアップロード) を選択します。
5.  **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
6.  TrustFrameworkExtensions.xml を**アップロード**し、検証に失敗しないことを確認します。

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>手順 6: [今すぐ実行] を使用してカスタム ポリシーをテストする
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。

    >[!NOTE]
    >
    >    **[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 
    >    アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。

2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。
3.  **[名]** フィールドに「Test」と入力しようとすると、B2C によってページ上部にエラー メッセージが表示されます。

    ![ID API をテストする](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  **[名]** フィールドに "test" 以外の名前を入力すると、 B2C でユーザーがサインアップされ、アプリケーションに loyaltyNumber が送信されます。 この例ではこの JWT の番号に注意してください。

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="next-steps"></a>次のステップ
* [クライアント証明書を使用して RESTful API をセキュリティで保護する](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

## <a name="optional-download-the-complete-policy-files-and-code"></a>完全なポリシー ファイルとコードをダウンロードする (省略可能)
* これらのサンプル ファイルを使う代わりに、カスタム ポリシーの概要チュートリアルの完了後に独自のカスタム ポリシー ファイルを使ってシナリオを構築することをお勧めします。  [参照用のサンプル ポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)
* 完全なコードは、[参照用のVisual Studio ソリューションのサンプル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)からダウンロードできます