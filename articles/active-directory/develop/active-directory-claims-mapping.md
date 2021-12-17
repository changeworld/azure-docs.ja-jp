---
title: Azure AD テナントのアプリ要求をカスタマイズする (PowerShell)
titleSuffix: Microsoft identity platform
description: 特定の Azure Active Directory テナントのアプリケーションに対するトークンに組み込むクレーム (属性情報) をカスタマイズする方法を説明します。
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 06/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 71677a085627f46a995530c522a3a480be188042
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351006"
---
# <a name="customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant"></a>テナントに存在する特定のアプリに対するトークンに組み込むクレームをカスタマイズする

要求とは、そのユーザーに発行するトークンの中にあるユーザーに関する ID プロバイダーが提示した情報を指します。 テナント管理者は、自身のテナントの特定のアプリケーションに対するトークンに組み込むクレームをカスタマイズできます。 要求のマッピング ポリシーを使用すると、次の操作を行うことができます。

- トークンに組み込むクレームを選ぶ。
- まだ存在しない種類のクレームを作成する。
- 特定のクレームに入れるデータのソースを指定、変更する。

クレームのカスタマイズでは、WS-Fed、SAML、OAuth、OpenID Connect のプロトコルに対するクレームマッピング ポリシーを構成できます。

> [!NOTE]
> これは、Azure portal で提供している[クレームのカスタマイズ](active-directory-saml-claims-customization.md)の後継機能です。 同一のアプリケーションに対し、このドキュメントで詳述する Microsoft Graph または Powershell を利用した方法に加えて、Azure portal でもクレームをカスタマイズした場合、そのアプリケーションのトークンでは、Azure portal での構成は無視されます。 このドキュメントで詳しく説明した方法で行った構成は、ポータルには反映されません。

この記事では、[クレームマッピング ポリシーの種類](reference-claims-mapping-policy-type.md)の使い方が分かるように、いくつかのよくある使用方法を説明します。

## <a name="get-started"></a>はじめに

次の例では、サービス プリンシパルのポリシーを作成、更新、リンク、および削除します。 クレームマッピング ポリシーは、サービス プリンシパル オブジェクトにのみ割り当てることができます。 Azure AD に慣れていない場合は、こうした例を確認する前に、[Azure AD テナントを取得する方法](quickstart-create-new-tenant.md)について学習することをお勧めします。

クレームマッピング ポリシーを作成するときは、トークンのディレクトリ スキーマ拡張属性からクレームを発信するよう設定することもできます。 `ClaimsSchema` 要素の *ID* ではなく、拡張属性の *ExtensionID* を使用します。  拡張属性の詳細については、[ディレクトリ スキーマ拡張属性の使用](active-directory-schema-extensions.md)に関するページをご覧ください。

> [!NOTE]
> クレームマッピング ポリシーを構成するには、[パブリック プレビュー版 Azure AD PowerShell モジュール](https://www.powershellgallery.com/packages/AzureADPreview)が必要です。 PowerShell モジュールはプレビュー版ですが、Azure のクレームマッピングおよびトークン作成ランタイムは一般公開しています。 プレビュー版の PowerShell モジュールに更新する場合、構成スクリプトの更新あるいは変更が必要な場合があります。 

使用を開始するには、次の手順を実行します。

1. 最新版の [Azure AD PowerShell モジュール パブリック プレビュー リリース](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードします。
1. [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0-preview&preserve-view=true) を実行して Azure AD 管理者アカウントにサインインします。 新しいセッションを開始するたびにこのコマンドを実行します。

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. 組織に作成されているすべてのポリシーを表示するには、次のコマンドを実行します。 ポリシーが想定どおりに作成されていることを確認するために、次のシナリオのほとんどの操作の後にこのコマンドを実行することをお勧めします。

   ``` powershell
   Get-AzureADPolicy
   ```

次に、クレームマッピング ポリシーを作成してサービス プリンシパルに割り当てます。  よくある使用方法については、これらの例をご覧ください。
- [トークンから基本要求を除外する](#omit-the-basic-claims-from-tokens)
- [EmployeeID と TenantCountry を要求としてトークンに含める](#include-the-employeeid-and-tenantcountry-as-claims-in-tokens)
- [トークンで要求変換を使用する](#use-a-claims-transformation-in-tokens)

クレームマッピング ポリシーを作成したら、トークンにカスタム クレームを組み込むことを承認するよう、アプリケーションを設定します。  詳しくは「[セキュリティに関する考慮事項](#security-considerations)」を読んでください。

## <a name="omit-the-basic-claims-from-tokens"></a>トークンから基本要求を除外する

この例では、リンク サービス プリンシパルに対して発行されたトークンから、[基本要求セット](reference-claims-mapping-policy-type.md#claim-sets)を削除するポリシーを作成します。

1. クレームマッピング ポリシーを作成します。 このポリシーは、特定のサービス プリンシパルにリンクされ、トークンから基本要求セットを削除します。
   1. ポリシーを作成するには、このコマンドを実行します。

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. 新しいポリシーを表示し、ポリシーの ObjectId を取得するには、次のコマンドを実行します。

      ``` powershell
      Get-AzureADPolicy
      ```
1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。
   1. 組織のすべてのサービス プリンシパルを表示するには、[Microsoft Graph API にクエリを実行](/graph/traverse-the-graph)します。 または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) で、Azure AD アカウントにサインインします。
   2. サービス プリンシパルの ObjectId がある場合は、次のコマンドを実行します。

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>EmployeeID と TenantCountry を要求としてトークンに含める

この例では、リンクされたサービス プリンシパルに対して発行されたトークンに、EmployeeID と TenantCountry を追加するポリシーを作成します。 EmployeeID は、SAML トークンと JWT の両方で名前要求の種類として出力されます。 TenantCountry は、SAML トークンと JWT の両方で国/リージョン要求の種類として出力されます。 この例では、操作を続行し、トークンに基本要求セットを含めます。

1. クレームマッピング ポリシーを作成します。 このポリシーは、特定のサービス プリンシパルにリンクされ、EmployeeID 要求と TenantCountry 要求をトークンに追加します。
   1. ポリシーを作成するには、次のコマンドを実行します。

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"employeeid"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

      > [!WARNING]
      > ディレクトリの拡張属性に対するクレームマッピング ポリシーを定義するときは、`ClaimsSchema` 配列の中で `ID` プロパティの代わりに `ExtensionID` プロパティを使用します。

   2. 新しいポリシーを表示し、ポリシーの ObjectId を取得するには、次のコマンドを実行します。

      ``` powershell
      Get-AzureADPolicy
      ```
1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。
   1. 組織のすべてのサービス プリンシパルを表示するには、[Microsoft Graph API にクエリを実行](/graph/traverse-the-graph)します。 または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) で、Azure AD アカウントにサインインします。
   2. サービス プリンシパルの ObjectId がある場合は、次のコマンドを実行します。

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>トークンで要求変換を使用する

この例では、リンクされたサービス プリンシパルに対して発行された JWT に、カスタム要求 "JoinedData" を出力するポリシーを作成します。 この要求には、ユーザー オブジェクトの extensionattribute1 属性に格納されたデータと ".sandbox" を結合して作成された値が追加されます。 この例では、トークンで基本要求セットを除外します。

1. クレームマッピング ポリシーを作成します。 このポリシーは、特定のサービス プリンシパルにリンクされ、EmployeeID 要求と TenantCountry 要求をトークンに追加します。
   1. ポリシーを作成するには、次のコマンドを実行します。

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 新しいポリシーを表示し、ポリシーの ObjectId を取得するには、次のコマンドを実行します。

      ``` powershell
      Get-AzureADPolicy
      ```
1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。
   1. 組織のすべてのサービス プリンシパルを表示するには、[Microsoft Graph API にクエリを実行](/graph/traverse-the-graph)します。 または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) で、Azure AD アカウントにサインインします。
   2. サービス プリンシパルの ObjectId がある場合は、次のコマンドを実行します。

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>セキュリティに関する考慮事項

トークンを受信するアプリケーションは、クレーム値が Azure AD によって正式に発行され、改ざんできないという事実に依存します。 ただし、クレームマッピング ポリシーによってトークンの内容を変更すると、この前提は通用しなくなります。 悪意のある目的で作成されたクレームマッピング ポリシーからアプリケーションを保護するため、アプリケーションでは、クレームマッピング ポリシーの作成者がトークンを変更したことを、明示的に確認する必要があります。 これは、次のいずれかの方法で実施できます。

- [カスタム署名キーを構成する](#configure-a-custom-signing-key)
- または、[アプリケーション マニフェストを編集](#update-the-application-manifest)して、マッピングしたクレームを承認する。
 
これを行わないと、Azure AD は[`AADSTS50146` エラー コード](reference-aadsts-error-codes.md#aadsts-error-codes)を返します。

### <a name="configure-a-custom-signing-key"></a>カスタム署名キーを構成する

マルチテナントのアプリでは、カスタム署名キーを使用するべきです。  アプリ マニフェストには `acceptMappedClaims` を設定しないでください。 Azure portal でアプリをセットアップすると、アプリ登録オブジェクトとサービス プリンシパルがテナントに作成されます。  そのアプリでは Azure グローバル サインイン キーを使用します。このキーではトークンのクレームをカスタマイズできません。  トークンにカスタム クレームを使用するには、証明書からカスタム サインイン キーを作成してサービス プリンシパルに追加します。  テスト目的で、自己署名証明書を使用してもかまいません。 カスタム署名キーを設定した後、アプリケーションのコードで[トークン署名キーを承認する](#validate-token-signing-key)必要があります。

次の情報をサービス プリンシパルに追加します。

- 秘密キー ([キー資格情報](/graph/api/resources/keycredential))
- パスワード ([パスワード資格情報](/graph/api/resources/passwordcredential))
- 公開キー ([キー資格情報](/graph/api/resources/keycredential))

証明書からエクスポートした PFX ファイルから、base-64 でエンコードした秘密キーと公開キーを抽出します。 “Sign” に使用する `keyCredential` の `keyId` が `passwordCredential` の `keyId` に一致することを確認します。 証明書の拇印のハッシュを取得することで、`customkeyIdentifier` を生成できます。

#### <a name="request"></a>Request

カスタム署名キーをサービス プリンシプルに追加する HTTP PATCH 要求の形式を、次に示します。  `keyCredentials` プロパティの “key” の値は、読みやすいように短縮してあります。 この値は base-64 でエンコードしてあります。 秘密キーでは、プロパティの “usage” (用途) は “Sign” です。 公開キーでは、プロパティの “usage” (用途) は “Verify” です。

```
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json
Authorization: Bearer {token}

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "X509CertAndPassword",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=contoso"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=contoso"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "mypassword"
        }
    ]
}
```

#### <a name="configure-a-custom-signing-key-using-powershell"></a>PowerShell でカスタム署名キーを設定する

PowerShell で [MSAL Public Client Application のインスタンスを作成し](msal-net-initializing-client-applications.md#initializing-a-public-client-application-from-code)、[Authorization Code Grant](v2-oauth2-auth-code-flow.md) フローで Microsoft Graph に対する委任権限アクセス トークンを取得します。 このアクセス トークンを使用して Microsoft Graph を呼び出し、サービス プリンシパルに対してカスタム署名キーを設定します。 カスタム署名キーを設定した後、アプリケーションのコードで[トークン署名キーを承認する](#validate-token-signing-key)必要があります。

このスクリプトを実行するには、次のものが必要です。
1. アプリケーションのサービス プリンシパルのオブジェクト ID。Azure portal の [[Enterprise Applications]\(エンタープライズ アプリケーション\)](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) の目的のアプリケーションの項目の **[Overview]\(概要\)** ブレードで確認できます。
2. ユーザーをサインインさせ、Microsoft Graph を呼び出すアクセス トークンを取得するためのアプリの登録。 Azure portal の [[App registrations]\(アプリの登録)\](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) の目的のアプリケーションの項目の **[Overview]\(概要\)** ブレードで、このアプリのアプリケーション (クライアント) ID を取得します。 アプリを登録するには、次のものを設定する必要があります。
    - **[Mobile and desktop applications]\(モバイルおよびデスクトップアプリケーション\)** プラットフォーム構成に、リダイレクト URI として http://localhost を設定する。
    - **[API permissions]\(API 使用権限\)** で、Microsoft Graph 委任権限の **Application.ReadWrite.All** と **User.Read** を設定する (これらの権限には管理者の同意を与える必要があります)。
3. ログインして Microsoft Graph アクセス トークンを取得するユーザー。 このユーザーは、次の Azure AD 管理者ロールのどれかを持っている必要があります (サービス プリンシパルの更新に必要です)。
    - クラウド アプリケーション管理者
    - アプリケーション管理者
    - グローバル管理者
4. アプリケーションのカスタム署名キーに設定する証明書。 自己署名証明書を作成する方法と、信頼できる証明機関から証明書を取得する方法があります。 スクリプトでは、証明書に含まれる次のものを使用します。
    - 公開キー (通常 .cer ファイル)
    - PKCS#12 形式の秘密キー (.pfx ファイル)
    - 秘密キーのパスワード (.pfx ファイル)

> [!IMPORTANT]
> Azure AD では他の形式をサポートしていないため、秘密キーは PKCS#12 形式である必要があります。 誤った形式を使用した場合、証明書情報を保持する `keyCredentials` の存在するサービス プリンシパルを Microsoft Graph で PATCH すると、“Invalid certificate: Key value is invalid certificate” (無効な証明書: キーの値が無効な証明書です) というエラーが発生する場合があります。

```powershell

$fqdn="fourthcoffeetest.onmicrosoft.com" # this is used for the 'issued to' and 'issued by' field of the certificate
$pwd="mypassword" # password for exporting the certificate private key
$location="C:\\temp" # path to folder where both the pfx and cer file will be written to

# Create a self-signed cert
$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx"
 
# Export the public and private keys
Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile

$ClientID = "<app-id>"
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "fourthcoffeetest.onmicrosoft.com"
$redirectURL = "http://localhost" # this reply URL is needed for PowerShell Core 
[string[]] $Scopes = "https://graph.microsoft.com/.default"
$pfxpath = $pfxFile # path to pfx file
$cerpath = $cerFile # path to cer file
$SPOID = "<service-principal-id>"
$graphuri = "https://graph.microsoft.com/v1.0/serviceprincipals/$SPOID"
$password = $pwd  # password for the pfx file
 
 
# choose the correct folder name for MSAL based on PowerShell version 5.1 (.Net) or PowerShell Core (.Net Core)
 
if ($PSVersionTable.PSVersion.Major -gt 5)
    { 
        $core = $true
        $foldername =  "netcoreapp2.1"
    }
else
    { 
        $core = $false
        $foldername = "net45"
    }
 
# Load the MSAL/microsoft.identity/client assembly -- needed once per PowerShell session
[System.Reflection.Assembly]::LoadFrom((Get-ChildItem C:/Users/<username>/.nuget/packages/microsoft.identity.client/4.32.1/lib/$foldername/Microsoft.Identity.Client.dll).fullname) | out-null
  
$global:app = $null
  
$ClientApplicationBuilder = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($ClientID)
[void]$ClientApplicationBuilder.WithAuthority($("$loginURL/$tenantdomain"))
[void]$ClientApplicationBuilder.WithRedirectUri($redirectURL)
 
$global:app = $ClientApplicationBuilder.Build()
  
Function Get-GraphAccessTokenFromMSAL {
    [Microsoft.Identity.Client.AuthenticationResult] $authResult  = $null
    $AquireTokenParameters = $global:app.AcquireTokenInteractive($Scopes)
    [IntPtr] $ParentWindow = [System.Diagnostics.Process]::GetCurrentProcess().MainWindowHandle
    if ($ParentWindow)
    {
        [void]$AquireTokenParameters.WithParentActivityOrWindow($ParentWindow)
    }
    try {
        $authResult = $AquireTokenParameters.ExecuteAsync().GetAwaiter().GetResult()
    }
    catch {
        $ErrorMessage = $_.Exception.Message
        Write-Host $ErrorMessage
    }
     
    return $authResult
}
  
$myvar = Get-GraphAccessTokenFromMSAL
if ($myvar)
{
    $GraphAccessToken = $myvar.AccessToken
    Write-Host "Access Token: " $myvar.AccessToken
    #$GraphAccessToken = "eyJ0eXAiOiJKV1QiL ... iPxstltKQ"
    
 
    #  this is for PowerShell Core
    $Secure_String_Pwd = ConvertTo-SecureString $password -AsPlainText -Force
 
    # reading certificate files and creating Certificate Object
    if ($core)
    {
        $pfx_cert = get-content $pfxpath -AsByteStream -Raw
        $cer_cert = get-content $cerpath -AsByteStream -Raw
        $cert = Get-PfxCertificate -FilePath $pfxpath -Password $Secure_String_Pwd
    }
    else
    {
        $pfx_cert = get-content $pfxpath -Encoding Byte
        $cer_cert = get-content $cerpath -Encoding Byte
        # Write-Host "Enter password for the pfx file..."
        # calling Get-PfxCertificate in PowerShell 5.1 prompts for password
        # $cert = Get-PfxCertificate -FilePath $pfxpath
        $cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new($pfxpath, $password)
    }
 
    # base 64 encode the private key and public key
    $base64pfx = [System.Convert]::ToBase64String($pfx_cert)
    $base64cer = [System.Convert]::ToBase64String($cer_cert)
 
    # getting id for the keyCredential object
    $guid1 = New-Guid
    $guid2 = New-Guid
 
    # get the custom key identifier from the certificate thumbprint:
    $hasher = [System.Security.Cryptography.HashAlgorithm]::Create('sha256')
    $hash = $hasher.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($cert.Thumbprint))
    $customKeyIdentifier = [System.Convert]::ToBase64String($hash)
 
    # get end date and start date for our keycredentials
    $endDateTime = ($cert.NotAfter).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
    $startDateTime = ($cert.NotBefore).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
 
    # building our json payload
    $object = [ordered]@{    
    keyCredentials = @(       
         [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid1
            startDateTime = $startDateTime 
            type = "X509CertAndPassword"
            usage = "Sign"
            key = $base64pfx
            displayName = "CN=fourthcoffeetest" 
        },
        [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid2
            startDateTime = $startDateTime 
            type = "AsymmetricX509Cert"
            usage = "Verify"
            key = $base64cer
            displayName = "CN=fourthcoffeetest"   
        }
        )  
    passwordCredentials = @(
        [ordered]@{
            customKeyIdentifier = $customKeyIdentifier
            keyId = $guid1           
            endDateTime = $endDateTime
            startDateTime = $startDateTime
            secretText = $password
        }
    )
    }
 
    $json = $object | ConvertTo-Json -Depth 99
    Write-Host "JSON Payload:"
    Write-Output $json
 
    # Request Header
    $Header = @{}
    $Header.Add("Authorization","Bearer $($GraphAccessToken)")
    $Header.Add("Content-Type","application/json")
 
    try 
    {
        Invoke-RestMethod -Uri $graphuri -Method "PATCH" -Headers $Header -Body $json
    } 
    catch 
    {
        # Dig into the exception to get the Response details.
        # Note that value__ is not a typo.
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__ 
        Write-Host "StatusDescription:" $_.Exception.Response.StatusDescription
    }
 
    Write-Host "Complete Request"
}
else
{
    Write-Host "Fail to get Access Token"
}
```

#### <a name="validate-token-signing-key"></a>トークン署名キーを承認する
要求のマッピングが有効なアプリでは、[OpenID Connect メタデータ要求](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)に `appid={client_id}` を追加して、トークン署名キーを検証する必要があります。 使用する必要がある OpenID Connect メタデータ ドキュメントのフォーマットは次のとおりです。

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>アプリケーション マニフェストを更新する

シングル テナントのアプリでは、[アプリケーション マニフェスト](reference-app-manifest.md)で `acceptMappedClaims` プロパティを `true` に設定できます。  「[apiApplication リソースの種類](/graph/api/resources/apiapplication#properties)」に記載されているように、これにより、カスタム署名キーを指定せずに、アプリケーションでクレーム マッピングが使用できます。 

> [!WARNING]
> マルチテナント アプリでは `acceptMappedClaims` プロパティを `true` に設定しないでください。悪意のある者がアプリのクレームマッピング ポリシーを作成することを許可してしまう場合があります。

これを行うには、要求されたトークンの対象ユーザーが Azure AD テナントの検証済みドメイン名を使用する必要があります。つまり、(アプリケーション マニフェスト内で `identifierUris` によって表される) `Application ID URI` を例えば `https://contoso.com/my-api` に設定するか、(単に既定のテナント名を使用して) `https://contoso.onmicrosoft.com/my-api` に設定する必要があります。

検証済みのドメインを使用していない場合、Azure AD は `AADSTS501461` エラー コードを、 *[AcceptMappedClaims is only supported for a token audience matching the application GUID or an audience within the tenant's verified domains. Either change the resource identifier, or use an application-specific signing key]\(AcceptMappedClaims は、アプリケーション GUID に一致するトークン対象ユーザー、またはテナントの検証済みドメイン内の対象ユーザーでのみサポートされています。リソース識別子を変更するか、アプリケーション固有の署名キーを使用してください\)* というメッセージと共に返します。

## <a name="next-steps"></a>次のステップ

- 詳しくは、[クレームマッピング ポリシーの種類](reference-claims-mapping-policy-type.md)に関する参考記事をご覧ください。
- SAML トークンで発行された要求を Azure portal でカスタマイズする方法については、「[方法: エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](active-directory-saml-claims-customization.md)」を参照してください。
- 拡張属性の詳細については、[要求でのディレクトリ スキーマ拡張属性の使用](active-directory-schema-extensions.md)に関するページをご覧ください。
