---
title: Azure Active Directory B2C でのセルフサービスによるパスワードの変更 | Microsoft Docs
description: Azure Active Directory B2C でコンシューマー向けにセルフサービスによるパスワードの変更をセットアップする方法を示すトピック。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 028d10b5c005be2db7cfd9c5ca5210ab55f0592a
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448137"
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーにパスワードの変更を構成する  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

ローカル アカウントを使用してサインインしたユーザーは、[セルフサービスによるパスワードのリセット フロー](active-directory-b2c-reference-sspr.md)で説明されている電子メールでの確認による信頼性の証明なしで、パスワード変更機能を使用してパスワードを変更できます。 ユーザーがパスワードの変更フローを終了する前にセッションの有効期限が切れた場合は、もう一度サインインすることを求められます。 

## <a name="prerequisites"></a>前提条件

[概要](active-directory-b2c-get-started-custom.md)に関するページに記載されたローカル アカウントのサインアップ/サインインを完了するように構成された Azure AD B2C テナント。

## <a name="how-to-configure-password-change-in-custom-policy"></a>カスタム ポリシーにパスワードの変更を構成する方法

カスタム ポリシーにパスワードの変更を構成するには、信頼フレームワークの 拡張ポリシーを次のように変更します。 

## <a name="define-a-claimtype-oldpassword"></a>ClaimType 'oldPassword' を定義する

カスタム ポリシーの全体的な構造に `ClaimsSchema` を含め、新しい `ClaimType` 'oldPassword' を次のように定義します。 

```XML
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="oldPassword">
        <DisplayName>Old Password</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Enter password</UserHelpText>
        <UserInputType>Password</UserInputType>
      </ClaimType>
    </ClaimsSchema>
  </BuildingBlocks>
```

これらの要素の目的は次のとおりです。

- `ClaimsSchema` はどの要求が検証対象かを定義します。  この例では、'古いパスワード' が検証されます。 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>パスワード変更クレーム プロバイダーとそのサポート要素を追加する

パスワード変更クレーム プロバイダーは次の操作を行います。

1. 古いパスワードに対してユーザーを認証します。
2. [新しいパスワード] と [新しいパスワードの確認] の入力が一致していれば、その値が B2C データ ストアに格納され、パスワードが正常に変更されます。 

![画像](images/passwordchange.jpg)

次のクレーム プロバイダーを拡張ポリシーに追加します。 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="login-NonInteractive-PasswordChange">
          <DisplayName>Local Account SignIn</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
            <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
            <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
            <Item Key="ProviderName">https://sts.windows.net/</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
            <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">query</Item>
            <Item Key="scope">email openid</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
            <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
            <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
            <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
            <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Local Account Password Change</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
          <DisplayName>Change password (username)</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
          </CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
            <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
```



### <a name="add-the-application-ids-to-your-custom-policy"></a>アプリケーション ID をカスタム ポリシーに追加する

アプリケーション ID を拡張ファイル (`TrustFrameworkExtensions.xml`) に追加します。

1. 拡張ファイル (TrustFrameworkExtensions.xml) で、要素 `<TechnicalProfile Id="login-NonInteractive">` と `<TechnicalProfile Id="login-NonInteractive-PasswordChange">` を見つけます。

2. `IdentityExperienceFrameworkAppId` のすべてのインスタンスを、[概要](active-directory-b2c-get-started-custom.md)で説明されている Identity Experience Framework アプリケーションのアプリケーション ID に置き換えます。 たとえば次のようになります。

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. `ProxyIdentityExperienceFrameworkAppId` のすべてのインスタンスを、[概要](active-directory-b2c-get-started-custom.md)で説明されている Proxy Identity Experience Framework アプリケーションのアプリケーション ID に置き換えます。

4. 拡張ファイルを保存します。



## <a name="create-a-password-change-user-journey"></a>パスワード変更のユーザー体験を作成する

```XML
 <UserJourneys>
    <UserJourney Id="PasswordChange">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

拡張ファイルの変更が終了しました。 このファイルを保存してアップロードします。 すべての検証が成功することを確認します。



## <a name="create-a-relying-party-rp-file"></a>証明書利用者 (RP) ファイルの作成

次に、作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに ProfileEdit.xml のコピーを作成します。 次に、名前を変更します (例: PasswordChange.xml)。
2. 新しいファイルを開き、`<TrustFrameworkPolicy>` の `PolicyId` 属性を一意の値で更新します。 これがポリシーの名前になります (例: PasswordChange)。
3. `<DefaultUserJourney>` の `ReferenceId` 属性を変更して、作成した新しいユーザー体験の `Id` と一致するようにします (例: PasswordChange)。
4. 変更内容を保存し、ファイルをアップロードします。
5. アップロードしたカスタムのポリシーをテストするには、Azure Portal でポリシー ブレードに移動し、**[今すぐ実行]** をクリックします。




## <a name="link-to-password-change-sample-policy"></a>パスワード変更のサンプル ポリシーへのリンク

[こちら](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)でサンプル ポリシーを見つけることができます。 










