---
title: カスタム ポリシーでの条件付きアクセス技術プロファイル
titleSuffix: Azure AD B2C
description: Azure AD B2C での条件付きアクセス技術プロファイルのカスタム ポリシー リファレンス。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/18/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: d44ae627279131004bdac2f755eb26b59e375381
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007954"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C カスタム ポリシーで条件付きアクセス技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) 条件付きアクセスは、シグナルをまとめ、決定を行い、組織のポリシーを適用するために Azure AD B2C によって使用されるツールです。 ポリシー条件を使用してリスク評価を自動化すると、危険なサインインがただちに特定され、修復またはブロックされます。

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 **handler** 属性には、Azure AD B2C により使用されるプロトコル ハンドラー アセンブリの完全修飾名が含まれる必要があります。

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

条件付きアクセス技術プロファイルの例を次に示します。

```xml
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>条件付きアクセスの評価

毎回のサインインで、すべてのポリシーが Azure AD B2C によって評価され、すべての要件が満たされていることを確認したうえで、ユーザー アクセスが許可されます。 "アクセスのブロック" は、他のすべての構成設定よりも優先されます。 条件付きアクセス技術プロファイルの **[評価]** モードでは、ローカル アカウントを使用したサインイン時に Azure AD B2C によって収集されたシグナルが評価されます。 条件付きアクセス技術プロファイルの結果として、条件付きアクセスの評価に基づく一連の要求が生成されます。 Azure AD B2C は、これらの要求を次のオーケストレーション手順で使用し、ユーザーをブロックしたり多要素認証でユーザーをチャレンジするなどのアクションを実行します。 このモードに対しては、次のオプションを構成できます。

### <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| OperationType | はい | **Evaluation** である必要があります。  |

### <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、条件付きアクセスに送信する要求の一覧が含まれます。 要求の名前を、条件付きアクセス技術プロファイルで定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | データ型 | 説明 |
| --------- | -------- | ----------- |----------- |
| UserId | はい | string | サインインするユーザーの識別子。 |
| AuthenticationMethodsUsed | はい |stringCollection | ユーザーがサインインに使用したメソッドの一覧。 使用できる値: `Password` および`OneTimePasscode`。 |
| IsFederated | はい |boolean | フェデレーション アカウントを使用してユーザーがサインインしたかどうかを示します。 値は `false` である必要があります。 |
| IsMfaRegistered | はい |boolean | ユーザーが多要素認証用に電話番号を既に登録しているかどうかを示します。 |

**InputClaimsTransformations** 要素には、入力要求を条件付きアクセス サービスに送信する前に、その入力要求を変更するか新しい入力要求を生成するために使用される、**InputClaimsTransformation** 要素のコレクションが含まれる場合があります。

### <a name="output-claims"></a>出力クレーム

**OutputClaims** 要素には、ConditionalAccessProtocolProvider によって生成される要求の一覧が含まれています。 要求の名前を以下に定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | データ型 | 説明 |
| --------- | -------- | ----------- |----------- |
| 課題 | はい |stringCollection | 特定された脅威を修復するためのアクションの一覧。 指定できる値: `block`、`mfa`、`chg_pwd`。 |
| MultiConditionalAccessStatus | はい | stringCollection | 条件付きアクセスの評価の状態。  |

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

### <a name="example-evaluation"></a>例:評価

サインインの脅威を評価するために使用される条件付きアクセス技術プロファイルの例を次に示します。

```xml
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>修復

条件付きアクセス技術プロファイルの **修復** モードは、サインイン時に特定された脅威が修復されたことを Azure AD B2C に通知します。 修復モードに対しては、次のオプションを構成できます。

### <a name="metadata"></a>Metadata

| 属性 | 必須 | 説明 |
| --------- | -------- | ----------- |
| OperationType | はい | **Remediation** である必要があります。  |

### <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、条件付きアクセスに送信する要求の一覧が含まれます。 要求の名前を、条件付きアクセス技術プロファイルで定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | データ型 | 説明 |
| --------- | -------- | ----------- |----------- |
| ChallengesSatisfied | はい | stringCollection| 評価モードからの戻り値として特定された脅威を修復するための満たされたチャレンジの一覧。チャレンジ要求。|

**InputClaimsTransformations** 要素には、条件付きアクセス サービスを呼び出す前に、その入力要求を変更するか新しい入力要求を生成するために使用される、**InputClaimsTransformation** 要素のコレクションが含まれる場合があります。

### <a name="output-claims"></a>出力クレーム

条件付きアクセス プロトコル プロバイダーでは **OutputClaims** は返されないため、出力要求を指定する必要はありません。 ただし、`DefaultValue` 属性を設定している限り、条件付きアクセス プロトコル プロバイダーによって返されない要求を含めることができます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

### <a name="example-remediation"></a>例:Remediation

サインインの脅威を修復するために使用される条件付きアクセス技術プロファイルの例を次に示します。

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory B2C のユーザー フローに条件付きアクセスを追加する](conditional-access-user-flow.md)方法について確認します。
