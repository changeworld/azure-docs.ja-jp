---
title: Azure Active Directory B2C の Identity Experience Framework スキーマの整数要求変換の例 | Microsoft Docs
description: Azure Active Directory B2C の Identity Experience Framework スキーマの整数要求変換の例。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 988e25b6a5ef3f99ae7df9076a40e06b403bb029
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "44383031"
---
# <a name="integer-claims-transformations"></a>整数要求変換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory (Azure AD) B2C の Identity Experience Framework スキーマの整数要求変換の使用例を示します。 詳細については、「[ClaimsTransformations](claimstransformations.md)」を参照してください。

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

long データ型を string データ型に変換します。

| 項目 | TransformationClaimType | データ型 | メモ |
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
    - **inputClaim**: 12334 (long)
- 出力要求: 
    - **outputClaim**: "12334" (string)

