---
title: Azure Active Directory B2C の Identity Experience Framework スキーマの整数要求変換の例 | Microsoft Docs
description: Azure Active Directory B2C の Identity Experience Framework スキーマの整数要求変換の例。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 20c109f188acfe37292aa31dfe057ced8b73199b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694742"
---
# <a name="integer-claims-transformations"></a>整数要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C の Identity Experience Framework スキーマの整数要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

long データ型を string データ型に変換します。

| Item | TransformationClaimType | データ型 | メモ |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 文字列に変換する ClaimType。  |
| OutputClaim | outputClaim | string | この ClaimsTransformation が呼び出された後に生成される ClaimType。 |

この例では、値の型が long の `numericUserId` 要求は、値の型が string の `UserId` 要求に変換されます。

```XML
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

