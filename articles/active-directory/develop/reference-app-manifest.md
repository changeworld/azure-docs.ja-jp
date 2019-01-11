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
ms.date: 12/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: a971806b453d34aa8459cb30090024bfca96d342
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631190"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory のアプリ マニフェスト

アプリケーション マニフェストには、Microsoft ID プラットフォームにあるアプリケーション オブジェクトのすべての属性の定義が含まれます。 それは、アプリケーション オブジェクトを更新するメカニズムとしても機能します。 アプリケーション エンティティとそのスキーマの詳細については、[Graph API のアプリケーション エンティティに関するドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)を参照してください。

Azure portal で、または Microsoft Graph を使用してプログラムで、アプリの属性を構成できます。 ただし、一部のシナリオでは、アプリ マニフェストを編集してアプリの属性を構成する必要があります。 これらのシナリオは、次のとおりです。

* アプリを Azure AD マルチテナントと個人用の Microsoft アカウントとして登録した場合、サポートされる Microsoft アカウントを UI で変更することはできません。 代わりに、アプリケーション マニフェスト エディターを使用して、サポートされるアカウントの種類を変更する必要があります。
* アプリでサポートされるアクセス許可とロールを定義する必要がある場合は、アプリケーション マニフェストを変更する必要があります。

## <a name="configure-the-app-manifest"></a>アプリ マニフェストを構成する

アプリケーション マニフェストを構成するには:

1. [Azure portal](https://portal.azure.com) にログインします。
1. **[Azure Active Directory]** サービスを選択し、**[アプリの登録]** または **[アプリの登録 (プレビュー)]** を選択します。
1. 構成するアプリを選択します。
1. アプリの **[概要]** ページで、**[マニフェスト]** セクションを選択します。 Web ベースのマニフェスト エディターが開き、ポータルでマニフェストを編集できます。 必要があれば、**[ダウンロード]** を選択してローカルでマニフェストを編集します。その後、**[アップロード]** を使用して、アプリケーションにマニフェストを再適用します。

## <a name="manifest-reference"></a>マニフェスト リファレンス

> [!NOTE]
> **[説明]** の後に **[値の例]** 列が表示されない場合は、ご利用のブラウザー ウィンドウを最大化し、**[値の例]** 列が表示されるまでスクロール/スワイプします。

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| キー  | 値の型 | 説明  | 値の例 |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Null 許容の Int32 | 現在の API リソース用の承認済みアクセス トークンのバージョンを指定します。 指定できる値は 1、2、null です。 既定では null に設定され、2 として扱われます。 | `2` |
| `allowPublicClient` | ブール値 | フォールバック アプリケーションの種類を指定します。 Azure AD では、既定で replyUrlsWithType からアプリケーションの種類が推測されます。 Azure AD でクライアント アプリの種類を判別できない特定のシナリオがあります (URL リダイレクトなしで HTTP 要求が行われる [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) フローなど)。 そのような場合、Azure AD では、このプロパティの値に基づいて、アプリケーションの種類が解釈されます。 この値が true に設定されている場合、フォールバック アプリケーションの種類は、モバイル デバイス上で実行されているインストール済みのアプリなど、パブリック クライアントとして設定されます。 既定値は false です。これは、フォールバック アプリケーションの種類が、Web アプリなどの機密クライアントであることを意味します。 | `false` |
| `appId` | 識別子の文字列 | Azure AD によってアプリに割り当てられた一意識別子を指定します。 | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | 配列の型 | アプリが宣言する可能性のあるロールのコレクションを指定します。 これらのロールは、ユーザー、グループ、またはサービス プリンシパルに割り当てることができます。 その他の例および詳細については、「[アプリケーションにアプリ ロールを追加してトークンで受け取る](howto-add-app-roles-in-azure-ad-apps.md)」を参照してください。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `groupMembershipClaims` | string | アプリが期待する、ユーザーまたは OAuth 2.0 アクセス トークンで発行される `groups` 要求を構成します。 この属性を設定するには、次のいずれかの有効な文字列値を使用します。<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (セキュリティ グループおよび Azure AD ロールの場合)<br/>- `"All"` (これは、サインイン ユーザーがメンバーになっているすべてのセキュリティ グループ、配布グループ、および Azure AD ディレクトリ ロールが取得されます)。 | `"SecurityGroup"` |
| `optionalClaims` | string | この特定のアプリのセキュリティ トークン サービスによってトークンで返される省略可能な要求。<br>現時点では、個人アカウントと Azure AD (アプリ登録ポータルを使用して登録済み) の両方をサポートするアプリは、省略可能な要求を使用できません。 ただし、v2.0 エンドポイントを使用して Azure AD のみに登録されたアプリは、マニフェストで要求した省略可能な要求を取得することができます。 詳細については、[省略可能な要求](active-directory-optional-claims.md)に関するページを参照してください。 | `null` |
| `id` | 識別子の文字列 | ディレクトリ内のアプリの一意識別子。 この ID は、いずれかのプロトコル トランザクション内のアプリを識別するために使用される識別子ではありません。 これは、ディレクトリ クエリ内のオブジェクトを参照するために使用されます。 | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | 文字列配列 | その Azure AD テナント内で、またはアプリがマルチテナントである場合は検証されたカスタム ドメイン内で Web アプリを一意に識別するユーザー定義 URI。 | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | string | アプリのサービス利用規約とプライバシーに関する声明へのリンクを指定します。 サービス利用規約とプライバシーに関する声明は、ユーザーの同意エクスペリエンスからユーザーに提示されます。 詳細については、[登録済み Azure AD アプリのサービス利用規約とプライバシーに関する声明を追加する方法](howto-add-terms-of-service-privacy-statement.md)に関するページを参照してください。 | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | 配列の型 | アプリで割り当てられた資格情報、文字列ベースの共有シークレット、および X.509 証明書への参照を保持します。 これらの資格情報は、アクセス トークンを要求するときに使用されます (そのアプリがリソースとしてではなく、クライアントとして機能している場合)。 | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | 配列の型 | クライアント アプリとカスタム Web API アプリの 2 つの部分を含むソリューションがある場合に、同意をバンドルするために使用されます。 この値にクライアント アプリの appID を入力すると、ユーザーは、クライアント アプリに 1 回同意するだけで済みます。 Azure AD は、クライアントへの同意が Web API への暗黙的な同意を示し、クライアントと Web API の両方のサービス プリンシパルを同時に自動的にプロビジョニングすることを認識します。 クライアントと Web API アプリの両方が同じテナントに登録されている必要があります。 | `[GUID]` |
| `logoUrl` | string | ポータルでアップロードされたロゴへの CDN URL を指す値のみを読み取ります。 | `https://MyRegisteredAppLogo` |
| `logoutUrl` | string | アプリからログアウトするための URL。 | `https://MyRegisteredAppLogout` |
| `name` | string | アプリの表示名。 | `MyRegisteredApp` |
| `oauth2AllowImplicitFlow` | ブール値 | この Web アプリで OAuth2.0 暗黙的フロー アクセス トークンを要求できるかどうかを指定します。 既定値は false です。 このフラグは、ブラウザー ベースのアプリ (Javascript シングル ページ アプリなど) に使用されます。 詳細については、目次に「`OAuth 2.0 implicit grant flow`」と入力し、暗黙的フローに関するトピックを表示します。 | `false` |
| `oauth2AllowIdTokenImplicitFlow` | ブール値 | この Web アプリで OAuth2.0 暗黙的フロー ID トークンを要求できるかどうかを指定します。 既定値は false です。 このフラグは、ブラウザー ベースのアプリ (Javascript シングル ページ アプリなど) に使用されます。 | `false` |
| `oauth2Permissions` | 配列の型 | Web API (リソース) アプリがクライアント アプリに公開する OAuth 2.0 アクセス許可スコープのコレクションを指定します。 これらのアクセス許可スコープは、同意中にクライアント アプリに付与できます。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"u| Web API (リソース) アプリがクライアント アプリに公開する OAuth 2.0 アクセス許可スコープのコレクションを指定します。 これらのアクセス許可スコープは、同意中にクライアント アプリに付与できます。 ser_impersonation"<br>&nbsp;&nbsp;}<br>]</code> |
| `oauth2RequiredPostResponse` | ブール値 | OAuth 2.0 トークン要求の一部として、Azure AD が GET 要求ではなく、POST 要求を許可するかどうかを指定します。 既定値は false です。これは、GET 要求のみが許可されることを指定します。 | `false` |
| `parentalControlSettings` | string | `countriesBlockedForMinors` は、未成年者に関してアプリがブロックされる国を指定します。<br>`legalAgeGroupRule` は、アプリのユーザーに適用される法的年齢グループ ルールを指定します。 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids`、`BlockMinors` のいずれかに設定できます。  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | 配列の型 | `keyCredentials` プロパティの説明を参照してください。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | 配列の型 | 暗黙的に同意するアプリケーションと要求されたアクセス許可がリストされます。 管理者がアプリケーションに同意する必要があります。 preAuthorizedApplications では、ユーザーが要求されたアクセス許可に同意する必要はありません。 PreAuthorizedApplications でリストされるアクセス許可にはユーザーの同意は必要ありません。 しかし、preAuthorizedApplications にリストされていない追加の要求されたアクセス許可にはユーザーの同意が必要です。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `replyUrlsWithType` | 文字列配列 | この複数値プロパティは、Azure AD がトークンを返すときに宛先として受け入れる登録された redirect_uri 値の一覧を保持します。 各 uri 値には、関連付けられているアプリの種類の値を含める必要があります。 サポートされる種類の値は `Web`、`InstalledClient` です。 | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | 配列の型 | 動的な同意では、静的な同意を使用しているユーザーに対する管理者の同意エクスペリエンスおよびユーザーの同意エクスペリエンスが `requiredResourceAccess` によって作動します。 ただし、一般的なケースでは、これによってユーザーの同意エクスペリエンスが作動することはありません。<br>`resourceAppId` は、アプリがアクセスする必要があるリソースの一意識別子です。 この値は、ターゲット リソース アプリで宣言された appId に等しくなるようにしてください。<br>`resourceAccess` は、指定されたリソースに対してアプリが必要とする OAuth2.0 アクセス許可スコープとアプリ ロールを格納する配列です。 指定されたリソースの `id` と `type` の値が格納されます。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | string | アプリの SAML メタデータへの URL。 | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | string | アプリのホーム ページへの URL を指定します。 | `https://MyRegisteredApp` |
| `signInAudience` | string | 現在のアプリケーションでサポートされる Microsoft アカウントを指定します。 サポートされる値は次のとおりです。<ul><li>**AzureADMyOrg** - 自分の組織の Azure AD テナント (つまり、シングル テナント) における Microsoft の職場または学校アカウントを持つユーザー</li><li>**AzureADMultipleOrgs** - 任意の組織の Azure AD テナント (つまり、マルチテナント) での Microsoft の職場または学校アカウントを持つユーザー</li> <li>**AzureADandPersonalMicrosoftAccount** - 個人の Microsoft アカウント、または任意の組織の Azure AD テナントでの職場または学校アカウントを持つユーザー</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | 文字列配列 | アプリケーションの分類と特定に使用できるカスタム文字列。 | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

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
