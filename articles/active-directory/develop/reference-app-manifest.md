---
title: Azure Active Directory のアプリケーション マニフェストについて | Microsoft Docs
description: Azure Active Directory アプリケーション マニフェストについて詳しく説明しています。このマニフェストは、Azure AD テナントでのアプリケーションの ID 構成を表しています。また、OAuth 認証、同意エクスペリエンスなどを利用しやすくするために使用されます。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, sureshja
ms.openlocfilehash: a0b39f5ac4347e48dc834bf5f5408c36df107aff
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601076"
---
# <a name="azure-active-directory-application-manifest"></a>Azure Active Directory アプリケーション マニフェスト
Azure AD と統合するアプリは、Azure AD テナントに登録する必要があります。 このアプリは、[Azure Portal](https://portal.azure.com) で (Azure AD ブレードの下の) アプリ マニフェストを使用して構成できます。

## <a name="manifest-reference"></a>マニフェスト リファレンス

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|キー  |値の型 |値の例  |説明  |
|---------|---------|---------|---------|
|appID     |  識別子の文字列       |""|  Azure AD によってアプリに割り当てられたアプリケーションの一意識別子。|
|appRoles     |    配列の型     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|アプリケーションが宣言する可能性のあるロールのコレクション。 これらのロールは、ユーザー、グループ、またはサービス プリンシパルに割り当てることができます。|
|availableToOtherTenants|ブール値|`true`|この値が true に設定されている場合、そのアプリケーションは他のテナントから使用できます。 false に設定されている場合、そのアプリは、それが登録されているテナントからしか使用できません。 詳細については、「[マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (AD) ユーザーがサインインできるようにする方法](howto-convert-app-to-be-multi-tenant.md)」を参照してください。 |
|displayName     |文字列         |`MyRegisteredApp`         |アプリケーションの表示名。 |
|errorURL     |文字列         |`http://MyRegisteredAppError`         |アプリケーションで発生したエラーの URL。 |
|groupMembershipClaims     |    文字列     |    `1`     |   アプリケーションが期待する、ユーザーまたは OAuth 2.0 アクセス トークンで発行される "グループ" 要求を構成するビットマスク。 ビットマスク値は、0: なし、1: セキュリティ グループと Azure AD ロール、2: 予約済み、および 4: 予約済みです。 ビットマスクを 7 に設定すると、サインイン ユーザーがメンバーになっているすべてのセキュリティ グループ、配布グループ、および Azure AD ディレクトリ ロールが取得されます。 |
|optionalClaims     |  文字列       |     `null`    |    この特定のアプリのセキュリティ トークン サービスによってトークンで返される[省略可能な要求](active-directory-optional-claims.md)。 |
|acceptMappedClaims    |      ブール値   | `true`        |    この値が true に設定されている場合、アプリケーションは、カスタム署名キーを指定せずに要求のマッピングを使用できます。|
|homepage     |  文字列       |`http://MyRegistererdApp`         |    アプリケーションのホーム ページへの URL。 |
|identifierUris     |  文字列配列       | `http://MyRegistererdApp`        |   その Azure AD テナント内で、またはアプリケーションがマルチテナントである場合は検証されたカスタム ドメイン内で Web アプリケーションを一意に識別するユーザー定義 URI。 |
|keyCredentials     |   配列の型      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   このプロパティは、アプリケーションで割り当てられた資格情報、文字列ベースの共有シークレット、および X.509 証明書への参照を保持します。 これらの資格情報は、アクセス トークンを要求するときに使用されます (そのアプリがリソースとしてではなく、クライアントとして機能している場合)。 |
|knownClientApplications     |     配列の型    |    [guid]     |     この値は、クライアント アプリケーションとカスタム Web API アプリケーションの 2 つの部分を含むソリューションがある場合に、同意をバンドルするために使用されます。 この値にクライアント アプリケーションの appID を入力すると、ユーザーは、クライアント アプリケーションに 1 回同意するだけで済みます。 Azure AD は、クライアントへの同意が Web API への暗黙的な同意を示し、クライアントと Web API の両方のサービス プリンシパルを同時に自動的にプロビジョニングすることを認識します。 クライアントと Web API アプリケーションの両方が同じテナントに登録されている必要があります。|
|logoutUrl     |   文字列      |     `http://MyRegisteredAppLogout`    |   アプリケーションからログアウトするための URL。 |
|oauth2AllowImplicitFlow     |   ブール値      |  `false`       |       この Web アプリケーションが OAuth2.0 暗黙的フロー トークンを要求できるかどうかを指定します。 既定値は false です。 このフラグは、ブラウザー ベースのアプリ (Javascript シングル ページ アプリなど) に使用されます。 |
|oauth2AllowUrlPathMatching     |   ブール値      |  `false`       |   OAuth 2.0 トークン要求の一部として、Azure AD がアプリケーションの replyUrls に対するリダイレクト URI のパスの照合を許可するかどうかを指定します。 既定値は false です。 |
|oauth2Permissions     | 配列の型         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Web API (リソース) アプリケーションがクライアント アプリケーションに公開する OAuth 2.0 アクセス許可スコープのコレクション。 これらのアクセス許可スコープは、同意中にクライアント アプリケーションに付与できます。 |
|oauth2RequiredPostResponse     | ブール値        |    `false`     |      OAuth 2.0 トークン要求の一部として、Azure AD が GET 要求ではなく、POST 要求を許可するかどうかを指定します。 既定値は false です。これは、GET 要求のみが許可されることを指定します。 
|objectId     | 識別子の文字列        |     ""    |    ディレクトリ内のアプリケーションの一意識別子。 この ID は、いずれかのプロトコル トランザクション内のアプリを識別するために使用される識別子ではありません。 これは、ディレクトリ クエリ内のオブジェクトを参照するために使用されます。|
|passwordCredentials     | 配列の型        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    keyCredentials プロパティの説明を参照してください。 |
|publicClient     |  ブール値       |      `false`   | アプリケーションがパブリック クライアント (モバイル デバイス上で実行されているインストール済みのアプリケーションなど) であるかどうかを指定します。 既定値は false です。 |
|supportsConvergence     |  ブール値       |   `false`      | このプロパティを編集しないでください。 既定値を受け入れてください。 |
|replyUrls     |  文字列配列       |   `http://localhost`     |  この複数値プロパティは、Azure AD がトークンを返すときに宛先として受け入れる登録された redirect_uri 値の一覧を保持します。 |
|requiredResourceAccess     |     配列の型    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   このアプリケーションがアクセスする必要があるリソースと、このアプリケーションがこれらの各リソースの下で必要とする OAuth アクセス許可スコープとアプリケーション ロールのセットを指定します。 この必要なリソース アクセスの事前構成によって、同意エクスペリエンスが促進されます。|
|resourceAppId     |    識別子の文字列     |  ""      |   アプリケーションがアクセスする必要があるリソースの一意識別子。 この値は、ターゲット リソース アプリケーションで宣言された appId に等しくなるようにしてください。 |
|resourceAccess     |  配列の型       | requiredResourceAccess プロパティの値の例を参照してください。 |   アプリケーションが指定されたリソースから必要とする OAuth2.0 アクセス許可スコープとアプリ ロールの一覧 (指定されたリソースの ID と型の値を含む)        |
|samlMetadataUrl    |文字列| `http://MyRegisteredAppSAMLMetadata` |アプリケーションの SAML メタデータへの URL。| 

## <a name="next-steps"></a>次の手順
* アプリケーションのアプリケーション オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、「[Azure AD のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)」を参照してください。
* Azure Active Directory (AD) の開発者向けの重要な概念の定義については、[Azure AD の 開発者向け用語集](developer-glossary.md)のページを参照してください。

次のコメント セクションを使用して、Microsoft のコンテンツの改善に役立つフィードバックを提供してください。

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:../../../azure-ad-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

