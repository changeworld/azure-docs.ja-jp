---
title: カスタム ポリシーでの電話番号要求変換
titleSuffix: Azure AD B2C
description: Azure AD B2C での電話番号要求変換に関するカスタム ポリシー リファレンス。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c43e3386886456eed0c58fefd0fb1212795db66c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475041"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Azure AD B2C で電話番号要求変換を定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) で Identity Experience Framework スキーマの電話番号要求変換を使用するためのリファレンスと例を示します。 要求変換全般の詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

この要求によって、電話番号の形式が検証されます。 有効な形式である場合は、Azure AD B2C によって使用される標準形式に変更されます。 指定された電話番号が有効な形式でない場合は、エラー メッセージが返されます。

| アイテム | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | 変換元である文字列型の要求。 |
| OutputClaim | outputClaim | string | この要求変換の結果。 |

**ConvertStringToPhoneNumberClaim** 要求変換は、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)または[表示コントロール](display-controls.md)によって呼び出される[検証技術プロファイル](validation-technical-profile.md)から常に実行する必要があります。 **UserMessageIfClaimsTransformationInvalidPhoneNumber** セルフアサート技術プロファイル メタデータにより、ユーザーに表示されるエラー メッセージが制御されます。

![エラー メッセージの実行パスの図](./media/phone-authentication/assert-execution.png)

この要求変換を使用すると、指定された文字列要求が有効な電話番号であることが保証されます。 そうでない場合は、エラー メッセージがスローされます。 次の例では、**phoneString** ClaimType が実際に有効な電話番号であることを確認してから、標準の Azure AD B2C 形式で電話番号を返します。 そうでない場合は、エラー メッセージがスローされます。

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

この要求変換を含む検証技術プロファイルを呼び出すセルフアサート技術プロファイルで、エラー メッセージを定義できます。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example"></a>例

- 入力要求:
  - **inputClaim**: +1 (123) 456-7890
- 出力要求:
  - **outputClaim**: +11234567890

## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

これにより、入力要求から国番号と国内番号が抽出され、指定した電話番号が有効でない場合は、必要に応じて例外がスローされます。

| アイテム | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | string | 電話番号の文字列要求。 電話番号は国際対応形式で、先頭に "+" と国番号を使用する必要があります。 |
| InputParameter | throwExceptionOnFailure | boolean | [省略可能] 電話番号が有効でない場合に例外をスローするかどうかを示すパラメーター。 既定値は false です。 |
| InputParameter | countryCodeType | string | [省略可能] 出力要求の国番号の種類を示すパラメーター。 使用できる値は、**CallingCode** (国の国際呼び出しコード。例: +1)、または **ISO3166** (2 文字の ISO-3166 国コード) です。 |
| OutputClaim | nationalNumber | string | 電話番号の国内番号に対する文字列要求。 |
| OutputClaim | countryCode | string | 電話番号の国番号に対する文字列要求。 |


**GetNationalNumberAndCountryCodeFromPhoneNumberString** 要求変換が、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)または[表示コントロール アクション](display-controls.md#display-control-actions)によって呼び出される[検証技術プロファイル](validation-technical-profile.md)から実行された場合、セルフアサート技術プロファイルの **UserMessageIfPhoneNumberParseFailure** メタデータによって、ユーザーに表示されるエラー メッセージが制御されます。

![エラー メッセージの実行パスの図](./media/phone-authentication/assert-execution.png)

この要求変換を使用すると、完全な電話番号を国番号と国内番号に分割できます。 指定された電話番号が有効でない場合にエラー メッセージをスローするように選択できます。

次の例では、電話番号を国内番号と国番号に分割しようとしています。 電話番号が有効な場合、電話番号は国内番号によって上書きされます。 電話番号が有効でない場合、例外はスローされず、電話番号の元の値がそのまま使用されます。

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

この要求変換を含む検証技術プロファイルを呼び出すセルフアサート技術プロファイルで、エラー メッセージを定義できます。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>例 1

- 入力要求:
  - **phoneNumber**: +49 (123) 456-7890
- 入力パラメーター:
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: ISO3166
- 出力要求:
  - **nationalNumber**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>例 2

- 入力要求:
  - **phoneNumber**: +49 (123) 456-7890
- 入力パラメーター
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: CallingCode
- 出力要求:
  - **nationalNumber**: 1234567890
  - **countryCode**: +49
