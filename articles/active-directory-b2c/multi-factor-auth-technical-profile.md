---
title: カスタム ポリシーでの Azure MFA の技術プロファイル
titleSuffix: Azure AD B2C
description: Azure AD B2C での Azure Multi-Factor Authentication (MFA) 技術プロファイルのカスタム ポリシー リファレンス。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a8aaea6b2afb4d89e6e667edba0eeba2f4ddcca8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475057"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C カスタム ポリシーで Azure MFA 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、Azure Multi-Factor Authentication (MFA) を使用して電話番号を確認するためのサポートが提供されています。 この技術プロファイルを使用して、コードを生成し、電話番号に送信してから、コードを確認します。

Azure MFA 技術プロファイルからは、エラー メッセージが返される場合もあります。 **検証技術プロファイル**を使用して、Azure MFA との統合を設計できます。 検証技術プロファイルでは、Azure MFA サービスが呼び出されます。 検証技術プロファイルでは、ユーザー体験を続ける前に、ユーザーが入力したデータを検証します。 検証技術プロファイルにより、エラー メッセージが自己宣言されたページに表示されます。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 **handler** 属性には、Azure AD B2C により使用されるプロトコル ハンドラー アセンブリの完全修飾名が含まれる必要があります。

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

次の例では、Azure MFA の技術プロファイルを示します。

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>SMS を送信する

この技術プロファイルの最初のモードでは、コードが生成されて送信されます。 このモードに対しては、次のオプションを構成できます。

### <a name="input-claims"></a>入力要求

**InputClaims** 要素には、Azure MFA に送信する要求の一覧が含まれます。 要求の名前を、MFA 技術プロファイルで定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | [説明] |
| --------- | -------- | ----------- |
| userPrincipalName | はい | 電話番号を所有しているユーザーの識別子。 |
| phoneNumber | はい | SMS コードを送信する先の電話番号。 |
| companyName | いいえ |SMS の会社名。 指定されていない場合は、アプリケーションの名前が使用されます。 |
| locale | いいえ | SMS のロケール。 指定されていない場合は、ユーザーのブラウザーのロケールが使用されます。 |

**InputClaimsTransformations** 要素には、Azure MFA サービスに送信する前に、入力要求の変更または新しい入力要求の生成に使用される、**InputClaimsTransformation** 要素のコレクションが含まれる場合があります。

### <a name="output-claims"></a>出力要求

Azure MFA プロトコル プロバイダーでは **OutputClaims** は返されないため、出力要求を指定する必要はありません。 ただし、`DefaultValue` 属性を設定している限り、Azure MFA ID プロバイダーによって返されない要求を含めることができます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

### <a name="metadata"></a>メタデータ

| Attribute | 必須 | [説明] |
| --------- | -------- | ----------- |
| 操作 | はい | **OneWaySMS** になっている必要があります。  |
| UserMessageIfInvalidFormat | いいえ | 指定された電話番号が有効な電話番号ではない場合のカスタム エラー メッセージ |
| UserMessageIfCouldntSendSms | いいえ | 指定された電話番号で SMS が受け付けられない場合のカスタム エラー メッセージ |
| UserMessageIfServerError | いいえ | サーバーで内部エラーが発生した場合のカスタム エラー メッセージ |

### <a name="return-an-error-message"></a>エラー メッセージを返す

「[メタデータ](#metadata)」で説明されているように、さまざまなエラー ケースに対してユーザーに表示するエラー メッセージをカスタマイズできます。 ロケールにプレフィックスを付けることで、これらのメッセージをさらにローカライズできます。 次に例を示します。

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>例: SMS を送信する

次の例では、SMS でコードを送信するために使用される Azure MFA 技術プロファイルを示します。

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">OneWaySMS</Item>
    </Metadata>
    <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
        <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
    </InputClaimsTransformations>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>コードの確認

この技術プロファイルの 2 番目のモードでは、コードが検証されます。 このモードに対しては、次のオプションを構成できます。

### <a name="input-claims"></a>入力要求

**InputClaims** 要素には、Azure MFA に送信する要求の一覧が含まれます。 要求の名前を、MFA 技術プロファイルで定義されている名前にマップすることもできます。

| ClaimReferenceId | 必須 | [説明] |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| はい | 以前にコードを送信するために使用したのと同じ電話番号。 また、電話検証セッションを探すためにも使用されます。 |
| verificationCode  | はい | 確認のためにユーザーによって指定された確認コード |

**InputClaimsTransformations** 要素には、Azure MFA サービスの呼び出しの前に、入力要求の変更または新しい入力要求の生成に使用される、**InputClaimsTransformation** 要素のコレクションが含まれる場合があります。

### <a name="output-claims"></a>出力要求

Azure MFA プロトコル プロバイダーでは **OutputClaims** は返されないため、出力要求を指定する必要はありません。 ただし、`DefaultValue` 属性を設定している限り、Azure MFA ID プロバイダーによって返されない要求を含めることができます。

**OutputClaimsTransformations** 要素には、出力要求を修正したり新しい要求を生成するために使用される、**OutputClaimsTransformation** 要素のコレクションが含まれている場合があります。

## <a name="metadata"></a>メタデータ

| Attribute | 必須 | [説明] |
| --------- | -------- | ----------- |
| 操作 | はい | **Verify** になっている必要があります |
| UserMessageIfInvalidFormat | いいえ | 指定された電話番号が有効な電話番号ではない場合のカスタム エラー メッセージ |
| UserMessageIfWrongCodeEntered | いいえ | 検証のために入力されたコードが間違っている場合のカスタム エラー メッセージ |
| UserMessageIfMaxAllowedCodeRetryReached | いいえ | ユーザーによる確認コードの試行回数が多すぎる場合のカスタム エラー メッセージ |
| UserMessageIfThrottled | いいえ | ユーザーがスロットルされている場合のカスタム エラー メッセージ |
| UserMessageIfServerError | いいえ | サーバーで内部エラーが発生した場合のカスタム エラー メッセージ |

### <a name="return-an-error-message"></a>エラー メッセージを返す

「[メタデータ](#metadata)」で説明されているように、さまざまなエラー ケースに対してユーザーに表示するエラー メッセージをカスタマイズできます。 ロケールにプレフィックスを付けることで、これらのメッセージをさらにローカライズできます。 次に例を示します。

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>例: コードを検証する

次の例では、コードの検証に使用される Azure MFA の技術プロファイルを示します。

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
