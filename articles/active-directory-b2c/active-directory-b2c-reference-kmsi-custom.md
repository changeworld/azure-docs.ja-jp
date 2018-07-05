---
title: Azure Active Directory B2C の "サインインしたままにする (KMSI)" | Microsoft Docs
description: "\"サインインしたままにする\" ための設定方法を説明するトピック。"
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6bad6e1f2b204f76b075652a9d3f27367a8de49f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441319"
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: "サインインしたままにする (KMSI)" を有効にする  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C は、Web アプリケーションとネイティブ アプリケーションで "サインインしたままにする (KMSI)" 機能を有効にできるようになりました。 この機能では、ユーザー名とパスワードの再入力を求めることなく、戻ってきたユーザー にアプリケーションへのアクセスを許可します。 このアクセスは、ユーザーがログアウトすると失効します。 

公共のコンピューターでは、ユーザーはこのオプションにチェックを入れないよう推奨されています。 

![画像](images/kmsi.PNG)


## <a name="prerequisites"></a>前提条件

[概要](active-directory-b2c-get-started-custom.md)に関するページに記載されているように、ローカル アカウントのサインアップとサインインを許可するように構成された Azure AD B2C テナント。

## <a name="how-to-enable-kmsi"></a>KMSI を有効にする方法

信頼フレームワークの拡張ポリシーで、次の変更を行います。

## <a name="adding-a-content-definition-element"></a>コンテンツ定義要素の追加 

拡張ファイルの `BuildingBlocks` ノードには、`ContentDefinitions` 要素を含める必要があります。 

1. `ContentDefinitions` セクションで、ID `api.signuporsigninwithkmsi` を持つ新しい `ContentDefinition` を定義します。
2. 新しい `ContentDefinition` には、次のように、`LoadUri`、`RecoveryUri` および `DataUri` を含める必要があります。
3. Datauri `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` は、サインイン ページに KMSI チェック ボックスを表示する、マシンが理解できる識別子です。 この値は変更しないでください。 

```XML
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.signuporsigninwithkmsi">
        <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>                       
```



## <a name="add-a--local-account-sign-in-claims-provider"></a>ローカル アカウント サインイン クレーム プロバイダーの追加 

ポリシーの拡張ファイル内にある `<ClaimsProvider>` ノードに、ローカル アカウント サインインをクレーム プロバイダーとして定義できます。

1. 作業ディレクトリから拡張ファイル (TrustFrameworkExtensions.xml) を開きます。 
2. セクション `<ClaimsProviders>` を探します。 存在しない場合は、ルート ノードの下に追加します。
3. [概要](active-directory-b2c-get-started-custom.md)のページからのスターター パックには、"ローカル アカウント サインイン" クレーム プロバイダーが付属しています。 
4. 付属していない場合は、新しい `<ClaimsProvider>` ノードを次のように追加します。

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
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
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

## <a name="create-a-kmsi-in-enabled-user-journey"></a>有効なユーザー体験での KMSI の作成

ここで、自分のユーザー体験に、ローカル アカウント サインイン クレーム プロバイダーを追加する必要があります。 

1. ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
2. `<UserJourneys>` 要素を見つけて、`Id="SignUpOrSignIn"` を含む `<UserJourney>` ノード全体をコピーします。
3. 拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<UserJourneys>` 要素を見つけます。 要素が存在しない場合は追加します。
4. コピーした `<UserJourney>` ノード全体を `<UserJourneys>` 要素の子として貼り付けます。
5. 新しいユーザー体験の ID の名前を変更します (たとえば `SignUpOrSignInWithKmsi` に名前を変更します)。
6. 最後に `OrchestrationStep 1` で、`ContentDefinitionReferenceId` を前述の手順で定義した `api.signuporsigninwithkmsi` に変更します。 これで、ユーザー体験のチェック ボックスが有効になります。 
7. 拡張ファイルの変更が終了しました。 このファイルを保存してアップロードします。 すべての検証が成功することを確認します。

```XML
<UserJourneys>
    <UserJourney Id="SignUpOrSignInWithKmsi">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- This step reads any user attributes that we may not have received when in the token. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

## <a name="create-a-relying-party-rp-file"></a>証明書利用者 (RP) ファイルの作成

次に、作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに SignUpOrSignIn.xml のコピーを作成します。 次に、名前を変更します (例: SignUpOrSignInWithKmsi.xml)。

2. 新しいファイルを開き、`<TrustFrameworkPolicy>` の `PolicyId` 属性を一意の値で更新します。 これがポリシーの名前になります (例: SignUpOrSignInWithKmsi)。

3. `<DefaultUserJourney>` の `ReferenceId` 属性を変更して、作成した新しいユーザー体験の `Id` と一致するようにします (例: SignUpOrSignInWithKmsi)。

4. KMSI は `UserJourneyBehaviors` に構成されています。 

5. **`KeepAliveInDays`** は、ユーザーのサインイン状態の継続期間を制御します。 次の例では、KMSI セッションは、ユーザーがサイレント認証を実行する頻度に関係なく 14 日後に自動的に期限が切れます。

   `KeepAliveInDays` の値を 0 に設定すると、KMSI 機能がオフになります。 既定では、この値は 0 です。

6. **`SessionExpiryType`** が *ローリング*である場合は、KMSI セッションは、ユーザーがサイレント認証を実行するたびに 14 日延長されます。  *ローリング*が選択されている場合、日数は最小値にすることをお勧めします。 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
         <SessionExpiryType>Absolute</SessionExpiryType>
         <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
       </UserJourneyBehaviors>
       <TechnicalProfile Id="PolicyProfile">
         <DisplayName>PolicyProfile</DisplayName>
         <Protocol Name="OpenIdConnect" />
         <OutputClaims>
           <OutputClaim ClaimTypeReferenceId="displayName" />
           <OutputClaim ClaimTypeReferenceId="givenName" />
           <OutputClaim ClaimTypeReferenceId="surname" />
           <OutputClaim ClaimTypeReferenceId="email" />
           <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
         </OutputClaims>
         <SubjectNamingInfo ClaimType="sub" />
       </TechnicalProfile>
       </RelyingParty>

7. 変更内容を保存し、ファイルをアップロードします。

8. アップロードしたカスタムのポリシーをテストするには、Azure Portal でポリシー ブレードに移動し、**[今すぐ実行]** をクリックします。


## <a name="link-to-sample-policy"></a>サンプル ポリシーへのリンク

[こちら](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)でサンプル ポリシーを見つけることができます。








