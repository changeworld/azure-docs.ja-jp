---
title: Microsoft Graph API を使用して SAML ベースのシングル サインオンを構成する
titleSuffix: Azure Active Directory
description: アプリケーションの複数のインスタンスを対象に SAML ベースのシングル サインオンを設定する必要がありますか? Microsoft Graph API を使用して SAML ベースのシングル サインオンの構成を自動化し、時間を節約する方法について説明します。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.reviewer: luleon
ms.openlocfilehash: fd59dcdd566110d1df02333f5701c0c206442d5d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846462"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>Microsoft Graph API で SAML ベースの SSO アプリ構成を自動化する

この記事では、Azure Active Directory (Azure AD) ギャラリーからアプリケーションを作成して構成する方法について説明します。 この記事では AWS を例として使用しますが、記事内の手順は Azure AD ギャラリーのどの SAML ベースのアプリにも使用できます。

**Microsoft Graph API を使用して SAML ベースのシングル サインオンの構成を自動化する手順**

| 手順  | 詳細  |
|---------|---------|
| [1.ギャラリー アプリケーションを作成する](#step-1-create-the-gallery-application) | API クライアントにサインインする <br> ギャラリー アプリケーションを取得する <br> ギャラリー アプリケーションを作成する|
| [2.シングル サインオンを構成する](#step-2-configure-single-sign-on) | アプリ オブジェクト ID とサービス プリンシパル オブジェクト ID を取得する <br> シングル サインオン モードを設定する <br> 基本的な SAML URL (識別子 URL、応答 URL、サインオン URL など) を設定する <br> アプリ ロールを追加する (オプション)|
| [3.要求のマッピングを構成する](#step-3-configure-claims-mapping) | 要求のマッピング ポリシーを作成する <br> 要求のマッピング ポリシーをサービス プリンシパルに割り当てる|
| [4.署名証明書を構成する](#step-4-configure-signing-certificate) | 証明書を作成する <BR> カスタム署名キーを追加する <br> カスタム署名キーをアクティブにする|
| [5.ユーザーを割り当てる](#step-5-assign-users) | アプリケーションにユーザーとグループを割り当てる
| [6.アプリケーション側を構成する](#step-6-configure-the-application-side)| Azure AD SAML メタデータを取得する

**このドキュメントで使用されているすべての API の一覧**

次の API を呼び出すための対応するアクセス許可があることを確認してください。

|リソースの種類 |Method |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[List applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[Instantiate applicationTemplate](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[servicePrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)|[Update servicePrincipal](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-beta&tabs=http) <br> [Create appRoleAssignments](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-beta&tabs=http) <br> [Assign claimsMappingPolicies](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[applications](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[アプリケーションの更新](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-beta&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [Create claimsMappingPolicy](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>この記事に示されている応答オブジェクトは、読みやすくするために短縮されている可能性があります。 実際の呼び出しからは、すべてのプロパティが返されます。

## <a name="step-1-create-the-gallery-application"></a>手順 1:ギャラリー アプリケーションを作成する

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Microsoft Graph エクスプローラー (推奨)、Postman、または使用しているその他の API クライアントにサインインする

1. [Microsoft Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)を開始します。
2. **[Microsoft アカウントでサインイン]** を選択し、Azure AD 全体管理者またはアプリ管理者の資格情報を使用してサインインします。
3. サインインに成功すると、左側のペインにユーザー アカウントの詳細が表示されます。

### <a name="retrieve-the-gallery-application-template-identifier"></a>ギャラリー アプリケーション テンプレート識別子を取得する

Azure AD アプリケーション ギャラリーのアプリケーションにはそれぞれ、そのアプリケーションのメタデータを記述する[アプリケーション テンプレート](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http)があります。 このテンプレートを使用して、管理用のテナントにアプリケーションとサービス プリンシパルのインスタンスを作成できます。

#### <a name="request"></a>Request

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>Response

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>ギャラリー アプリケーションを作成する

前のステップで取得したアプリケーションのテンプレート ID を使用して、ご自身のテナント内にアプリケーションとサービス プリンシパルの[インスタンスを作成](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)します。

#### <a name="request"></a>Request

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Response


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>手順 2:Configure single sign-on

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>アプリ オブジェクト ID とサービス プリンシパル オブジェクト ID を取得する

前出の呼び出しからの応答を使用して、アプリケーション オブジェクト ID とサービス プリンシパル オブジェクト ID を取得し、保存します。

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>シングル サインオン モードを設定する

この例では、`saml` を[リソースの種類 servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) でのシングル サインオン モードとして設定します。 この他に構成できる SAML SSO プロパティは、`notificationEmailAddresses`、`loginUrl`、および `samlSingleSignOnSettings.relayState` です。

#### <a name="request"></a>Request

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml",
    "notificationEmailAddresses": [
        "john@contoso.com"
      ]
}
```

#### <a name="response"></a>Response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>基本的な SAML URL (識別子 URL、応答 URL、サインオン URL など) を設定する

アプリケーション オブジェクトで AWS の識別子 URL と応答 URL を設定します。

#### <a name="request"></a>Request

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>Response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>アプリ ロールを追加する (オプション)

アプリケーションのトークンにロール情報が必要である場合は、アプリケーション オブジェクトにロールの定義を追加します。 AWS の場合は、[ユーザー プロビジョニングを有効にする](../app-provisioning/application-provisioning-configure-api.md)ことで、当該 AWS アカウントからすべてのロールをフェッチできます。 

詳細については、[SAML トークンで発行されるロール要求の構成](../develop/active-directory-enterprise-app-role-management.md)に関するページを参照してください。

> [!NOTE] 
> アプリ ロールを追加する際に、既定のアプリ ロール msiam_access を変更しないようにしてください。 

#### <a name="request"></a>Request

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/beta/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>Response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>手順 3:要求のマッピングを構成する

### <a name="create-claims-mapping-policy"></a>要求のマッピング ポリシーを作成する

基本要求に加えて、Azure AD に対する次の要求を SAML トークンで発行するように構成します。

| 要求名 | source  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userprincipalname |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| roles | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userprincipalname |

#### <a name="request"></a>Request

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>Response

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>要求のマッピング ポリシーをサービス プリンシパルに割り当てる

#### <a name="request"></a>Request

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/beta/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>Response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>手順 4:署名証明書を構成する

[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) API を使用した場合、既定では署名証明書が作成されません。 カスタム署名証明書を作成してアプリケーションに割り当てます。 

### <a name="create-a-custom-signing-certificate"></a>カスタム署名証明書を作成する

テストのために、次の PowerShell コマンドを使用して自己署名証明書を取得します。 社内のセキュリティに関するベスト プラクティスを使用して、運用環境用の署名証明書を作成します。

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>カスタム署名キーを追加する

次の情報をサービス プリンシパルに追加します。

* 秘密キー
* Password
* 公開キー 

PFX ファイルから、Base64 でエンコードされた秘密キーと公開キーを抽出します。 プロパティの詳細については、[リソースの種類 keyCredential](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0)に関するページを参照してください。

"署名" に使用される keyCredential の keyId が、passwordCredential の keyId と一致していることを確認します。

証明書の拇印のハッシュを取得することで、`customkeyIdentifier` を生成できます。

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>Request

> [!NOTE]
> KeyCredentials プロパティの "key" 値は、読みやすくするために短縮されています。

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>Response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>カスタム署名キーをアクティブにする

`preferredTokenSigningKeyThumbprint` プロパティを、Azure AD で SAML 応答の署名に使用する証明書の拇印に設定する必要があります。 

#### <a name="request"></a>Request

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>Response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>手順 5:ユーザーの割り当て

### <a name="assign-users-and-groups-to-the-application"></a>アプリケーションにユーザーとグループを割り当てる

次のユーザーをサービス プリンシパルに割り当て、AWS_Role1 を割り当てます。 

| 名前  | id  |
|---------|---------|
| ユーザー ID (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| 種類 (principalType) | User |
| アプリ ロール ID (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| サービス プリンシパル ID (resourceId) | 515f62cb-d18a-4dca-bec3-bb0bf31deeea |

#### <a name="request"></a>Request

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```
#### <a name="response"></a>Response

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```

詳細については、リソースの種類 [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-beta) を参照してください。

## <a name="step-6-configure-the-application-side"></a>手順 6:アプリケーション側を構成する

### <a name="get-azure-ad-saml-metadata"></a>Azure AD SAML メタデータを取得する

次の URL を使用して、特定の構成済みアプリケーションの Azure AD SAML メタデータを取得します。 メタデータには、署名証明書、Azure AD entityID、Azure AD SingleSignOnService などの情報が含まれています。

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>次のステップ
- [Microsoft Graph API を使用してプロビジョニングを構成する](../app-provisioning/application-provisioning-configure-api.md)
- [AD FS アプリケーション アクティビティ レポートを使用してアプリケーションを Azure AD に移行する](migrate-adfs-application-activity.md)
