---
title: カスタム ポリシーで OAuth2 カスタム エラーの技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C のカスタム ポリシーで OAuth2 カスタム エラーの技術プロファイルを定義します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/26/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 1126ce99c660860f7ee0f4abc9afcb9ed5cd1afc
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036248"
---
# <a name="define-an-oauth2-custom-error-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで OAuth2 カスタム エラーの技術プロファイルを定義する

この記事では、Azure Active Directory B2C (Azure AD B2C) で OAuth2 カスタム エラーを処理する方法について説明します。 ポリシーで何らかのロジックに問題がある場合は、この技術プロファイルを使用します。 技術プロファイルによって、OAuth2 または OpenId Connect 証明書利用者アプリケーションにエラーが返されます。

カスタム OAuth2 エラー メッセージを処理するには:

1. Auth2 エラーの技術プロファイルを定義します。
1. エラーコードとエラー メッセージのクレームを設定します。
1. ユーザー体験から、OAuth2 エラーの技術プロファイルを呼び出します。

## <a name="oauth2-error"></a>OAuth2 エラー

エラーでは、次のデータが返されます。

- **error** - `access_denied`
- **error_description** - `AAD_Custom_<errorCode>: <errorMessage>` という規則を使用したエラー メッセージ。
- **Correlation ID** - Azure AD B2C 関連付け ID。
- **Timestamp** - エラーのタイムスタンプ。

次の例では、 https://jwt.ms アプリに返されるカスタム エラー メッセージを示しています。

```http
https://jwt.ms/#error=access_denied&error_description=AAD_Custom_1234%3a+My+custom+error+message%0d%0aCorrelation+ID%3a+233bf9bd-747a-4800-9062-6236f3f69a47%0d%0aTimestamp%3a+2021-03-25+14%3a01%3a23Z%0d%0a
```
  
## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `None` に設定する必要があります。 **OutputTokenFormat** 要素を `OAuth2Error` に設定します。

次の例は、`ReturnOAuth2Error` 用の技術プロファイルを示しています。

```xml
<!--
 <ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ReturnOAuth2Error">
        <DisplayName>Return OAuth2 error</DisplayName>
        <Protocol Name="None" />
        <OutputTokenFormat>OAuth2Error</OutputTokenFormat>
        <CryptographicKeys>
          <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
        </CryptographicKeys>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="errorCode" />
          <InputClaim ClaimTypeReferenceId="errorMessage" />
        </InputClaims>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="input-claims"></a>入力クレーム

**Inputclaims** 要素には、OAuth2 エラーを返すために必要なクレームの一覧が含まれています。 

| ClaimReferenceId | 必須 | 説明 |
| --------- | -------- | ----------- |
| errorCode | はい | エラー コード。 | 
| errorMessage | はい | エラー メッセージ。 |

## <a name="cryptographic-keys"></a>暗号化キー

CryptographicKeys 要素には、次のキーが含まれています。

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| issuer_secret | はい  | X509 証明書 (RSA キー セット)。 「[カスタム ポリシーの概要](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)」で構成した `B2C_1A_TokenSigningKeyContainer` キーを使用します。|
|

## <a name="invoke-the-technical-profile"></a>技術プロファイルを呼び出す

OAuth2 エラーの技術プロファイルは、ユーザー体験またはサブ体験から呼び出すことができます。 [オーケストレーション ステップ](userjourneys.md#orchestrationsteps)の種類を `SendClaims` に設定し、OAuth2 エラーの技術プロファイルへの参照を設定します。

ユーザー体験またはサブ体験に既に別の `SendClaims` オーケストレーション ステップがある場合は、`DefaultCpimIssuerTechnicalProfileReferenceId` 属性をトークン発行者の技術プロファイルに設定します。

次に例を示します。

-  ユーザー体験 `SignUpOrSignIn-Custom` は、`DefaultCpimIssuerTechnicalProfileReferenceId` をトークン発行者の技術プロファイルに設定します`JwtIssuer`。 
- 8 番目のオーケストレーション ステップでは、`errorCode` が存在するかどうかを確認しています。 「はい」の場合は、`ReturnOAuth2Error` 技術プロファイルを呼び出してエラーを返します。
- `errorCode` が存在しない場合は、9 番目のオーケストレーション ステップによってトークンが発行されます。

```xml
<UserJourney Id="SignUpOrSignIn-Custom" DefaultCpimIssuerTechnicalProfileReferenceId="JwtIssuer">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="ReturnOAuth2Error">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>errorCode</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
    </OrchestrationStep>

    <OrchestrationStep Order="9" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />

  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="next-steps"></a>次のステップ

[UserJourneys](userjourneys.md) の詳細