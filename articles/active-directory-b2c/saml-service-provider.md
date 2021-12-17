---
title: Azure Active Directory B2C を SAML IdP としてアプリケーションに構成する
title-suffix: Azure Active Directory B2C
description: アプリケーション (サービス プロバイダー) に SAML プロトコル アサーションを提供するように Azure Active Directory B2C を構成する方法について学習します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4dee0d3730c74218e236c5d6b51afedc496e02fe
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522804"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>SAML アプリケーションを Azure AD B2C に登録する

この記事では、Security Assertion Markup Language (SAML) アプリケーション (サービス プロバイダー) を Azure Active Directory B2C (Azure AD B2C) に接続して認証を行う方法について説明します。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>概要

顧客 ID およびアクセス管理ソリューションとして Azure AD B2C を使用する組織では、SAML プロトコルを使用して認証するアプリケーションとの統合が必要になる場合があります。 次の図は、Azure AD B2C が "*ID プロバイダー*" (IdP) として機能し、SAML ベースのアプリケーションでシングルサインオン (SSO) を実現する方法を示しています。

![左側に ID プロバイダーとして、右側にサービス プロバイダーとして機能する Azure Active Directory B2C を示す図。](media/saml-service-provider/saml-service-provider-integration.png)

1. アプリケーションで、Azure AD B2C の SAML ログイン エンドポイントに送信される SAML AuthN 要求が作成されます。
2. ユーザーは、Azure AD B2C ローカル アカウント、または他のフェデレーション ID プロバイダー (構成されている場合) を使用して認証を行うことができます。
3. ユーザーがフェデレーション ID プロバイダーを使用してサインインすると、トークン応答は Azure AD B2C に送信されます。
4. Azure AD B2C により、SAML アサーションが生成され、アプリケーションに送信されます。

このビデオでは、SAML アプリケーションを Azure AD B2C と統合する方法について説明します。 

>[!Video https://www.youtube.com/embed/r2TIVBCm7v4]

## <a name="prerequisites"></a>前提条件

この記事のシナリオでは、以下が必要です。

* カスタム ポリシー スターター パックの *SocialAndLocalAccounts* カスタム ポリシー。 「[Azure AD B2C でのカスタム ポリシーの概要](tutorial-create-user-flows.md?pivots=b2c-custom-policy)」の手順を完了します。 
* SAML プロトコルの基礎知識と、アプリケーションの SAML 実装に関する知識。
* SAML アプリケーションとして構成された Web アプリケーション。 SAML AuthN 要求を送信し、Azure AD B2C からの SAML 応答を受信、デコード、検証する機能を備えている必要があります。 SAML アプリケーションは、証明書利用者アプリケーションまたはサービス プロバイダーとも呼ばれます。 
* SAML アプリケーションの一般公開されている SAML *メタデータ エンドポイント* または XML ドキュメント。
* [Azure AD B2C テナント](tutorial-create-tenant.md)。

SAML アプリケーションと関連するメタデータ エンドポイントがまだない場合は、テスト向けに用意されている [SAML テスト アプリケーション][samltest]を使用できます。

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="set-up-certificates"></a>証明書を設定する

アプリケーションと Azure AD B2C の間で信頼関係を構築するには、両方のサービスが相互の署名を作成および検証できる必要があります。 アプリケーションと Azure AD B2C で X509 証明書を構成します。

**アプリケーション証明書**

| 使用法 | 必須 | 説明 |
| --------- | -------- | ----------- |
| SAML 要求の署名  | いいえ | 秘密キーが Web アプリに保存されている証明書。 アプリケーションではこの証明書を使用して、Azure AD B2C に送信された SAML 要求に署名します。 Web アプリでは、SAML メタデータ エンドポイントを使用して公開キーを公開する必要があります。 Azure AD B2C では、アプリケーション メタデータから公開キーを使用して、SAML 要求の署名を検証します。|
| SAML アサーションの暗号化  | いいえ | 秘密キーが Web アプリに保存されている証明書。 Web アプリでは、SAML メタデータ エンドポイントを使用して公開キーを公開する必要があります。 Azure AD B2C では、公開キーを使用してアプリケーションへのアサーションを暗号化できます。 アプリケーションで、秘密キーを使用してアサーションの暗号化を解除します。|

**Azure AD B2C の証明書**

| 使用法 | 必須 | 説明 |
| --------- | -------- | ----------- |
| SAML 応答の署名 | はい  | 秘密キーが Azure AD B2C に保存されている証明書。 この証明書は、アプリケーションに送信される SAML 応答に署名するために Azure AD B2C によって使用されます。 アプリケーションでは、Azure AD B2C のメタデータ公開キーを読み取り、SAML 応答の署名を検証します。 |
| SAML アサーション署名 | はい | 秘密キーが Azure AD B2C に保存されている証明書。 Azure AD B2C ではこの証明書を使用して、SAML 応答の `<saml:Assertion>` 部分に署名します。  |

運用環境では、公的証明機関によって発行された証明書を使用することをお勧めします。 しかし、自己署名証明書でこの手順を完了することもできます。

### <a name="create-a-policy-key"></a>ポリシー キーを作成する

アプリケーションと Azure AD B2C の間で信頼関係を確立するには、SAML 応答の署名証明書を作成します。 この証明書は、アプリケーションに送信される SAML 応答に署名するために Azure AD B2C によって使用されます。 アプリケーションでは、Azure AD B2C のメタデータ公開キーを読み取り、SAML 応答の署名を検証します。 

> [!TIP]
> このポリシー キーは、[SAML アサーション](saml-service-provider-options.md#check-the-saml-assertion-signature)に署名するなどの他の目的で使用できます。 

### <a name="obtain-a-certificate"></a>証明書を取得する

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>証明書をアップロードする

証明書を Azure AD B2C テナントに格納する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選びます。
1. **[概要]** ページで、 **[Identity Experience Framework]** を選択します。
1. **[ポリシー キー]** を選択し、 **[追加]** を選択します。
1. **[オプション]** では、 **[アップロード]** を選択します。
1. **[名前]** には、ポリシー キーの名前を入力します。 たとえば、「**SamlIdpCert**」と入力します。 プレフィックス **B2C_1A_** がキーの名前に自動的に追加されます。
1. 秘密キーが含まれている証明書の .pfx ファイルを参照して選択します。
1. **[作成]** を選択します。

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>SAML アプリケーションと接続するためのポリシーを有効にする

SAML アプリケーションに接続するには、Azure AD B2C が SAML 応答を作成できる必要があります。

カスタム ポリシー スターター パックの *SocialAndLocalAccounts\TrustFrameworkExtensions.xml* を開きます。

`<ClaimsProviders>` セクションを見つけ、次の XML スニペットを追加して SAML 応答ジェネレーターを実装します。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML-based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issuer-uri-of-the-saml-response"></a>SAML 応答の発行者 URI を構成する

SAML トークン発行者の技術プロファイルで、`IssuerUri` メタデータ項目の値を変更できます。 この変更は、Azure AD B2C からの SAML 応答で返される `issuerUri` 属性に反映されます。 SAML 応答の検証時に同じ `IssuerUri` 値を受け入れるようにアプリケーションを構成します。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-your-policy-to-issue-a-saml-response"></a>SAML 応答を発行するようにポリシーを構成する

ポリシーで SAML 応答を作成できるようになったので、アプリケーションに対する既定の JWT 応答ではなく SAML 応答を発行するようにポリシーを構成する必要があります。

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>SAML 用に構成されたサインアップまたはサインイン ポリシーを作成する

1. スターター パックの作業ディレクトリに *SignUpOrSignin* ファイルのコピーを作成し、新しい名前で保存します。 この記事では、例として *SignUpOrSigninSAML.xml* を使用します。 このファイルは、証明書利用者のポリシー ファイルです。 既定では JWT 応答を発行するように構成されています。

1. 任意のエディターで *SignUpOrSigninSAML* ファイルを開きます。

1. ポリシーの `PolicyId` および `PublicPolicyUri` の値を `B2C_1A_signup_signin_saml` および `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` に変更します。

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. ユーザー体験の終了時に、Azure AD B2C に `SendClaims` ステップが含まれます。 このステップでは、トークン発行者の技術プロファイルが参照されます。 既定の JWT 応答ではなく SAML 応答を発行するには、新しい SAML トークン発行者の技術プロファイル `Saml2AssertionIssuer` を参照するように、`SendClaims` ステップを変更します。

`<RelyingParty>` 要素の直前に次の XML スニペットを追加します。 この XML により、_SignUpOrSignIn_ ユーザー体験のオーケストレーション ステップ 7 が上書きされます。 

スターター パックの別のフォルダーから開始した場合、またはオーケストレーション ステップを追加または削除してユーザー体験をカスタマイズした場合は、`order` 要素の番号がトークン発行者ステップのユーザー体験で指定された番号に対応していることを確認してください。 たとえば、他のスターター パック フォルダーでは、対応するステップ番号は、`LocalAccounts` の場合は 4、`SocialAccounts` の場合は 6、`SocialAndLocalAccountsWithMfa` の場合は 9 です。

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

証明書利用者要素によって、アプリケーションで使用されるプロトコルが決まります。 既定では、 `OpenId`です。 `Protocol` 要素を `SAML` に変更する必要があります。 出力要求により、SAML アサーションに対する要求のマッピングが作成されます。

`<RelyingParty>` 要素の `<TechnicalProfile>` 要素全体を次のテクニカル プロファイル XML に置き換えます。 Azure AD B2C テナントの名前で `tenant-name` を更新します。

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

証明書利用者の最終的なポリシー ファイルは、次の XML コードのようになるはずです。

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> この同じプロセスに従って、他の種類のユーザー フロー (サインイン、パスワード リセット、プロファイル編集フローなど) を実装できます。

### <a name="upload-your-policy"></a>ポリシーをアップロードする

変更を保存して、新しい *TrustFrameworkExtensions.xml* と *SignUpOrSigninSAML.xml* の各ポリシー ファイルを Azure portal にアップロードします。

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Azure AD B2C IdP SAML メタデータをテストする

ポリシー ファイルがアップロードされた後、Azure AD B2C では構成情報を使用して、アプリケーションで使用される ID プロバイダーの SAML メタデータ ドキュメントが生成されます。 その SAML メタデータ ドキュメントには、サインインの方法、ログアウトの方法、証明書などのサービスの場所が含まれます。

Azure AD B2C ポリシー メタデータは、次の URL から入手できます。

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

`<tenant-name>`を Azure AD B2C テナントの名前に置き換えます。 `<policy-name>` を、ポリシーの名前 (ID) に置き換えます。 次に例を示します。

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>SAML アプリケーションを Azure AD B2C に登録する

Azure AD B2C にアプリケーションを信頼させるには、Azure AD B2C アプリケーションの登録を作成します。 その登録には、アプリケーションのメタデータ エンドポイントなどの構成情報が含まれます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、**[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、「**SAMLApp1**」と入力します。
1. **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
1. **[リダイレクト URI]** で **[Web]** を選択し、「`https://localhost`」と入力します。 後で、この値をアプリケーションの登録のマニフェストで変更します。
1. **[登録]** を選択します。

### <a name="configure-your-application-in-azure-ad-b2c"></a>Azure AD B2C でアプリケーションを構成する

SAML アプリの場合、アプリケーションの登録のマニフェストでいくつかのプロパティを構成する必要があります。

1. [Azure portal](https://portal.azure.com) で、前のセクションで作成したアプリケーションの登録に移動します。
1. **[管理]** で、 **[マニフェスト]** を選択してマニフェスト エディターを開きます。 その後、以降のセクションで説明されているプロパティを変更します。

#### <a name="add-the-identifier"></a>識別子を追加する

SAML アプリケーションで Azure AD B2C に対して要求が行われると、SAML AuthN 要求に `Issuer` 属性が含まれます。 この属性の値は、通常、アプリケーションのメタデータ `entityID` 値と同じです。 Azure AD B2C では、この値を使用して、ディレクトリ内でアプリケーションの登録を検索し、構成を読み取ります。 この検索を成功させるには、アプリケーションの登録内の `identifierUri` に、`Issuer` 属性と一致する値を設定する必要があります。

登録マニフェストで、`identifierURIs` パラメーターを見つけて、適切な値を追加します。 この値は、アプリケーションで `EntityId` の SAML AuthN 要求内に構成されたのと同じ値であり、アプリケーションのメタデータ内の `entityID` 値となります。 また、`accessTokenAcceptedVersion` パラメーターを検索し、値を `2` に設定する必要があります。

> [!IMPORTANT]
> `accessTokenAcceptedVersion` を `2` に更新しない場合は、検証済みドメインを求めるエラー メッセージが表示されます。

次の例は、SAML メタデータ内の `entityID` 値を示しています。

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

`identifierUris` プロパティでは、ドメイン `tenant-name.onmicrosoft.com` の URL のみが受け入れられます。

```json
"identifierUris":"https://tenant-name.onmicrosoft.com",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>アプリケーションのメタデータを Azure AD B2C と共有する

アプリケーションの登録がその `identifierUri` 値によって読み込まれた後、Azure AD B2C ではアプリケーションのメタデータを使用して、SAML AuthN 要求が検証され、応答方法が決定されます。

アプリケーションで、パブリックにアクセスできるメタデータ エンドポイントを公開することをお勧めします。

SAML メタデータ URL とアプリケーションの登録のマニフェストの "*両方*" に指定されたプロパティがある場合、それらは "*統合*" されます。 メタデータ URL に指定されたプロパティが最初に処理され、優先されます。

SAML テスト アプリケーションを例として使用すると、アプリケーション マニフェスト内の `samlMetadataUrl` に次の値を使用します。

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>アサーション コンシューマーの URL をオーバーライドまたは設定する (省略可能)

Azure AD B2C が SAML 応答を送信する応答 URL を構成することができます。 応答 URL はアプリケーション マニフェストで構成できます。 この構成は、アプリケーションで、パブリックにアクセスできるメタデータ エンドポイントを公開しない場合に役立ちます。

SAML アプリケーションの応答 URL は、アプリケーションが SAML 応答の受信を想定するエンドポイントです。 この例に示すように、アプリケーションでは、通常、メタデータ ドキュメント内の `AssertionConsumerService` 要素の `Location` 属性として、この URL を指定します。

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

アプリケーションのメタデータ `AssertionConsumerService` 要素が見つからない場合、またはオーバーライドする場合は、アプリケーション登録マニフェスト `replyUrlsWithType` プロパティを構成します。 Azure AD B2C では、バインドの種類 `HTTP-POST` を使って、サインイン後に `replyUrlsWithType` を使用してユーザーをリダイレクトします。

SAML テスト アプリケーションを例として使用して、`replyUrlsWithType` の `url` プロパティを、次の JSON スニペットに示す値に設定します。

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>ログアウト URL をオーバーライドまたは設定する (省略可能)

ログアウト URL によって、ログアウト要求後のユーザーのリダイレクト先が定義されます。 次の例に示すように、アプリケーションでは、通常、メタデータ ドキュメント内の `SingleLogoutService` 要素の `Location` 属性として、この URL を指定します。

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</SPSSODescriptor>
```

アプリケーションのメタデータ `SingleLogoutService` 要素が見つからない場合は、アプリケーション登録マニフェスト `logoutUrl` プロパティを構成します。 Azure AD B2C では、バインドの種類 `HTTP-Redirect` を使って、サインアウト後に `logoutURL` を使用してユーザーをリダイレクトします。

SAML テスト アプリケーションを例として使用して、`logoutUrl` プロパティを `https://samltestapp2.azurewebsites.net/logout` に設定します。

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> `samlMetadataUrl` プロパティを使用してアプリケーションのメタデータ エンドポイントを設定せずにアプリケーション マニフェストで応答 URL とログアウト URL を構成することを選択した場合、Azure AD B2C では SAML 要求の署名は検証されません。 SAML 応答も暗号化されません。

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>SAML アプリケーションで Azure AD B2C を SAML IdP として構成する

最後のステップとして、SAML アプリケーションで Azure AD B2C を SAML IdP として有効にします。 アプリケーションはそれぞれ異なり、ステップもさまざまです。 詳細については、アプリのドキュメントを参照してください。

アプリケーションでは、メタデータが "*静的メタデータ*" または "*動的メタデータ*" として構成されている可能性があります。 静的モードでは、メタデータのすべてまたは一部を Azure AD B2C ポリシー メタデータからコピーします。 動的モードでは、メタデータへの URL を指定し、アプリケーションでメタデータを動的に読み取れるようにします。

通常、以下の一部またはすべてが必要です。

* **メタデータ**: 形式 `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` を使用します。
* **発行者**: SAML 要求の `issuer` 値は、アプリケーション登録マニフェストの `identifierUris` 要素で構成された URI のいずれかと一致する必要があります。 SAML 要求の `issuer` の名前が `identifierUris` 要素に存在しない場合は、[それをアプリケーション登録マニフェストに追加します](#add-the-identifier)。 (例: `https://contoso.onmicrosoft.com/app-name`)。 
* **ログイン URL、SAML エンドポイント、SAML URL**: Azure AD B2C SAML ポリシー メタデータ ファイル内の `<SingleSignOnService>` XML 要素の値を確認します。
* **証明書**: この証明書は *B2C_1A_SamlIdpCert* ですが、秘密キーは含まれません。 証明書の公開キーを取得するには、次のようにします。

    1. 先ほど指定したメタデータ URL に移動します。
    1. `<X509Certificate>` 要素の値をコピーします。
    1. テキスト ファイルに貼り付けます。
    1. テキスト ファイルを *.cer* ファイルとして保存します。

### <a name="test-with-the-saml-test-app"></a>SAML テスト アプリでテストする

[SAML テスト アプリケーション][samltest]を使用して、構成をテストすることができます。

* テナント名を更新します。
* ポリシー名を更新します。 たとえば、*B2C_1A_signup_signin_saml* を使用します。
* 発行者 URI を指定します。 アプリケーション登録マニフェストの `identifierUris` 要素にある URI のいずれかを使用します。 たとえば、 `https://contoso.onmicrosoft.com/app-name`を使用します。

**[ログイン]** を選択すると、ユーザーのサインイン画面が表示されるはずです。 サインイン後、サンプル アプリケーションに SAML 応答が発行されます。

## <a name="supported-and-unsupported-saml-modalities"></a>サポートされている SAML モダリティとサポートされていない SAML モダリティ

次の SAML アプリケーション シナリオは、独自のメタデータ エンドポイントを使用してサポートされます。

* アプリケーションまたはサービス プリンシパル オブジェクト内に、ログアウト URL に対する複数のログアウト URL または POST バインドを指定する。
* アプリケーションまたはサービス プリンシパル オブジェクト内に、証明書利用者要求を検証するための署名キーを指定する。
* アプリケーションまたはサービス プリンシパル オブジェクト内にトークン暗号化キーを指定する。
* IdP-initiated サインオンを指定する。この場合、ID プロバイダーは Azure AD B2C です。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C GitHub コミュニティ リポジトリ](https://github.com/azure-ad-b2c/saml-sp-tester)から SAML テスト Web アプリを取得してください。
- [Azure AD B2C に SAML アプリケーションを登録するためのオプション](saml-service-provider-options.md)を参照してください。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
