---
title: SAML サービス プロバイダーのオプションを構成する
title-suffix: Azure Active Directory B2C
description: Azure Active Directory B2C SAML サービス プロバイダーのオプションを構成する方法について学習します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 51672c7757e5c747bf2b243e32506159a468f2d5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222161"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C に SAML アプリケーションを登録するためのオプション

この記事では、Azure Active Directory B2C (Azure AD B2C) を対象の Security Assertion Markup Language (SAML) アプリケーションに接続するときに使用できる構成オプションについて説明します。

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="specify-a-saml-response-signature"></a>SAML 応答の署名を指定する

SAML メッセージに署名するために使用される証明書を指定することができます。 メッセージは、アプリケーションに送信される SAML 応答内の `<samlp:Response>` 要素です。

まだポリシー キーがない場合は、[作成](saml-service-provider.md#create-a-policy-key)します。 次に、SAML トークン発行者の技術プロファイル内で `SamlMessageSigning` メタデータ項目を構成します。 `StorageReferenceId` では、ポリシー キー名を参照する必要があります。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

### <a name="signature-algorithm"></a>署名アルゴリズム

SAML アサーションの署名に使用される署名アルゴリズムを構成できます。 指定できる値は、`Sha256`、`Sha384`、`Sha512`、および `Sha1` です。 技術プロファイルとアプリケーションで同じ署名アルゴリズムが使用されていることを確認します。 証明書でサポートされているアルゴリズムのみを使用してください。

証明書利用者の `Metadata` 要素内の `XmlSignatureAlgorithm` メタデータ キーを使用して、署名アルゴリズムを構成します。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="check-the-saml-assertion-signature"></a>SAML アサーション署名を確認する

対象のアプリケーションで SAML アサーション セクションが署名されることを想定している場合は、SAML サービス プロバイダーで `WantAssertionsSigned` が `true` に設定されていることを確認します。 これが `false` に設定されている場合、または存在しない場合、アサーション セクションは署名されません。 

次の例は、`WantAssertionsSigned` が `true` に設定された SAML サービス プロバイダーのメタデータを示しています。

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
  <SPSSODescriptor  WantAssertionsSigned="true" AuthnRequestsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  </SPSSODescriptor>
</EntityDescriptor>
```  

### <a name="signature-certificate"></a>署名証明書

ポリシーでは、SAML 応答の SAML アサーション セクションに署名するために使用する証明書を指定する必要があります。 まだポリシー キーがない場合は、[作成](saml-service-provider.md#create-a-policy-key)します。 次に、SAML トークン発行者の技術プロファイル内で `SamlAssertionSigning` メタデータ項目を構成します。 `StorageReferenceId` では、ポリシー キー名を参照する必要があります。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="enable-encryption-in-saml-assertions"></a>SAML アサーションでの暗号化を有効にする

対象のアプリケーションで SAML アサーションが暗号化形式になっていることを想定している場合は、Azure AD B2C ポリシーで暗号化が有効になっていることを確認します。

Azure AD B2C は、サービス プロバイダーの公開キー証明書を使用して SAML アサーションを暗号化します。 公開キーは、次の例に示すように、`KeyDescriptor` `use` 値が `Encryption` に設定された SAML アプリケーションのメタデータ エンドポイントにある必要があります。

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Azure AD B2C が暗号化されたアサーションを送信できるようにするには、[証明書利用者の技術プロファイル](relyingparty.md#technicalprofile)内で `WantsEncryptedAssertion` メタデータ項目を `true` に設定します。 SAML アサーションの暗号化に使用されるアルゴリズムも構成できます。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

### <a name="encryption-method"></a>暗号化方法

SAML アサーション データの暗号化に使用される暗号化方法を構成するには、証明書利用者内で `DataEncryptionMethod` メタデータ キーを設定します。 指定可能な値は `Aes256` (既定値)、`Aes192`、`Sha512`、または `Aes128` です。 このメタデータにより、SAML 応答内の `<EncryptedData>` 要素の値が制御されます。

SAML アサーション データの暗号化に使用されたキーのコピーを暗号化するための暗号化方法を構成するには、証明書利用者内で `KeyEncryptionMethod` メタデータ キーを設定します。 指定できる値は次のとおりです。

- `Rsa15` (既定値): RSA 公開キー暗号化標準 (PKCS) バージョン 1.5 アルゴリズム。
- `RsaOaep`: RSA 最適非対称暗号化パディング (OAEP) 暗号化アルゴリズム。  

このメタデータにより、SAML 応答内の `<EncryptedKey>` 要素の値が制御されます。

次の例は、SAML アサーションの `EncryptedAssertion` セクションを示しています。 暗号化データ メソッドは `Aes128` で、暗号化キー メソッドは `Rsa15` です。

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

暗号化されたアサーションの形式は変更できます。 暗号化形式を構成するには、証明書利用者内で `UseDetachedKeys` メタデータ キーを設定します。 指定できる値: `true` または `false`(既定値)。 値が `true` に設定されている場合、デタッチされたキーは、暗号化されたアサーションを、`EncryptedData` ではなく `EncryptedAssertion` の子として追加します。

[証明書利用者の技術プロファイル](relyingparty.md#technicalprofile)内でメタデータ キーを使用して、暗号化の方法と形式を構成します。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="configure-idp-initiated-flow"></a>IdP Initiated フローを構成する

対象のアプリケーションで、SAML AuthN 要求を最初に ID プロバイダー (IdP) に送信することなく SAML アサーションを受け取ることを想定している場合は、IdP Initiated フロー用に Azure AD B2C を構成する必要があります。

IdP Initiated フローでは、ID プロバイダー (Azure AD B2C) がサインイン プロセスを開始します。 ID プロバイダーは、要求されていない SAML 応答をサービス プロバイダー (証明書利用者アプリケーション) に送信します。

現在、開始側の ID プロバイダーが、Azure AD B2C とフェデレーションされた外部 ID プロバイダー ([Active Directory フェデレーション サービス (AD FS)](identity-provider-adfs.md)、[Salesforce](identity-provider-salesforce-saml.md) など) であるシナリオはサポートされていません。 IdP Initiated フローは、Azure AD B2C のローカル アカウント認証でのみサポートされます。

IdP Initiated フローを有効にするには、[証明書利用者の技術プロファイル](relyingparty.md#technicalprofile)内で、`IdpInitiatedProfileEnabled` メタデータ項目を `true` に設定します。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

IdP Initiated フローを通じてユーザーをサインインまたはサインアップするには、次の URL を使用します。

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

次の値を置き換えます。

* `<tenant-name>` を、実際のテナント名に置き換えます。
* `<policy-name>` を対象の SAML 証明書利用者ポリシーの名前に置き換えます。
* `app-identifier-uri` をメタデータ ファイルの `identifierUris` 値 (`https://contoso.onmicrosoft.com/app-name` など) に置き換えます。

### <a name="sample-policy"></a>サンプル ポリシー

SAML テスト アプリでテストするための完全なサンプル ポリシーを使用できます。

1. [SAML SP によって開始されるログインのサンプル ポリシー](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)をダウンロードします。
1. テナント名に合わせて `TenantId` を更新します。 この記事では、サンプルの *contoso.b2clogin.com* を使用します。
1. *B2C_1A_signup_signin_saml* のポリシー名をそのままにします。

## <a name="configure-the-saml-response-lifetime"></a>SAML 応答の有効期間を構成する

SAML 応答を有効にする期間を構成できます。 SAML トークン発行者技術プロファイル内の `TokenLifeTimeInSeconds` メタデータ項目を使用して有効期間を設定します。 この値は、トークンの発行時に計算された `NotBefore` タイム スタンプからの経過時間を秒数で示します。 既定の有効期間は 300 秒 (5 分) です。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="configure-the-time-skew-of-a-saml-response"></a>SAML 応答の時間のずれを構成する

SAML 応答の `NotBefore` タイム スタンプに適用される時間のずれを構成できます。 この構成により、2 つのプラットフォーム間の時間が同期していない場合でも、この時間のずれの範囲内であれば、SAML アサーションは有効と見なされます。

時間のずれは、SAML トークン発行者技術プロファイル内の `TokenNotBeforeSkewInSeconds` メタデータ項目を使用して設定します。 時間のずれは秒単位で指定します。既定値は 0 です。 最大値は 3600 (1 時間) です。

たとえば、`TokenNotBeforeSkewInSeconds` が `120` 秒に設定されている場合、次のようになります。

- トークンが 13:05:10 UTC に発行されます。
- トークンは 13:03:10 UTC から有効です。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-the-date-and-time"></a>日付と時刻からミリ秒を削除する

SAML 応答内の日付と時刻の値からミリ秒を削除するかどうかを指定できます (これらの値には、`IssueInstant`、`NotBefore`、`NotOnOrAfter`、および `AuthnInstant` が含まれます)。ミリ秒を削除するには、証明書利用者内で `RemoveMillisecondsFromDateTime` メタデータ キーを設定します。 指定できる値: `false` (既定値) または `true`。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="use-an-issuer-id-to-override-an-issuer-uri"></a>発行者 ID を使用して発行者の URI をオーバーライドする

異なる `entityID` 値に依存する複数の SAML アプリケーションがある場合は、証明書利用者ファイルの `IssuerUri` 値をオーバーライドできます。 発行者の URI をオーバーライドするには、基本ファイルから ID が `Saml2AssertionIssuer` の技術プロファイルをコピーし、`IssuerUri` 値をオーバーライドします。

> [!TIP]
> 基本ファイルから `<ClaimsProviders>` セクションをコピーし、これらの要素を要求プロバイダー `<DisplayName>Token Issuer</DisplayName>`、`<TechnicalProfile Id="Saml2AssertionIssuer">`、および `<DisplayName>Token Issuer</DisplayName>` 内に保存します。
 
例:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="manage-a-session"></a>セッションの管理

`UseTechnicalProfileForSessionManagement` 要素と [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) を使用して、Azure AD B2C と SAML 証明書利用者アプリケーション間のセッションを管理できます。

## <a name="force-users-to-reauthenticate"></a>ユーザーに再認証を強制する 

ユーザーに再認証を強制するために、アプリケーションで SAML 認証要求に `ForceAuthn` 属性を含めることができます。 `ForceAuthn` 属性はブール値です。 `true` に設定すると、ユーザーのセッションは Azure AD B2C で無効になり、ユーザーは再認証するように強制されます。 

次の SAML 認証要求では、`ForceAuthn` 属性を `true` に設定する方法を示しています。 

```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="sign-the-azure-ad-b2c-idp-saml-metadata"></a>Azure AD B2C IdP SAML メタデータに署名する

アプリケーションで必要な場合は、Azure AD B2C に対して、SAML ID プロバイダーのメタデータ ドキュメントに署名するように指示できます。 まだポリシー キーがない場合は、[作成](saml-service-provider.md#create-a-policy-key)します。 次に、SAML トークン発行者の技術プロファイル内で `MetadataSigning` メタデータ項目を構成します。 `StorageReferenceId` では、ポリシー キー名を参照する必要があります。

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

## <a name="debug-the-saml-protocol"></a>SAML プロトコルをデバッグする

サービス プロバイダーとの統合の構成とデバッグを支援するために、SAML プロトコル用のブラウザー拡張機能を使用できます。 ブラウザー拡張機能には、[Chrome 用の SAML DevTools 拡張機能](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio)、[Firefox 用の SAML-tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/)、および [Edge または Internet Explorer 用の開発者ツール](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957)があります。

これらのツールを使用すると、対象のアプリケーションと Azure AD B2C の統合を確認できます。 次に例を示します。

* SAML 要求に署名が含まれているかどうかを確認し、認可要求へのサインインに使用するアルゴリズムを決定します。
* Azure AD B2C からエラー メッセージが返されるかどうかを確認します。
* アサーション セクションが暗号化されているかどうかを確認します。

## <a name="next-steps"></a>次のステップ

- SAML プロトコルの詳細については、[OASIS の Web サイト](https://www.oasis-open.org/)を参照してください。
- [Azure AD B2C GitHub コミュニティ リポジトリ](https://github.com/azure-ad-b2c/saml-sp-tester)から SAML テスト Web アプリを取得してください。

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
