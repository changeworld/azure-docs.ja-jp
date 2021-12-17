---
title: Azure AD パートナー統合を使用した安全なハイブリッド アクセス
description: 顧客が SaaS アプリケーションを検出して Azure AD に移行し、従来の認証方法を使用するアプリを Azure AD に接続できるよう支援します。
titleSuffix: Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c8bffabb1cb2e12fb8144e66e038f500a8e5229
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253863"
---
# <a name="secure-hybrid-access-with-azure-active-directory-partner-integrations"></a>Azure Active Directory パートナー統合を使用した安全なハイブリッド アクセス

Azure Active Directory (Azure AD) では、緊密に接続されたクラウドベースの世界でアプリケーションのセキュリティを維持するのに役立つ最新の認証プロトコルがサポートされています。 とはいえ、多くのビジネス アプリケーションは保護された企業ネットワークの内部で動作するように作成されており、そのようなアプリケーションの中には従来の認証方法が使用されているものもあります。 多くの企業はゼロ トラスト戦略の構築と、ハイブリッドおよびクラウドファーストの仕事環境のサポートを検討しているため、アプリを Azure AD に接続して、レガシ アプリケーションに最新の認証ソリューションを提供するソリューションを必要としています。

Azure AD では、SAML、WS-Fed、OIDC などの最新のプロトコルがネイティブにサポートされています。 Azure AD のアプリ プロキシでは、Kerberos とヘッダーベースの認証がサポートされています。 SSH、NTLM、LDAP、Cookie などの他のプロトコルはまだサポートされていません。 しかし、ISV はこれらのアプリケーションを Azure AD に接続するためのソリューションを作成して、顧客のゼロ トラストへの移行を支援できます。

ISV には、顧客がサービスとしてのソフトウェア (SaaS) アプリケーションを検出して Azure AD に移行できるように支援する機会があります。 さらに、従来の認証方法を使用するアプリを Azure AD に接続することもできます。 これにより、単一のプラットフォーム (Azure AD) に統合することでアプリの管理を簡素化し、ゼロ トラストの原則を導入できるように顧客を支援します。 従来の認証を使用するアプリをサポートすることにより、ユーザーのセキュリティが強化されます。 このソリューションは、顧客がアプリを最新化して最新の認証プロトコルをサポートするようになるまでの間の優れた一時的解決策になり得ます。

## <a name="solution-overview"></a>ソリューションの概要

構築するソリューションには次のものを含めることができます。

1. **アプリの検出**。 顧客は使用しているアプリケーションを全部把握しているとは限りません。 このため、最初のステップとして、アプリケーションの検出機能をソリューションに組み込み、検出したアプリケーションをユーザー インターフェイスに表示することができます。 これにより、顧客はアプリケーションと Azure AD との統合についての優先順位を判断できるようになります。
2. **アプリの移行**。 次に、顧客が Azure AD ポータルに移動しなくてもアプリを直接 Azure AD に統合できるようにする、製品内ワークフローを作成できます。 ソリューションに検出機能を実装しない場合は、ソリューションをここから開始し、顧客が把握しているアプリケーションを Azure AD に統合できます。
3. **従来の認証のサポート**。 従来の認証方法を使用してアプリを Azure AD に接続することで、シングル サインオン (SSO) やその他の機能の利点を活用できます。
4. **条件付きアクセス**。 追加の機能として、顧客が Azure AD ポータルに移動しなくても、ソリューション内から、Azure AD の[条件付きアクセス](../conditional-access/overview.md) ポリシーをアプリケーションに適用できるようにすることもできます。

このガイドのこれ以降の部分では、ソリューションを実装するための技術的な検討事項と、推奨事項について説明します。

## <a name="publishing-your-application-to-azure-marketplace"></a>Azure Marketplace にアプリケーションを公開する

[Azure Marketplace で公開する](../develop/v2-howto-app-gallery-listing.md)ためのプロセスに従って、アプリケーションを Azure AD と事前に統合し、SSO や自動プロビジョニングをサポートすることができます。 Azure Marketplace は、IT 管理者にとって信頼できるアプリケーション ソースです。 そこにリストされているアプリケーションは、Azure AD と互換性があることが検証されています。 SSO がサポートされており、ユーザー プロビジョニングを自動化でき、自動アプリ登録により顧客テナントへの統合が容易です。

また、[確認済みの発行元](../develop/publisher-verification-overview.md)になり、顧客からアプリの信頼された発行元であると認識してもらえるようにすることをお勧めします。

## <a name="enabling-single-sign-on-for-it-admins"></a>IT 管理者のシングル サインオンを有効にする

[OIDC か SAML のどちらかを選択](/azure/active-directory/manage-apps/sso-options#choosing-a-single-sign-on-method/)し、ソリューションで IT 管理者が SSO を利用できるようにします。 最適なのは、OIDC を使用することです。 

Microsoft Graph は [OIDC/OAuth](../develop/v2-protocols-oidc.md) を使用しています。 ソリューションで IT 管理者の SSO のために OIDC と Azure AD を使用すると、顧客にとってシームレスなエンドツーエンドのエクスペリエンスになります。 OIDC を使用してソリューションにサインインし、Azure AD が発行したのと同じ JSON Web Token (JWT) を使用して Microsoft Graph とやり取りできます。

一方、IT 管理者の SSO に [SAML](/azure/active-directory/manage-apps/configure-saml-single-sign-on/) を使用するソリューションの場合、SAML トークンでソリューションと Microsoft Graph とのやり取りはできません。 IT 管理者の SSO には SAML を使用できますが、ソリューションと Microsoft Graph との適正なやり取りのためには、Azure AD との OIDC 統合をサポートして、Azure AD から JWT を取得できるようにする必要があります。 次のいずれかの手順を利用できます。

- **推奨される SAML アプローチ**: Azure Marketplace で新しい登録を作成します。これは [OIDC アプリ](../saas-apps/openidoauth-tutorial.md)です。 これは顧客にとって最もシームレスなエクスペリエンスです。 それぞれが自分のテナントに SAML と OIDC の両方のアプリを追加します。 アプリケーションが現時点で Azure AD ギャラリーにない場合、ギャラリーに含まれない[マルチテナント アプリケーション](../develop/howto-convert-app-to-be-multi-tenant.md)で開始できます。

- **代替の SAML アプローチ**: 顧客が自分の Azure AD テナントに手動で [OIDC のアプリケーションの登録を作成](../saas-apps/openidoauth-tutorial.md)します。この記事の後のセクションに示す正しい URI、エンドポイント、アクセス許可が設定されていることを確認します。

[client_credentials 付与タイプ](../develop/v2-oauth2-client-creds-grant-flow.md#get-a-token)を使用することが望ましく思える場合もあります。 この場合、ソリューションのユーザー インターフェイスに各顧客がクライアント ID とシークレットを入力できるようにすることと、この情報を保存することが必要です。 Azure AD から JWT を取得し、それを使用して Microsoft Graph とやり取りします。

この方法を選択する場合は、顧客の Azure AD テナント内でこのアプリケーションの登録を作成する方法に関する顧客向けのドキュメントを用意しておく必要があります。 この情報には、エンドポイント、URI、必要なアクセス許可が含まれます。

> [!NOTE]
> アプリケーションを IT 管理者またはユーザーのどちらかの SSO に使用できるようにするには、事前に顧客の IT 管理者が[テナント内でアプリケーションに同意する](./grant-admin-consent.md)必要があります。

## <a name="authentication-flows"></a>認証フロー

ソリューションには、次のシナリオをサポートする 3 つの主要な認証フローが含まれます。

- 顧客の IT 管理者が、ソリューションを管理するために SSO を使用してサインインする。

- 顧客の IT 管理者が、ソリューションを使用して Microsoft Graph 経由でアプリケーションを Azure AD と統合する。

- ユーザーが、ソリューションと Azure AD で保護されたレガシ アプリケーションにサインインする。

### <a name="your-customers-it-administrator-does-single-sign-on-to-your-solution"></a>顧客の IT 管理者がソリューションにシングル サインオンする

顧客の IT 管理者がサインインする際に、ソリューションで SAML か OIDC のどちらかを SSO に使用できます。 どちらの方法でも、IT 管理者が Azure AD 資格情報を使用してソリューションにサインインできるようにすることをお勧めします。 そうすればシームレスなエクスペリエンスになり、既に機能している既存のセキュリティ コントロールが使えます。 SAML または OIDC のいずれかを使用した SSO のために、ソリューションを Azure AD と統合する必要があります。

このユーザー認証フローの図と概要は以下のとおりです。

![IT 管理者が、サインインするためにソリューションによって Azure AD にリダイレクトされ、その後に Azure AD によってソリューションに再びリダイレクトされる様子を示すユーザー認証フローの図。](./media/secure-hybrid-access-integrations/admin-flow.png)

1. IT 管理者が自分の Azure AD 資格情報を使用してソリューションにサインインしようとします。

2. SAML または OIDC サインイン要求と共に、IT 管理者はソリューションから Azure AD にリダイレクトされます。

3. Azure AD によって認証されると、IT 管理者はソリューション内で認可するために SAML トークンまたは JWT のどちらかと共にソリューションに送り返されます。

### <a name="the-it-administrator-integrates-applications-with-azure-ad-by-using-your-solution"></a>IT 管理者がソリューションを使用してアプリケーションを Azure AD と統合する

IT 管理者が行うべき 2 つ目のステップは、ソリューションを使用してアプリケーションを Azure AD と統合することです。 そのために、ソリューションで Microsoft Graph を使用してアプリケーションの登録と Azure AD 条件付きアクセス ポリシーを作成します。

このユーザー認証フローの図と概要は以下のとおりです。

![IT 管理者、Azure Active Directory、ソリューション、Microsoft Graph の間のリダイレクトおよびその他のやり取りを示すユーザー認証フローの図。](./media/secure-hybrid-access-integrations/registration-flow.png)


1. IT 管理者が自分の Azure AD 資格情報を使用してソリューションにサインインしようとします。

2. SAML または OIDC サインイン要求と共に、IT 管理者はソリューションから Azure AD にリダイレクトされます。

3. Azure AD によって認証されると、IT 管理者はソリューション内で認可を得るための SAML トークンまたは JWT のどちらかと共にソリューションに送り返されます。

4. IT 管理者が自分のアプリケーションの 1 つを Azure AD と統合することを望む場合は、Azure AD ポータルに移動する代わりに既存の JWT を使用してソリューションから Microsoft Graph を呼び出してそれらのアプリケーションを登録するか、Azure AD 条件付きアクセス ポリシーをそれらに適用します。

### <a name="users-sign-in-to-the-applications-secured-by-your-solution-and-azure-ad"></a>ユーザーがソリューションと Azure AD で保護されたアプリケーションにサインインする

ユーザーは、ソリューションと Azure AD で保護された個々のアプリケーションにサインインする必要がある場合に、OIDC または SAML を使用します。 Microsoft Graph または Azure AD で保護された API とやり取りする必要があるアプリケーションは、OICD を使用するように構成することをお勧めします。 この構成では、アプリケーションが Azure AD から受け取るアプリケーションでの認証用 JWT を、Microsoft Graph とのやり取りにも適用できます。 個々のアプリケーションで Microsoft Graph または Azure AD で保護された API とやり取りする必要がない場合は、SAML で十分です。

このユーザー認証フローの図と概要は以下のとおりです。

![ユーザー、Azure Active Directory、ソリューション、アプリケーションの間のリダイレクトおよびその他のやり取りを示すユーザー認証フローの図。](./media/secure-hybrid-access-integrations/end-user-flow.png)

1. ユーザーが、ソリューションと Azure AD で保護されたアプリケーションにサインインしようとします。
2. SAML または OIDC サインイン要求と共に、ユーザーはソリューションから Azure AD にリダイレクトされます。
3. Azure AD によって認証されると、ユーザーはソリューション内で認可を得るための SAML トークンまたは JWT のどちらかと共にソリューションに送り返されます。
4. 認可を得た後、アプリケーションの優先プロトコルを使用してアプリケーションへの元の要求を通過させることがソリューションによって許可されます。

## <a name="summary-of-microsoft-graph-apis"></a>Microsoft Graph API の概要

ソリューションでは、次の API を使用する必要があります。 Azure AD では、委任されたアクセス許可またはアプリケーションのアクセス許可のいずれかを構成することができます。 このソリューションに必要なのは、委任されたアクセス許可のみです。

- [アプリケーション テンプレート API](/graph/application-saml-sso-configure-api#retrieve-the-gallery-application-template-identifier/): Azure Marketplace を検索したい場合、この API を使用して一致するアプリケーション テンプレートを見つけることができます。 **必要なアクセス許可**: Application.Read.All。

- [アプリケーションの登録 API](/graph/api/application-post-applications): ユーザーが、顧客がソリューションを使用してセキュリティで保護したアプリケーションにサインインできるようにするために、この API を使用して OIDC または SAML のアプリケーションの登録を作成します。 こうすることにより、これらのアプリケーションは Azure AD によっても保護されるようになります。 **必要なアクセス許可**: Application.Read.All、Application.ReadWrite.All。

- [サービス プリンシパル API](/graph/api/serviceprincipal-update): アプリを登録した後で、サービス プリンシパル オブジェクトを更新して、いくつかの SSO プロパティを設定する必要があります。 **必要なアクセス許可**: Application.ReadWrite.All、Directory.AccessAsUser.All、AppRoleAssignment.ReadWrite.All (割り当て用)。

- [条件付きアクセス API](/graph/api/resources/conditionalaccesspolicy): Azure AD 条件付きアクセス ポリシーをこれらのユーザー アプリケーションにも適用したい場合、この API を使用できます。 **必要なアクセス許可**: Policy.Read.All、Policy.ReadWrite.ConditionalAccess、Application.Read.All。

## <a name="example-graph-api-scenarios"></a>Graph API シナリオの例

このセクションでは、Microsoft Graph API を使用して、ソリューション経由でアプリケーションの登録を行い、レガシ アプリケーションを接続し、条件付きアクセス ポリシーを有効にするための参考となる例を説明します。 また、管理者の同意の自動化、トークン署名証明書の取得、ユーザーとグループの割り当てについてのガイダンスも提供します。 こうした機能はソリューションの役に立つ可能性があります。

### <a name="use-the-graph-api-to-register-apps-with-azure-ad"></a>Graph API を使用してアプリを Azure AD に登録する

#### <a name="add-apps-that-are-in-azure-marketplace"></a>Azure Marketplace にあるアプリを追加する

顧客が使用しているアプリケーションの中には、既に [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps) で提供されているものがあります。 プログラムを使用してこれらのアプリケーションを顧客のテナントに追加するソリューションを作成できます。 次のコードは、Microsoft Graph API を使用して、一致するテンプレートを Azure Marketplace から検索し、そのアプリケーションを顧客の Azure AD テナントに登録する例です。

一致するアプリケーションを Azure Marketplace から検索します。 アプリケーション テンプレート API を使用する場合、表示名の大文字と小文字は区別されます。

```http
Authorization: Required with a valid Bearer token
Method: Get

https://graph.microsoft.com/v1.0/applicationTemplates?$filter=displayname eq "Salesforce.com"
```

前述の API 呼び出しで一致するものが見つかったら、その ID をキャプチャしておき、次の API 呼び出しを実行します。その際、JSON 本文でそのアプリケーションのわかりやすい表示名を指定します。

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/cd3ed3de-93ee-400b-8b19-b61ef44a0f29/instantiate
{
    "displayname": "Salesforce.com"
}
```

前述の API 呼び出しを行う際に、サービス プリンシパル オブジェクトも生成されます。これには数秒かかることがあります。 アプリケーション ID とサービス プリンシパル ID を忘れずにキャプチャしておきます。 それらを次の API 呼び出しで使用します。

次に、SAML プロトコルと適切なログイン URL を指定して、サービス プリンシパル オブジェクトに対して PATCH を実行します。

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.salesforce.com"
}
```

最後に、適切なリダイレクト URI と識別子 URI を指定して、アプリケーション オブジェクトに対して PATCH を実行します。

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.salesforce.com"]},
    "identifierUris":["https://www.salesforce.com"]
}
```

#### <a name="add-apps-that-are-not-in-azure-marketplace"></a>Azure Marketplace にないアプリを追加する

Azure Marketplace には一致するものが見つからない場合や、カスタム アプリケーションを統合する必要がある場合は、次のテンプレート ID を使用することにより、カスタム アプリケーションを Azure AD に登録できます: **8adf8e6e-67b2-4cf2-a259-e3dc5476c621**。 その後、JSON 本文にアプリケーションのわかりやすい表示名を指定して、次の API 呼び出しを実行します。

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

前述の API 呼び出しを行う際に、サービス プリンシパル オブジェクトも生成されます。これには数秒かかることがあります。 アプリケーション ID とサービス プリンシパル ID を忘れずにキャプチャしておきます。 それらを次の API 呼び出しで使用します。

次に、SAML プロトコルと適切なログイン URL を指定して、サービス プリンシパル オブジェクトに対して PATCH を実行します。

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

最後に、適切なリダイレクト URI と識別子 URI を指定して、アプリケーション オブジェクトに対して PATCH を実行します。

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="cut-over-to-azure-ad-single-sign-on"></a>Azure AD シングル サインオンへの切り替え

SaaS アプリケーションを Azure AD 内で登録したら、Azure AD を ID プロバイダーとして使用し始めるために、アプリケーションで切り替えを行う必要があります。 これには、2 つの方法があります。

- アプリケーションで One Click SSO がサポートされている場合、顧客に代わって Azure AD でアプリケーションの切り替えを行うことができます。 顧客は、Azure AD ポータルに移動して、サポートされている SaaS アプリケーションに対して管理者の資格情報を使用して One Click SSO を実行するだけで済みます。 詳細については、「[アプリの One Click シングル サインオンの構成](./one-click-sso-tutorial.md)」を参照してください。
- アプリケーションで One Click SSO がサポートされていない場合は、Azure AD を使用し始めるために、顧客が手動でアプリケーションを切り替える必要があります。 詳細については、「[SaaS アプリケーションと Azure Active Directory の統合に関するチュートリアル](../saas-apps/tutorial-list.md)」を参照してください。

### <a name="connect-apps-by-using-legacy-authentication-methods-to-azure-ad"></a>従来の認証方法を使用してアプリを Azure AD に接続する

Azure AD とアプリケーションの間にソリューションを配置して、SSO やその他の Azure Active Directory の機能の利点を、サポート対象ではないアプリケーションでも顧客が利用できるようにすることができます。 そうするには、アプリケーションから Azure AD を呼び出し、ユーザーを認証して Azure AD 条件付きアクセス ポリシーを適用します。その後、ユーザーは従来のプロトコルを使用してこれらのアプリケーションにアクセスできるようになります。

この統合を顧客がコンソールから直接行えるようにすれば、検出と統合はエンドツーエンドのシームレスなエクスペリエンスになります。 これには、プラットフォームと Azure AD の間の SAML または OIDC のアプリケーションの登録をプラットフォームで作成することが含まれます。

#### <a name="create-a-saml-application-registration"></a>SAML のアプリケーションの登録を作成する

SAML のアプリケーションの登録を作成するには、カスタム アプリケーションのカスタム アプリケーション テンプレート ID **8adf8e6e-67b2-4cf2-a259-e3dc5476c621** を使用します。 次に、JSON 本文にわかりやすい表示名を指定して次の API 呼び出しを行います。

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

前述の API 呼び出しを行う際に、サービス プリンシパル オブジェクトも生成されます。これには数秒かかることがあります。 アプリケーション ID とサービス プリンシパル ID を忘れずにキャプチャしておきます。 それらを次の API 呼び出しで使用します。

次に、SAML プロトコルと適切なログイン URL を指定して、サービス プリンシパル オブジェクトに対して PATCH を実行します。

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

最後に、適切なリダイレクト URI と識別子 URI を指定して、アプリケーション オブジェクトに対して PATCH を実行します。

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="create-an-oidc-application-registration"></a>OIDC のアプリケーションの登録を作成する

OIDC のアプリケーションの登録を作成するには、カスタム アプリケーションのテンプレート ID **8adf8e6e-67b2-4cf2-a259-e3dc5476c621** を使用します。 次に、JSON 本文にわかりやすい表示名を指定して次の API 呼び出しを行います。

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom OIDC App"
}
```

API 呼び出しから、アプリケーション ID とサービス プリンシパル ID をキャプチャします。 それらを次の API 呼び出しで使用します。

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/{Application Object ID}
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["[https://www.samlapp.com"],
    "requiredResourceAccess": [
    {
        "resourceAppId": "00000003-0000-0000-c000-000000000000",
        "resourceAccess": [
        {
            "id": "7427e0e9-2fba-42fe-b0c0-848c9e6a8182",
            "type": "Scope"
        },
        {
            "id": "e1fe6dd8-ba31-4d61-89e7-88639da4683d",
            "type": "Scope"
        },
        {
            "id": "37f7f235-527c-4136-accd-4a02d197296e",
            "type": "Scope"
        }]
    }]
}
```

> [!NOTE]
> `resourceAccess` ノード内にリストされている API アクセス許可により、アプリケーションに *openid*、*User.Read*、*offline_access* のアクセス許可が付与されます。ユーザーがソリューションにサインインするには、これらで十分なはずです。 アクセス許可の詳細については、「[Microsoft Graph のアクセス許可のリファレンス](/graph/permissions-reference/)」を参照してください。

### <a name="apply-conditional-access-policies"></a>条件付きアクセス ポリシーを適用する

顧客とパートナーは、Microsoft Graph API を活用して条件付きアクセス ポリシーを作成し、それらを顧客のアプリケーションに適用することもできます。 顧客は Azure AD ポータルに移動せずにソリューションから直接これらのポリシーを適用できるので、これはパートナーに付加価値を提供するものとなり得ます。 

Azure AD 条件付きアクセス ポリシーを適用するには、次の 2 つの方法があります。

- アプリケーションを既存の条件付きアクセス ポリシーに割り当てる。
- 新しい条件付きアクセス ポリシーを作成して、アプリケーションをその新しいポリシーに割り当てる。

#### <a name="use-an-existing-conditional-access-policy"></a>既存の条件付きアクセス ポリシーを使用する

まず、次のクエリを実行して、すべての条件付きアクセス ポリシーのリストを取得します。 変更するポリシーのオブジェクト ID を取得します。

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies
```

次に、JSON 本文内の `includeApplications` のスコープにそのアプリケーション オブジェクト ID を含めることにより、そのポリシーに対して PATCH を実行します。

```https
Authorization: Required with a valid Bearer token
Method: PATCH

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/{policyid}
{
    "displayName":"Existing CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": 
        {
            "includeApplications":[
                "00000003-0000-0ff1-ce00-000000000000", 
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": 
    {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

#### <a name="create-a-new-conditional-access-policy"></a>新しい条件付きアクセス ポリシーを作成する

JSON 本文内の `includeApplications` のスコープにそのアプリケーション オブジェクト ID を追加します。

```https
Authorization: Required with a valid Bearer token
Method: POST

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/
{
    "displayName":"New CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

新しい Azure AD 条件付きアクセス ポリシーを作成したい場合は、次の追加のテンプレートを使用して、[条件付きアクセス API](../conditional-access/howto-conditional-access-apis.md) の使用を開始することができます。

```https
#Policy Template for Requiring Compliant Device

{
    "displayName":"Enforce Compliant Device",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "compliantDevice",
            "domainJoinedDevice"
        ]
    }
}

#Policy Template for Block

{
    "displayName":"Block",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "block"
        ]
    }
}
```

### <a name="automate-admin-consent"></a>管理者の同意を自動化する

顧客が多くのアプリケーションをプラットフォームから Azure AD にオンボードしようとしている場合、管理者の同意を自動化することにより、それらのアプリケーションに手動で同意しなくても済むようにできます。 この自動化は、Microsoft Graph を使用して行うこともできます。 それには、前述の API 呼び出しで作成したアプリケーションのサービス プリンシパル オブジェクト ID と、顧客のテナントからの Microsoft Graph のサービス プリンシパル オブジェクト ID の両方が必要になります。

次の API 呼び出しを行うことにより、Microsoft Graph のサービス プリンシパル オブジェクト ID を取得します。

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/serviceprincipals/?$filter=appid eq '00000003-0000-0000-c000-000000000000'&$select=id,appDisplayName
```

管理者の同意を自動化する準備ができたら、次の API 呼び出しを行います。

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/oauth2PermissionGrants
{
    "clientId":"{Service Principal Object ID of Application}",
    "consentType":"AllPrincipals",
    "principalId":null,
    "resourceId":"{Service Principal Object ID Of Microsoft Graph}",
    "scope":"openid user.read offline_access}"
}
```

### <a name="get-the-token-signing-certificate"></a>トークン署名証明書を取得する

これらのすべてのアプリケーションのトークン署名証明書の公開部分を取得するには、そのアプリケーションの Azure AD メタデータ エンドポイントから `GET` を使用します。

```https
Method:GET

https://login.microsoftonline.com/{Tenant_ID}/federationmetadata/2007-06/federationmetadata.xml?appid={Application_ID}
```

### <a name="assign-users-and-groups"></a>ユーザーとグループの割り当て

アプリケーションを Azure AD に公開したら、必要に応じてこれをユーザーとグループに割り当てることで、[MyApplications](/azure/active-directory/user-help/my-applications-portal-workspaces/) ポータルに確実に表示されるようにできます。 この割り当ては、アプリケーションを作成したときに生成された、サービス プリンシパル オブジェクトに保存されます。

まず、アプリケーションが既に関連付けられている可能性がある `AppRole` インスタンスを取得します。 通常、SaaS アプリケーションにはさまざまな `AppRole` インスタンスが関連付けられています。 カスタム アプリケーションの場合は、通常は 1 つの既定の `AppRole` インスタンスのみが存在します。 割り当てたい `AppRole` インスタンスの ID を取得します。

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
```

次に、アプリケーションに割り当てたいユーザーまたはグループのオブジェクト ID を Azure AD から取得します。 また、前述の API 呼び出しからアプリ ロール ID を取得して、サービス プリンシパルの PATCH の本文の一部として送信します。

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "principalId":"{Principal Object ID of User -or- Group}",
    "resourceId":"{Service Principal Object ID}",
    "appRoleId":"{App Role ID}"
}
```

## <a name="partnerships"></a>パートナーシップ

Microsoft では、既存のネットワーク コントローラーとデリバリー コントローラーを使用しつつレガシ アプリケーションを保護するため、次のアプリケーション デリバリー コントローラー (ADC) プロバイダーとパートナーシップを結んでいます。

| **ADC プロバイダー** | **リンク** |
| --- | --- |
| Akamai Enterprise Application Access | [https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial](../saas-apps/akamai-tutorial.md) |
| Citrix ADC | [https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial](../saas-apps/citrix-netscaler-tutorial.md) |
| F5 Big-IP Access Policy Manager | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration](./f5-aad-integration.md) |
| Kemp LoadMaster | [https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial](../saas-apps/kemp-tutorial.md) |
| Pulse Secure Virtual Traffic Manager | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md) |

次の VPN ソリューション プロバイダーでは、Azure AD と接続して、SSO や多要素認証などの最新の認証および認可方法を利用できるようにしています。

| **VPN ベンダー** | **リンク** |
| --- | --- |
| Cisco AnyConnect | [https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect](../saas-apps/cisco-anyconnect.md) |
| Fortinet FortiGate | [https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial](../saas-apps/fortigate-ssl-vpn-tutorial.md) |
| F5 Big-IP Access Policy Manager | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn](./f5-aad-password-less-vpn.md) |
| Palo Alto Networks GlobalProtect | [https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial](../saas-apps/paloaltoadmin-tutorial.md) |
| Pulse Connect Secure | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial](../saas-apps/pulse-secure-pcs-tutorial.md) |

次の SDP (Software Defined Perimeter) ソリューション プロバイダーでは、Azure AD と接続して、SSO や多要素認証などの最新の認証および認可方法を利用できるようにしています。

| **SDP ベンダー** | **リンク** |
| --- | --- |
| Datawiza Access Broker | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/datawiza-with-azure-ad](./datawiza-with-azure-ad.md) |
| Perimeter 81 | [https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial](../saas-apps/perimeter-81-tutorial.md) |
| Silverfort Authentication Platform | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/silverfort-azure-ad-integration](./silverfort-azure-ad-integration.md) |
| Strata Maverics Identity Orchestrator | [https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial](../saas-apps/maverics-identity-orchestrator-saml-connector-tutorial.md) |
| Zscaler Private Access | [https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial](../saas-apps/zscalerprivateaccess-tutorial.md) |
