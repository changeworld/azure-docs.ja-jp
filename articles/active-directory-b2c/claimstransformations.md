---
title: ClaimsTransformations - Azure Active Directory B2C
description: Azure Active Directory B2C の Identity Experience Framework スキーマの ClaimsTransformations 要素の定義。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: d1b4aa49d6aecdf06ec445dd036be05805ffb128
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007992"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsTransformations** 要素には、[カスタム ポリシー](custom-policy-overview.md)の一部としてユーザー体験に使用できる要求変換関数のリストが含まれています。 要求変換により、所定の要求が別の要求に変換されます。 要求変換で、文字列コレクションに項目を追加したり、文字列の大文字と小文字を変更したりする変換メソッドを指定します。

ユーザー体験で使用できる要求変換関数のリストを含めるには、ClaimsTransformations XML 要素が、ポリシーの BuildingBlocks セクションで宣言されている必要があります。

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

**ClaimsTransformation** 要素には、次の属性が含まれています。

| 属性 |必須 | 説明 |
| --------- |-------- | ----------- |
| Id |はい | 要求変換を一意に識別するために使用される識別子。 識別子は、ポリシー内の他の XML 要素から参照されます。 |
| TransformationMethod | はい | 要求変換で使用する変換メソッド。 各要求変換は、独自の値を持ちます。 使用可能な値の完全なリストについては、[要求変換の参照](#claims-transformations-reference)を確認してください。 |

## <a name="claimstransformation"></a>ClaimsTransformation

**ClaimsTransformation** 要素には、次の要素が含まれています。

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| 要素 | 発生回数 | 説明 |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | 要求変換への入力として取得される要求の種類を指定する **InputClaim** 要素のリスト。 これらの各要素には、ポリシー内の ClaimsSchema セクションに既に定義されている ClaimType への参照が含まれています。 |
| InputParameters | 0:1 | 要求変換への入力として提供される **InputParameter** 要素のリスト。
| OutputClaims | 0:1 | ClaimsTransformation が呼び出された後に生成される要求の種類を指定する **OutputClaim** 要素のリスト。 これらの各要素には、ClaimsSchema セクションに既に定義されている ClaimType への参照が含まれています。 |

### <a name="inputclaims"></a>InputClaims

**InputClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 予想される入力要求の種類。 |

#### <a name="inputclaim"></a>InputClaim

**InputClaim** 要素には、次の属性が含まれています。

| 属性 |必須 | Description |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |はい | ポリシー内の ClaimsSchema セクションに既に定義されている ClaimType への参照。 |
| TransformationClaimType |はい | 変換要求の種類を参照する識別子。 各要求変換は、独自の値を持ちます。 使用可能な値の完全なリストについては、[要求変換の参照](#claims-transformations-reference)を確認してください。 |

### <a name="inputparameters"></a>InputParameters

**InputParameters** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | 想定される入力パラメーター。 |

#### <a name="inputparameter"></a>InputParameter

| 属性 | 必須 |説明 |
| --------- | ----------- |----------- |
| Id | はい | 要求変換メソッドのパラメーターへの参照である識別子。 各要求変換メソッドは、独自の値を持ちます。 使用可能な値の完全なリストについては、要求変換の表を参照してください。 |
| DataType | はい | カスタム ポリシー XML スキーマの DataType エミュレーションに従った、文字列、ブール値、Int、または DateTime などの、パラメーターのデータ型。 この型は、算術演算を正しく行うために使用されます。 各要求変換は、独自の値を持ちます。 使用可能な値の完全なリストについては、[要求変換の参照](#claims-transformations-reference)を確認してください。 |
| Value | はい | 変換に逐語的に渡される値。 一部の値は任意ですが、一部の値は要求変換メソッドから選択します。 |

### <a name="outputclaims"></a>OutputClaims

**OutputClaims** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | 予想される出力要求の種類。 |

#### <a name="outputclaim"></a>OutputClaim

**OutputClaim** 要素には、次の属性が含まれています。

| 属性 |必須 | Description |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | はい | ポリシー内の ClaimsSchema セクションに既に定義されている ClaimType への参照。
| TransformationClaimType | はい | 変換要求の種類を参照する識別子。 各要求変換は、独自の値を持ちます。 使用可能な値の完全なリストについては、[要求変換の参照](#claims-transformations-reference)を確認してください。 |

要求変換に使用される入力と出力の要求は、区別できる必要があります。 同じ入力要求を出力要求として使用することはできません。

## <a name="example"></a>例

例えば、ユーザーが同意したサービス使用条件の最後のバージョンを保存できます。 サービス使用条件を更新するときに、新しいバージョンに同意することをユーザーに求めることができます。 次の例では、**HasTOSVersionChanged** 要求変換によって、**TOSVersion** 要求の値と **LastTOSAcceptedVersion** 要求の値が比較され、ブール **TOSVersionChanged** 要求が返されます。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>要求の変換の参照

要求変換の例については、次の参照ページを確認してください。

- [Boolean](boolean-transformations.md)
- [Date](date-transformations.md)
- [整数](integer-transformations.md)
- [JSON](json-transformations.md)
- [電話番号](phone-number-claims-transformations.md)
- [全般](general-transformations.md)
- [ソーシャル アカウント](social-transformations.md)
- [String](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

