---
title: カスタム ポリシーの整数要求変換の例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C の Identity Experience Framework (IEF) スキーマの整数要求変換の例。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/10/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: dc637e6369a1dbaaa9a25ad5d7b91b7b5d95ffd6
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131036401"
---
# <a name="integer-claims-transformations"></a>整数要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) の Identity Experience Framework スキーマの整数要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="adjustnumber"></a>AdjustNumber

数値要求を増減し、新しい要求を返します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | INT | 増減する数が含まれる要求の種類。 `inputClaim` 要求値が null の場合、既定値の 0 が使用されます。 |
| InputParameter | 演算子 | string | 指定できる値は `INCREMENT`(既定値) または`DECREMENT`です。|
| OutputClaim | outputClaim | INT | この要求変換が呼び出された後に生成される要求の種類。 |

この要求変換を使用して、数値要求値を増減します。 

```xml
<ClaimsTransformation Id="UpdateSteps" TransformationMethod="AdjustNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="steps" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="INCREMENT" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="steps" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>例 1

- 入力要求:
    - **inputClaim**: 1
- 入力パラメーター:
    - **Operator**: INCREMENT
- 出力要求:
    - **outputClaim**: 2

### <a name="example-2"></a>例 2

- 入力要求:
    - **inputClaim**: NULL
- 入力パラメーター:
    - **Operator**: INCREMENT
- 出力要求:
    - **outputClaim**: 1


## <a name="assertnumber"></a>AssertNumber

数値と比較して数値要求が大きいか、小さいか、等しいか、等しくないかを判断します。 

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | INT | 2 番目の数値と比較して大きいか、小さいか、等しいか、または等しくないかを比較する 1 番目の数値要求。 Null 値の場合は例外がスローされます。 |
| InputParameter | CompareToValue | INT | 1 番目の数値と比較して大きいか、小さいか、等しいか、または等しくないかを比較する 2 番目の数値。 |
| InputParameter | 演算子 | string | 指定できる値: `LESSTHAN`、`GREATERTHAN`、`GREATERTHANOREQUAL`、`LESSTHANOREQUAL`、`EQUAL`、`NOTEQUAL`。 |
| InputParameter | throwError | boolean | 比較結果が `true` の場合に、このアサーションでエラーをスローする必要があるかどうかを指定します。 指定できる値は `true`(既定値) または`false`です。 <br />&nbsp;<br />`true` に設定すると (アサーション モード)、比較結果が `true` の場合、例外がスローされます。 `false` に設定すると (評価モード)、結果は、値が `true` または `false` の新しいブール型の要求の種類になります。| 
| OutputClaim | outputClaim | boolean | `ThrowError` が `false` に設定されている場合、この出力要求には、比較結果に従って、`true` または `false` が含まれます。 |

### <a name="assertion-mode"></a>アサーション モード

`throwError` 入力パラメーターが `true` (既定値) のときは、**AssertNumber** 要求変換は常に、[セルフアサート技術プロファイル](self-asserted-technical-profile.md)によって呼び出される [検証技術プロファイル](validation-technical-profile.md) から実行されます。 

**AssertNumberError** セルフアサート技術プロファイル メタデータでは、技術プロファイルによってユーザーに表示されるエラー メッセージが制御されます。 エラー メッセージは、[ローカライズ](localization-string-ids.md#claims-transformations-error-messages)できます。

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="AssertNumberError">You've reached the maximum logon attempts</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

アサーション モードで要求変換を呼び出す方法の詳細については、「[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal)」、「[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue)」、「[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan)」の各要求変換を参照してください。

### <a name="assertion-mode-example"></a>アサーション モードの例

次の例では、試行回数が 5 回を超えるとアサートします。 要求変換では、比較結果に従ってエラーがスローされます。 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```

- 入力要求:
    - **inputClaim**: 10
- 入力パラメーター:
    - **Operator**: GREATERTHAN
    - **CompareToValue**: 5
    - **throwError**: true
- 結果:エラーがスローされます

### <a name="evaluation-mode-example"></a>評価モードの例

次の例では、試行回数が 5 回を超えるかどうかが評価されます。 出力要求には、比較結果に従ったブール値が含まれます。 要求変換では、エラーはスローされません。 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="attemptsCountExceeded" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- 入力要求:
    - **inputClaim**: 10
- 入力パラメーター:
    - **Operator**: GREATERTHAN
    - **CompareToValue**: 5
    - **throwError**: false
- 出力要求:
    - **outputClaim**: true


## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

long データ型を string データ型に変換します。

| Item | TransformationClaimType | データ型 | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 文字列に変換する要求の種類。 |
| OutputClaim | outputClaim | string | この要求変換が呼び出された後に生成される要求の種類。 |

この例では、値の型が long の `numericUserId` 要求は、値の型が string の `UserId` 要求に変換されます。

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>例

- 入力要求:
    - **inputClaim**:12334 (long)
- 出力要求:
    - **outputClaim**:"12334" (string)

