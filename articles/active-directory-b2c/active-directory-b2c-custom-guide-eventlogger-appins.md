---
title: Azure AD B2C から Application Insights のイベントを使用してユーザーの動作を追跡する方法 | Microsoft Docs
description: カスタム ポリシーを使用して Azure AD B2C ユーザー体験から Application Insights のイベント ログを有効にする手順ガイド - プレビュー
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 1e8c4a53266072db71952ee35b15e5de54fabb95
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="tracking-user-behavior-inside-azure-ad-b2c-journeys-using-application-insights"></a>Application Insights を使用した Azure AD B2C ジャーニー内のユーザー動作の追跡

Azure Active Directory B2C は、Azure Application Insights と連携します。  Application Insights は、カスタムの作成されたユーザー体験について詳細でカスタマイズされたイベント ログを提供します。  このガイドでは、次の操作方法の概要について説明します。 
* ユーザーの動作を把握する
* 開発時または運用時にポリシーの問題を解決する
* パフォーマンスを測定する
* Application Insights の通知を作成する

## <a name="how-it-works"></a>動作のしくみ
新しいプロバイダー `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0` が Azure AD B2C の Identity Experience Framework に追加されました。  このプロバイダーは、Azure AD B2C に提供されているインストルメンテーション キーを使用して、イベント データを Application Insight に直接送信します。  技術プロファイルは、このプロバイダーを使用して B2C のイベントを定義します。  このプロファイルには、イベントの名前、記録される要求、およびインストルメンテーション キーを指定します。  イベントを投稿するために、カスタムのユーザー体験には技術プロファイルが `orchestration step` または `validation technical profile` として追加されます。  Application Insight で相関 ID を使用してこのイベントを統合し、ユーザー セッションを記録することができます。  Application Insights では、数秒でイベントとセッションを使用できるようになります。また、多くの視覚化、エクスポート、および分析のツールが用意されています。



## <a name="prerequisites"></a>前提条件
[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関するページの手順を完了します。  このガイドでは、カスタム ポリシー スターター パックが使用されていることを前提としています。  ただし、これは必須ではありません。



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>手順 1. Application Insights リソースを作成し、インストルメンテーション キーを取得する

Application Insights は強力なツールです。 Azure AD B2C と併用する場合、唯一の要件はリソースを作成してインストルメンテーション キーを取得することです。  Application Insights は、[Azure Portal](https://portal.azure.com) で作成する必要があります。

[Application Insights の詳細なドキュメント](https://docs.microsoft.com/azure/application-insights/)

1. Azure Portal でサブスクリプション テナント内の `+ Create a resource` をクリックします。  このテナントは自社の Azure AD B2C テナントではありません。  
2. `Application Insights` を検索して選択します。  
3. 任意のサブスクリプションで `Application Type` `ASP.NET web application` のリソースを作成します。
4. 作成が完了したら、Application Insights リソースを開き、`Instrumentation Key`をメモします。 

![Application Insights の概要とインストルメンテーション キー](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>手順 2. 新しい ClaimType 定義を信頼フレームワーク拡張ファイルに追加する

### <a name="open-the-extension-file-from-the-starter-pack-and-add-the-following-elements-to-the-buildingblocks-node--the-extensions-filename-is-typically-yourtenantonmicrosoftcom-b2c1atrustframeworkextensionsxml"></a>スターター パックから拡張ファイルを開き、次の要素を `<BuildingBlocks>` ノードに追加します。  通常、拡張ファイル名は `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml` です

```xml

    <ClaimsSchema>
      <ClaimType Id="EventType">
        <DisplayName>EventType</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="PolicyId">
        <DisplayName>PolicyId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="Culture">
        <DisplayName>Culture</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="CorrelationId">
        <DisplayName>CorrelationId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <!--Additional claims used for passing claims to Application Insights Provider -->
      <ClaimType Id="federatedUser">
        <DisplayName>federatedUser</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="parsedDomain">
        <DisplayName>Parsed Domain</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>The domain portion of the email address.</UserHelpText>
      </ClaimType>
      <ClaimType Id="userInLocalDirectory">
        <DisplayName>userInLocalDirectory</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
    </ClaimsSchema>

```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>手順 3. Application Insights プロバイダーを使用する新しい技術プロファイルを追加する

技術プロファイルは、Azure AD B2C の Identity Experience Framework の機能と考えることができます。  この例では、セッションを開いてイベントを投稿するための 5 つの技術プロファイルが定義されています。

| 技術プロファイル       | タスク |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | すべての Azure-Insights 技術プロファイルに含まれる共通パラメーター セット     | 
| JourneyContextForInsights   | App Insights でセッションを開き、相関 ID を送信します |
| AzureInsights-SignInRequest     | サインイン要求を受け取ったときに一連の要求を含む "SignIn" イベントを作成します      | 
| AzureInsights-UserSignup | signup/signin ジャーニーでユーザーがサインアップ オプションをトリガーしたときに、"UserSignup" というイベントを作成します     | 
| AzureInsights-SignInComplete | トークンが証明書利用者アプリケーションに送信されたときに、認証の正常完了を記録します     | 

これらの要素を `<ClaimsProviders>` ノードに追加して、スターター パックから拡張ファイルにプロファイルを追加します。  通常、拡張ファイル名は `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml` です

> [!IMPORTANT]
> `ApplicationInsights-Common` 技術プロファイルの `Instrumentation Key` を、Application Insights リソースが提供する GUID に変更します。

```xml
<ClaimsProvider>
      <DisplayName>Application Insights</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="JourneyContextForInsights">
          <DisplayName>Application Insights</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="CorrelationId" />
          </OutputClaims>
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInRequest">
          <InputClaims>
            <!-- 
                            An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
                            to create an event with the specified value.
                        -->
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInComplete">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
            <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
            <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-UserSignup">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-Common">
          <DisplayName>Alternate Email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
            <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
            <!-- 
                            A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
                            with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
                        -->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
                            A boolean indicating whether telemetry should be enabled or not.
                        -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
                            Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
                            to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
                        -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>手順 4. 既存のユーザー体験のオーケストレーション ステップとして Application-Insights の技術プロファイルを追加します。

オーケストレーション ステップ 1 として `JournyeContextForInsights` を呼び出します

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

オーケストレーション ステップ 2 として `Azure-Insights-SignInRequest` を呼び出し、サインイン/サインアップ要求が受信されたことを追跡します。

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

`SendClaims` オーケストレーション ステップの**直前**に、`Azure-Insights-UserSignup` を呼び出す新しいステップを追加します。 サインアップ/サインイン ジャーニーでサインアップ ボタンがクリックされたときにトリガーされます。

```xml
        <!-- Handles the user clicking the sign up link in the local account sign in page -->
        <OrchestrationStep Order="9" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>newUser</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>newUser</Value>
              <Value>false</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
          </ClaimsExchanges>
```

`SendClaims` オーケストレーション ステップの直後に `Azure-Insights-SignInComplete` を呼び出します。   このステップは、正常に完了したジャーニーを反映します。

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> 新しいオーケストレーション ステップを追加した後、ステップの番号には、1 から N の整数がスキップされずに順番に付け替えられます。


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>手順 5. 修正された拡張ファイルをアップロードし、ポリシーを実行し、Application Insights でイベントを表示する

新しい信頼フレームワーク拡張ファイルを保存してアップロードします。  次に、アプリケーションから証明書利用者ポリシーを呼び出すか、Azure AD B2C インターフェイスで `Run Now` を使用します。  数秒で、Application Insights でイベントを使用できるようになります。

1. Azure Active Directory テナントで Application Insights リソースを開きます。
2. [`USAGE`] サブメニューの [`Events`] をクリックします。
3. [`During`] を [`Last hour`] に、[`By`] を [`3 minutes`] に設定します。  必要に応じて `Refresh` をクリックして結果を表示します。

![Application Insights USAGE-Events Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  <a name="next-steps"></a>次の手順

ニーズに合わせて、新しい要求の種類とイベントをユーザー体験に追加します。  追加の要求リゾルバーを使用することができる要求の一覧を次に示します。

### <a name="culture-specific-claims"></a>カルチャ固有の要求

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>ポリシー固有の要求

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openidconnect-specific-claims"></a>OpenIDConnect 固有の要求

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>OIDC 要求と OAuth2 要求に含まれるプロトコル以外のパラメーター

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

OIDC 要求または OAuth2 要求の一部に含まれているすべてのパラメーター名は、ユーザー体験の要求にマップできます。  また、それをイベントに記録することができます。 たとえば、アプリケーションからの要求には、`app_session`、`loyalty_number`、または `any_string` の名前が指定されたクエリ文字列パラメーターを含めることができます。

アプリケーションからのサンプル要求:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
次に、以下を使用して、入力要求要素を Application Insights イベントに追加して要求を追加できます。
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>その他のシステム要求

一部のシステム要求は、イベントとして記録する前に要求バッグに追加しておく必要があります。 これらの要求を使用する前に、技術プロファイル `SimpleUJContext` をオーケストレーション ステップまたは検証技術プロファイルとして呼び出しておく必要があります。

```xml
<ClaimsProvider>
    <DisplayName>User Journey Context Provider</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="SimpleUJContext">
                <DisplayName>User Journey Context Provide</DisplayName>
                <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="IP-Address" />
                    <OutputClaim ClaimTypeReferenceId="CorrelationId" />
                    <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
                    <OutputClaim ClaimTypeReferenceId="Build" />
                 </OutputClaims>
            </TechnicalProfile>
        </TechnicalProfiles>
</ClaimsProvider>



