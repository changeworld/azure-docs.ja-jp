---
title: Azure AD スキーマとカスタム式について
description: このトピックでは、Azure AD スキーマ、プロビジョニング エージェントがフローする属性、およびカスタム式について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae594bcc20e3c4ed1c6fbd0333699de8c9f4452
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795530"
---
# <a name="understanding-the-azure-ad-schema"></a>Azure AD スキーマについて
ディレクトリのような Azure AD のオブジェクトは、ユーザー、グループ、連絡先などを表す、プログラムによる高レベルのデータ構造です。  Azure AD で新しいユーザーまたは連絡先を作成すると、そのオブジェクトの新しいインスタンスが作成されます。  これらのインスタンスは、プロパティに基づいて区別できます。

Azure AD のプロパティは、Azure AD 内のオブジェクトのインスタンスに関する情報を格納する要素です。  

Azure AD スキーマでは、エントリでプロパティが使用できるルール、それらのプロパティが持つことができる値の種類、およびユーザーがこれらの値を操作する方法を定義します。 

Azure AD には、2 種類のプロパティがあります。  プロパティは次のとおりです。
- **組み込みプロパティ** – Azure AD スキーマで事前に定義されているプロパティです。  これらのプロパティはさまざまな用途を提供し、アクセスできる場合と使用できない場合があります。
- **ディレクトリ拡張機能** – 自分用に Azure AD をカスタマイズできるように提供されるプロパティです。  たとえば、特定の属性を持つオンプレミスの Active Directory を拡張し、その属性をフローさせる場合は、提供されているカスタム プロパティの 1 つを使用できます。 

## <a name="attributes-and-expressions"></a>属性と式
ユーザーなどのオブジェクトが Azure AD にプロビジョニングされると、そのユーザー オブジェクトの新しいインスタンスが作成されます。  この作成物には、そのオブジェクトのプロパティが含まれています。これは属性とも呼ばれます。  初期状態では、新しく作成されたオブジェクトの属性は、同期規則によって決定される値に設定されます。  これらの属性は、クラウド プロビジョニング エージェントを使用して最新の状態に保たれます。

![](media/concept-attributes/attribute1.png)

たとえば、ユーザーがマーケティング部門の一員である場合は、プロビジョニング時に最初に Azure AD department 属性が作成され、次にその値が Marketing に設定されます。  しかし、6 か月後には彼らは営業部門に変わります。  オンプレミスの AD department 属性は、Sales に変更されます。  この変更は Azure AD に同期され、Azure AD ユーザー オブジェクトに反映されます。

属性の同期は、直接行うことができます。この場合、Azure AD の値は、オンプレミスの属性の値に直接設定されます。  または、この同期を処理するプログラム式が存在する場合もあります。  値を設定するために何らかのロジック、または決定を行う必要がある場合は、プログラム式が必要になります。

たとえば、メールの属性 ("john.smith@contoso.com") を持っていて、"@contoso.com" の部分を取り除き、値 "john. smith" だけをフローさせる必要がある場合は、次のようにします。

`Replace([mail], "@contoso.com", , ,"", ,)`  

**サンプル入力/出力:** <br>

* **入力** (mail): "john.smith@contoso.com"
* **出力**: "john. smith"

カスタム式の記述および構文の詳細については、「[Azure Active Directory での属性マッピングのための式の作成](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)」を参照してください。

次の一覧では、一般的な属性と、それらが Azure AD に同期される方法を示します。


|オンプレミスの Active Directory|マッピングの種類|Azure AD|
|-----|-----|-----|
|cn|直接|commonName
|countryCode|直接|countryCode|
|displayName|直接|displayName|
|givenName|式|givenName|
|objectGUID|直接|sourceAnchorBinary|  
|userprincipalName|直接|userPrincipalName|
|ProxyAdress|直接|ProxyAddress|

## <a name="viewing-the-schema"></a>スキーマの表示
スキーマを表示して確認するには、次の手順を実行します。

1.  [Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)に移動します。
2.  自分の全体管理者アカウントでサインインします
3.  左側で、 **[アクセス許可の変更]** をクリックし、**Directory.ReadWrite.All** が同意になっていることを確認します。
4.  クエリ https://graph.microsoft.com/beta/serviceprincipals/ を実行します。  このクエリは、サービス プリンシパルの一覧を返します。
5.  "appDisplayName":"Active Directory to Azure Active Directory Provisioning" を見つけ、"id:" の値をメモします。
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
6. {Service Principal id} を実際の値に置き換えて、次のクエリを実行します。`https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`
7. "id":"AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976" セクションを見つけ、"id:" をメモします。
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
8. クエリ `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema` を実行します。
 
    例: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

 {Service Principal Id} と {AD2ADD Provisioning Id} を実際の値に置き換えます。

9. このクエリでは、スキーマが返されます。
  ![](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>次の手順 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)
