---
title: B2B 用 SAML/WS-Fed ID プロバイダー (IdP) とのフェデレーション - Azure AD
description: ご自身の Azure AD アプリにゲストがサインインできるようにするために、SAML または WS-Fed ID プロバイダーと直接連携します
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/20/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8a535f7c74e32e59918badbda7747f24a1756f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623990"
---
# <a name="federation-with-samlws-fed-identity-providers-for-guest-users-preview"></a>ゲスト ユーザー向けの SAML/WS-Fed ID プロバイダーとのフェデレーション (プレビュー)

> [!NOTE]
>- Azure Active Directory の "*直接フェデレーション*" は、"*SAML/WS-Fed ID プロバイダー (IdP) フェデレーション*" と呼ばれるようになりました。
>- SAML/WS-Fed IdP フェデレーションは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、ID プロバイダー (IdP) により SAML 2.0 または WS-Fed プロトコルがサポートされている組織とのフェデレーションを設定する方法について説明します。 パートナーの IdP とのフェデレーションを設定すると、そのドメインに属する新しいゲスト ユーザーが、各自の IdP マネージド組織アカウントを使用してお客様の Azure AD テナントにサインインし、お客様との共同作業を開始できます。 ゲスト ユーザーが別個の Azure AD アカウントを作成する必要はありません。

> [!IMPORTANT]
> - 認証 URL ドメインがターゲット ドメインと一致するか、許可されている IdP からのものである必要があるという制限が削除されました。 詳細については、「[手順 1: パートナーが DNS テキスト レコードを更新する必要があるかどうかを判断する](#step-1-determine-if-the-partner-needs-to-update-their-dns-text-records)」を参照してください。
>-  パートナーは、SAML または WS-Fed ベースの IdP の対象ユーザーをテナントの対象ユーザーに設定することをお勧めします。 下記の [SAML 2.0](#required-saml-20-attributes-and-claims) および [WS-Fed](#required-ws-fed-attributes-and-claims) の必須の属性および要求のセクションを参照してください。

## <a name="when-is-a-guest-user-authenticated-with-samlws-fed-idp-federation"></a>ゲスト ユーザーが SAML/WS-Fed IdP フェデレーションで認証されるタイミング

組織の SAML/WS-Fed IdP とのフェデレーションを設定すると、お客様が招待する新しいゲスト ユーザーは、その SAML/WS-Fed IdP を使用して認証されます。 フェデレーションを設定しても、お客様からの招待を既に利用したゲスト ユーザーに対する認証方法は変更されない点に留意することが重要です。 次に例をいくつか示します。

 - ゲスト ユーザーがお客様からの招待を既に利用した後に組織の SAML/WS-Fed IdP とのフェデレーションを設定した場合、それらのゲスト ユーザーは、フェデレーション設定前に使用していたのと同じ認証方法を引き続き使用することになります。
 - 組織の SAML/WS-Fed IdP とのフェデレーションを設定してゲスト ユーザーを招待し、それ以降にそのパートナー組織が Azure AD に移行した場合、お客様のテナント内にフェデレーション ポリシーが存在する限り、既に招待を利用したゲスト ユーザーは引き続きフェデレーション SAML/WS-Fed IdP を使用します。
 - 組織の SAML/WS-Fed IdP とのフェデレーションを削除すると、SAML/WS-Fed IdP を現在使用しているすべてのゲスト ユーザーはサインインできなくなります。

これらのいずれのシナリオでも、[利用状態をリセットする](reset-redemption-status.md)ことで、ゲスト ユーザーの認証方法を更新できます。

SAML/WS-Fed IdP フェデレーションは、contoso.com や fabrikam.com などのドメイン名前空間に関連付けられます。 組織では、AD FS またはサード パーティの IdP とのフェデレーションを確立するときに、これらの IdP に 1 つ以上のドメイン名前空間を関連付けます。

## <a name="end-user-experience"></a>エンド ユーザー エクスペリエンス 

SAML/WS-Fed IdP フェデレーションを使用すると、ゲスト ユーザーは各自の組織アカウントを使用してお客様の Azure AD テナントにサインインします。 共有リソースへのアクセス時にサインインを求められると、ユーザーは自分の IdP にリダイレクトされます。 サインインが成功した後、ユーザーは Azure AD に戻ってリソースにアクセスします。 更新トークンの有効期間は 12 時間 (Azure AD での[パススルー更新トークンの既定の期間](../develop/active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties)) です。 連携した IdP で SSO が有効な場合、ユーザーは SSO を体験することになり、初回認証後にサインインを求めるメッセージは表示されません。

## <a name="sign-in-endpoints"></a>サインインのエンドポイント

SAML/WS-Fed IdP フェデレーションのゲスト ユーザーは、[共通のエンドポイント](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (つまり、テナント コンテキストを含まない一般的なアプリ URL) を使用して、マルチテナントまたは Microsoft のファーストパーティ アプリにサインインできるようになりました。 サインイン プロセス中に、ゲスト ユーザーは **[サインイン オプション]** を選択してから、 **[Sign in to an organization]\(組織にサインイン\)** を選択します。 次に、ユーザーは組織の名前を入力し、自分の資格情報を使用してサインインを続行します。

SAML/WS-Fed IdP フェデレーションのゲスト ユーザーは、テナント情報を含むアプリケーション エンドポイントを使用することもできます。次に例を示します。

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

また、`https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` のようにテナント情報を含めることによって、アプリケーションまたはリソースへの直接リンクをゲスト ユーザーに提供することもできます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-set-up-samlws-fed-idp-federation-with-azure-ad-verified-domains"></a>Azure AD の検証済みドメインを使用して SAML/WS-Fed IdP フェデレーションを設定できますか。
いいえ、Azure AD の検証済みドメインに対する SAML/WS-Fed IdP のフェデレーションをブロックし、ネイティブの Azure AD マネージド ドメイン機能を採用しています。 Azure AD で DNS 検証済みのドメインと SAML/WS-Fed IdP フェデレーションを設定しようとした場合は、Microsoft Azure portal や PowerShell でエラーが表示されます。

### <a name="can-i-set-up-samlws-fed-idp-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>アンマネージド (電子メールで検証済み) テナントが存在するドメインとの SAML/WS-Fed IdP フェデレーションを設定できますか。 
はい、管理されていない (電子メール検証済みまたは "バイラル" な) Azure AD テナントなど、Azure AD で DNS 検証されていないドメインと SAML/WS-Fed IdP フェデレーションを設定できます。 そのようなテナントは、ユーザーが B2B の招待に応じたときや、現在は存在しないドメインを使用して Azure AD のセルフサービス サインアップを実行したときに作成されます。 ドメインが検証されておらず、テナントで[管理者の引き継ぎ](../enterprise-users/domains-admin-takeover.md)が実施されていない場合は、そのドメインとのフェデレーションを設定できます。

### <a name="how-many-federation-relationships-can-i-create"></a>フェデレーション関係はいくつ作成できますか。
現在のところ、最大 1000 のフェデレーション リレーションシップがサポートされています。 この制限には、[内部フェデレーション](/powershell/module/msonline/set-msoldomainfederationsettings)と SAML/WS-Fed IdP フェデレーションの両方が含まれます。

### <a name="can-i-set-up-federation-with-multiple-domains-from-the-same-tenant"></a>同じテナントから、複数のドメインとのフェデレーションを設定できますか。
現在、同じテナントの複数のドメインとの SAML/WS-Fed IdP フェデレーションはサポートされていません。

### <a name="do-i-need-to-renew-the-signing-certificate-when-it-expires"></a>署名証明書は、有効期限切れになるときに更新する必要がありますか。
IdP の設定でメタデータ URL を指定した場合、署名証明書が有効期限切れになると、Azure AD によって自動的に更新されます。 ただし、証明書が有効期限切れになる前に何らかの理由でローテーションされた場合、またはメタデータ URL を指定しなかった場合には、Azure AD による更新はできません。 この場合、署名証明書を手動で更新する必要があります。

### <a name="if-samlws-fed-idp-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>SAML/WS-Fed IdP フェデレーションと電子メールのワンタイム パスコード認証の両方が有効な場合、どちらの方法が優先されますか。
パートナー組織との SAML/WS-Fed IdP フェデレーションが確立すると、その組織に属する新しいゲスト ユーザーに対して、電子メールのワンタイム パスコード認証よりもこちらが優先されます。 SAML/WS-Fed IdP フェデレーションを設定する前にゲスト ユーザーがワンタイム パスコード認証を使用して招待を利用した場合、彼らは引き続きワンタイム パスコード認証を使用することになります。

### <a name="does-samlws-fed-idp-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>SAML/WS-Fed IdP フェデレーションは、部分的に同期されたテナントに起因するサインインの問題に対応していますか。
いいえ。このシナリオでは、[電子メールのワンタイム パスコード](one-time-passcode.md)機能を使用する必要があります。 "部分的に同期されたテナント" とは、オンプレミスのユーザー ID がクラウドに完全には同期されていないパートナーの Azure AD テナントのことです。 クラウド上に ID がまだ存在していないゲストが、B2B の招待を利用しようとした場合、そのゲストはサインインできません。 ワンタイム パスコード機能であれば、このゲストをサインインさせることができます。 SAML/WS-Fed IdP フェデレーション機能は、ゲストが各自の IdP マネージド組織アカウントを持っているが、その組織に Azure AD が存在しないというシナリオに対応しています。

### <a name="once-samlws-fed-idp-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>組織で SAML/WS-Fed IdP フェデレーションを構成したら、各ゲストは送付された個別の招待を利用する必要がありますか。
SAML/WS-Fed IdP フェデレーションを設定しても、お客様からの招待を既に利用したゲスト ユーザーに対する認証方法は変更されません。 ゲスト ユーザーの認証方法を更新するには、[利用状態をリセット](reset-redemption-status.md)します。

### <a name="is-there-a-way-to-send-a-signed-request-to-the-saml-identity-provider"></a>署名された要求を SAML ID プロバイダーに送信する方法はありますか。
現在のところ、Azure AD SAML/WS-Fed フェデレーション機能では、署名された認証トークンを SAML ID プロバイダーに送信することはサポートされていません。

## <a name="step-1-determine-if-the-partner-needs-to-update-their-dns-text-records"></a>手順 1: パートナーが DNS テキスト レコードを更新する必要があるかどうかを判断する

パートナーの IdP によっては、パートナーが自身の DNS レコードを更新して、お客様とのフェデレーションを有効にすることが必要な場合があります。 DNS の更新が必要かどうかを判断するには、次の手順に従います。

1. パートナーの IdP が、以下の許可された IdP のいずれかである場合は、DNS の変更は必要ありません (この一覧は変更される可能性があります)。

     - accounts.google.com
     - pingidentity.com
     - login.pingone.com
     - okta.com
     - oktapreview.com
     - okta-emea.com
     - my.salesforce.com
     - federation.exostar.com
     - federation.exostartest.com
     - idaptive.app
     - idaptive.qa

2. IdP が前の手順の一覧に表示されている許可されたプロバイダーのいずれでもない場合は、パートナーの IdP 認証 URL を調べて、ドメインがターゲット ドメインまたはターゲット ドメイン内のホストと一致するかどうかを確認します。 すなわち、`fabrikam.com` のフェデレーションを設定する場合は次のようになります。

     - 認証 URL が `https://fabrikam.com` または `https://sts.fabrikam.com/adfs` (同じドメイン内のホスト) の場合、DNS の変更は必要ありません。
     - 認証 URL が `https://fabrikamconglomerate.com/adfs`  または  `https://fabrikam.com.uk/adfs` の場合、ドメインは fabrikam.com ドメインと一致していないので、パートナーは DNS 構成に認証 URL のテキスト レコードを追加する必要があります。

    > [!IMPORTANT]
    > 次の手順には、既知の問題があります。 現在のところ、フェデレーション IdP のドメインに DNS テキスト レコードを追加しても認証のブロックが解除されません。 現在、この問題の解決に積極的に取り組んでいます。

3. 前の手順に基づいて DNS の変更が必要な場合は、次の例のように、ドメインの DNS レコードに TXT レコードを追加するようにパートナーに依頼します。

   `fabrikam.com.  IN   TXT   DirectFedAuthUrl=https://fabrikamconglomerate.com/adfs`

## <a name="step-2-configure-the-partner-organizations-idp"></a>手順 2: パートナー組織の IdP を構成する

次に、パートナー組織において、必須の要求と証明書利用者の信頼を指定して IdP を構成する必要があります。

> [!NOTE]
> フェデレーション用に SAML/WS-Fed IdP を構成する方法を示すために、例として Active Directory フェデレーション サービス (AD FS) を使用します。 [AD FS との SAML/WS-Fed IdP フェデレーションの構成](direct-federation-adfs.md)に関する記事を参照してください。フェデレーションの準備として、AD FS を SAML 2.0 または WS-Fed の IdP として構成する方法の例が示されています。

### <a name="saml-20-configuration"></a>SAML 2.0 の構成

Azure AD B2B は、以下に示す特定の要件を伴う SAML プロトコルを使用する IdP とフェデレーションを行うように構成できます。 ご利用の SAML IdP と Azure AD の間に信頼を設定する方法の詳細については、「[シングル サインオンに SAML 2.0 ID プロバイダー (IdP) を使用する](../hybrid/how-to-connect-fed-saml-idp.md)」を参照してください。  

> [!NOTE]
> SAML/WS-Fed IdP フェデレーションのターゲット ドメインは、Azure AD の DNS によって検証されていない必要があります。 詳細については、「[よく寄せられる質問](#frequently-asked-questions)」セクションを参照してください。

#### <a name="required-saml-20-attributes-and-claims"></a>必須の SAML 2.0 属性および要求
サード パーティの IdP に構成する必要がある特定の属性と要求の要件を次の各表に示します。 フェデレーションを設定するには、IdP からの SAML 2.0 応答において以下の属性が受け取られる必要があります。 これらの属性は、オンライン セキュリティ トークン サービスの XML ファイルにリンクするか手動で入力することによって、構成できます。

IdP からの SAML 2.0 応答に必須の属性:

|属性  |値  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|対象ユーザー     |`https://login.microsoftonline.com/<tenant ID>/` (推奨されるテナントの対象ユーザー) `<tenant ID>` を、フェデレーションを設定する Azure AD テナントのテナント ID に置き換えます。<br><br>`urn:federation:MicrosoftOnline` (この値は非推奨です。)          |
|発行者     |パートナー IdP の発行者 URI (たとえば `http://www.example.com/exk10l6w90DHM0yi...`)         |


IdP によって発行される SAML 2.0 トークンに必須の要求:

|属性  |値  |
|---------|---------|
|NameID の形式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed の構成

Azure AD B2B は、以下に示す特定の要件を伴う WS-Fed プロトコルを使用する IdP とフェデレーションを行うように構成できます。 現時点で 2 つの WS-Fed プロバイダー (AD FS と Shibboleth) が、Azure AD との互換性テスト済みです。 WS-Fed 準拠のプロバイダーと Azure AD の間に証明書利用者信頼を確立する方法の詳細については、[Azure AD ID プロバイダーの互換性に関するドキュメント](https://www.microsoft.com/download/details.aspx?id=56843)で「STS Integration Paper using WS Protocols」(WS プロトコルを使用した STS 統合に関する文書) を参照してください。

> [!NOTE]
> フェデレーションのターゲット ドメインは Azure AD で DNS によって検証できません。 詳細については、「[よく寄せられる質問](#frequently-asked-questions)」セクションを参照してください。

#### <a name="required-ws-fed-attributes-and-claims"></a>必須の WS-Fed 属性および要求

サード パーティの WS-Fed IdP に構成する必要がある特定の属性と要求の要件を次の各表に示します。 フェデレーションを設定するには、IdP からの WS-Fed メッセージにおいて以下の属性が受け取られる必要があります。 これらの属性は、オンライン セキュリティ トークン サービスの XML ファイルにリンクするか手動で入力することによって、構成できます。

IdP からの WS-Fed メッセージに必須の属性:
 
|属性  |値  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|対象ユーザー     |`https://login.microsoftonline.com/<tenant ID>/` (推奨されるテナントの対象ユーザー) `<tenant ID>` を、フェデレーション対象の Azure AD テナントのテナント ID に置き換えます。<br><br>`urn:federation:MicrosoftOnline` (この値は非推奨です。)          |
|発行者     |パートナー IdP の発行者 URI (たとえば `http://www.example.com/exk10l6w90DHM0yi...`)         |

IdP によって発行される WS-Fed トークンに必須の要求:

|属性  |値  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-3-configure-samlws-fed-idp-federation-in-azure-ad"></a>手順 3: Azure AD で SAML/WS-Fed IdP フェデレーションを構成する

次に、手順 1 で構成した IdP とのフェデレーションを Azure AD で構成します。 Azure AD ポータルまたは PowerShell のいずれかを使用できます。 フェデレーション ポリシーが有効になるまで 5 分から 10 分かかる場合があります。 この間、フェデレーション ドメインの招待を利用しようとしないでください。 次の属性は必須です。

- パートナー IdP の発行者 URI
- パートナー IdP のパッシブ認証エンドポイント (サポートされているのは https のみ)
- Certificate

### <a name="to-configure-federation-in-the-azure-ad-portal"></a>Azure AD ポータルでフェデレーションを構成するには

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 左ウィンドウで、 **[Azure Active Directory]** を選択します。 
2. **[外部 ID]**  >  **[すべての ID プロバイダー]** を選択します。
3. **[新しい SAML/WS-Fed IdP]** を選択します。

    ![新しい SAML または WS-Fed IdP を追加するためのボタンを示すスクリーンショット](media/direct-federation/new-saml-wsfed-idp.png)

4. **[新しい SAML/WS-Fed IdP]** ページの **[ID プロバイダー プロトコル]** で、 **[SAML]** または **[WS-FED]** を選択します。

    ![SAML または WS-Fed IdP ページの解析ボタンを示すスクリーンショット](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. パートナー組織のドメイン名を入力します。これはフェデレーションのターゲット ドメイン名になります
6. メタデータの詳細を設定するメタデータ ファイルをアップロードすることができます。 メタデータを手動で入力する場合は、次の情報を入力します。
   - パートナー IdP のドメイン名
   - パートナー IdP のエンティティ ID
   - パートナー IdP のパッシブな要求元エンドポイント
   - Certificate
   > [!NOTE]
   > メタデータ URL は省略可能ですが、強くお勧めします。 メタデータ URL を指定すると、署名証明書が有効期限切れになったときに、Azure AD によって自動的に更新することができます。 証明書が有効期限切れになる前に何らかの理由でローテーションされた場合、またはメタデータ URL を指定しなかった場合には、Azure AD による更新はできません。 この場合、署名証明書を手動で更新する必要があります。

7. **[保存]** を選択します。 

### <a name="to-configure-samlws-fed-idp-federation-in-azure-ad-using-powershell"></a>PowerShell を使用して Azure AD で SAML/WS-Fed IdP フェデレーションを構成するには

1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 詳細な手順が必要な場合は、クイックスタートに [PowerShell モジュール](b2b-quickstart-invite-powershell.md#prerequisites)のガイダンスが含まれています。
2. 次のコマンドを実行します。

   ```powershell
   Connect-AzureAD
   ```

3. サインイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。
4. フェデレーション メタデータ ファイルにある値に置き換えたうえで、次のコマンドを実行します。 AD FS サーバーと Okta の場合、フェデレーション ファイルは federationmetadata.xml です (例: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`)。 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-4-test-samlws-fed-idp-federation-in-azure-ad"></a>手順 4: Azure AD で SAML/WS-Fed IdP フェデレーションをテストする
ここでは、新しい B2B ゲスト ユーザーを招待して、フェデレーションの設定をテストします。 詳細については、「[Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)」を参照してください。
 
## <a name="how-do-i-edit-a-samlws-fed-idp-federation-relationship"></a>SAML/WS-Fed IdP フェデレーション関係を編集する方法

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 左ウィンドウで、 **[Azure Active Directory]** を選択します。 
2. **[外部 ID]** を選択します。
3. **[すべての ID プロバイダー]** を選択します
4. **SAML/WS-Fed ID プロバイダー** でプロバイダーを選択します。
5. ID プロバイダーの詳細ウィンドウで、値を更新します。
6. **[保存]** を選択します。


## <a name="how-do-i-remove-federation"></a>フェデレーションを削除する方法

フェデレーション設定は削除できます。 実行した場合、既に招待を利用したフェデレーション ゲスト ユーザーは、サインインできなくなります。 ただし、[ユーザーの引き換え状態をリセットする](reset-redemption-status.md)ことで、リソースへのアクセスを再度許可することができます。 Azure AD ポータルで IdP とのフェデレーションを削除するには:

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 左ウィンドウで、 **[Azure Active Directory]** を選択します。
2. **[外部 ID]** を選択します。
3. **[すべての ID プロバイダー]** を選択します。
4. ID プロバイダーを選択し、 **[削除]** を選択します。
5. **[はい]** を選択して削除を確定します。 

PowerShell を使用して ID プロバイダーとのフェデレーションを削除するには:

1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 次のコマンドを実行します。

   ```powershell
   Connect-AzureAD
   ```

3. サインイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。
4. 次のコマンドを入力します。

   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>次のステップ

外部ユーザーがさまざまな ID プロバイダーでサインインするときの[招待の利用エクスペリエンス](redemption-experience.md)を確認します。
