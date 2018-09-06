---
title: Azure Active Directory のアプリ マニフェストについて | Microsoft Docs
description: Azure Active Directory のアプリ マニフェストについて詳しく説明しています。このマニフェストは、Azure AD テナントでのアプリケーションの ID 構成を表しています。また、OAuth 認証、同意エクスペリエンスなどを利用しやすくするために使用されます。
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
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127022"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory のアプリ マニフェスト

Azure Active Directory (Azure AD) と統合するアプリは、Azure AD テナントに登録する必要があります。 アプリの構成は、[Azure portal](https://portal.azure.com) で行うことができます。**[Azure Active Directory]** を選択して、構成するアプリを選択し、**[マニフェスト]** を選択してください。

## <a name="manifest-reference"></a>マニフェスト リファレンス

> [!NOTE]
> 説明を表示できない場合は、ブラウザー ウィンドウを最大化するか、スクロール/スワイプして説明を表示してください。

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| キー  | 値の型 | 値の例 | 説明  |
|---------|---------|---------|---------|
| `appID` | 識別子の文字列 | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | Azure AD によってアプリに割り当てられた一意識別子を指定します。 |
| `appRoles` | 配列の型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | アプリが宣言する可能性のあるロールのコレクションを指定します。 これらのロールは、ユーザー、グループ、またはサービス プリンシパルに割り当てることができます。 |
| `availableToOtherTenants`| ブール値 | `true` | この値が true に設定されている場合、そのアプリは他のテナントから使用できます。 false に設定されている場合、そのアプリは、それが登録されているテナントからしか使用できません。 詳細については、[マルチテナント アプリ パターンを使用してすべての Azure AD ユーザーがサインインできるようにする方法](howto-convert-app-to-be-multi-tenant.md)に関するページを参照してください。 |
| `displayName` | 文字列 | `MyRegisteredApp` | アプリの表示名。 |
| `errorURL` | 文字列 | `http://MyRegisteredAppError` | アプリで発生したエラーの URL。 |
| `groupMembershipClaims` | 文字列 | `1` | アプリが期待する、ユーザーまたは OAuth 2.0 アクセス トークンで発行される `groups` 要求を構成するビットマスク。 ビットマスクの値は次のとおりです。<br>0: なし<br>1: セキュリティ グループと Azure AD ロール<br>2: 予約済み<br>4: 予約済み<br>ビットマスクを 7 に設定すると、サインイン ユーザーがメンバーになっているすべてのセキュリティ グループ、配布グループ、および Azure AD ディレクトリ ロールが取得されます。 |
| `optionalClaims` | 文字列 | `null` | この特定のアプリのセキュリティ トークン サービスによってトークンで返される省略可能な要求。 詳細については、[省略可能な要求](active-directory-optional-claims.md)に関するページを参照してください。 |
| `acceptMappedClaims` | ブール値 | `true` | この値が `true` に設定されている場合、アプリは、カスタム署名キーを指定せずに要求のマッピングを使用できます。 |
| `homepage` | 文字列 | `http://MyRegisteredApp` | アプリのホーム ページへの URL を指定します。 |
| `informationalUrls` | 文字列 | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | アプリのサービス利用規約とプライバシーに関する声明へのリンクを指定します。 サービス利用規約とプライバシーに関する声明は、ユーザーの同意エクスペリエンスからユーザーに提示されます。 詳細については、[登録済み Azure AD アプリのサービス利用規約とプライバシーに関する声明を追加する方法](howto-add-terms-of-service-privacy-statement.md)のページを参照してください。 |
| `identifierUris` | 文字列配列 | `http://MyRegistererdApp` | その Azure AD テナント内で、またはアプリがマルチテナントである場合は検証されたカスタム ドメイン内で Web アプリを一意に識別するユーザー定義 URI。 |
| `keyCredentials` | 配列の型 | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | アプリで割り当てられた資格情報、文字列ベースの共有シークレット、および X.509 証明書への参照を保持します。 これらの資格情報は、アクセス トークンを要求するときに使用されます (そのアプリがリソースとしてではなく、クライアントとして機能している場合)。 |
| `knownClientApplications` | 配列の型 | `[GUID]` | クライアント アプリとカスタム Web API アプリの 2 つの部分を含むソリューションがある場合に、同意をバンドルするために使用されます。 この値にクライアント アプリの appID を入力すると、ユーザーは、クライアント アプリに 1 回同意するだけで済みます。 Azure AD は、クライアントへの同意が Web API への暗黙的な同意を示し、クライアントと Web API の両方のサービス プリンシパルを同時に自動的にプロビジョニングすることを認識します。 クライアントと Web API アプリの両方が同じテナントに登録されている必要があります。 |
| `logoutUrl` | 文字列 | `http://MyRegisteredAppLogout` | アプリからログアウトするための URL。 |
| `oauth2AllowImplicitFlow` | ブール値 | `false` | この Web アプリが OAuth2.0 暗黙的フロー トークンを要求できるかどうかを指定します。 既定値は false です。 このフラグは、ブラウザー ベースのアプリ (Javascript シングル ページ アプリなど) に使用されます。 |
| `oauth2AllowUrlPathMatching` | ブール値 | `false` | OAuth 2.0 トークン要求の一部として、Azure AD がアプリの replyUrls に対するリダイレクト URI のパスの照合を許可するかどうかを指定します。 既定値は false です。 |
| `oauth2Permissions` | 配列の型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | Web API (リソース) アプリがクライアント アプリに公開する OAuth 2.0 アクセス許可スコープのコレクションを指定します。 これらのアクセス許可スコープは、同意中にクライアント アプリに付与できます。 |
| `oauth2RequiredPostResponse` | ブール値 | `false` | OAuth 2.0 トークン要求の一部として、Azure AD が GET 要求ではなく、POST 要求を許可するかどうかを指定します。 既定値は false です。これは、GET 要求のみが許可されることを指定します。 |
| `objectId` | 識別子の文字列 | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | ディレクトリ内のアプリの一意識別子。 この ID は、いずれかのプロトコル トランザクション内のアプリを識別するために使用される識別子ではありません。 これは、ディレクトリ クエリ内のオブジェクトを参照するために使用されます。 |
| `parentalControlSettings` | 文字列 | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` は、未成年者に関してアプリがブロックされる国を指定します。<br>`legalAgeGroupRule` は、アプリのユーザーに適用される法的年齢グループ ルールを指定します。 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids`、`BlockMinors` のいずれかに設定できます。  |
| `passwordCredentials` | 配列の型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | `keyCredentials` プロパティの説明を参照してください。 |
| `publicClient` | ブール値 | `false` | アプリがパブリック クライアント (モバイル デバイス上で実行されているインストール済みのアプリなど) であるかどうかを指定します。 既定値は false です。 |
| `replyUrls` | 文字列配列 | `"http://localhost"` | この複数値プロパティは、Azure AD がトークンを返すときに宛先として受け入れる登録された redirect_uri 値の一覧を保持します。 |
| `requiredResourceAccess` | 配列の型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | 動的な同意では、静的な同意を使用しているユーザーに対する管理者の同意エクスペリエンスおよびユーザーの同意エクスペリエンスが `requiredResourceAccess` によって作動します。 ただし、一般的なケースでは、これによってユーザーの同意エクスペリエンスが作動することはありません。<br>`resourceAppId` は、アプリがアクセスする必要があるリソースの一意識別子です。 この値は、ターゲット リソース アプリで宣言された appId に等しくなるようにしてください。<br>`resourceAccess` は、指定されたリソースに対してアプリが必要とする OAuth2.0 アクセス許可スコープとアプリ ロールを格納する配列です。 指定されたリソースの `id` と `type` の値が格納されます。 |
| `samlMetadataUrl` | 文字列 | `http://MyRegisteredAppSAMLMetadata` | アプリの SAML メタデータへの URL。 |

## <a name="next-steps"></a>次の手順

* アプリのアプリ オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、「[Azure AD のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)」を参照してください。
* Azure Active Directory (AD) の開発者向けの重要な概念の定義については、[Azure AD の 開発者向け用語集](developer-glossary.md)のページを参照してください。

次のコメント セクションを使用して、Microsoft のコンテンツの改善に役立つフィードバックを提供してください。

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
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

