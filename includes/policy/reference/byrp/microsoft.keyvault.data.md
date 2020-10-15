---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4602b1b9602a191fba8583a01ac203c236836ca2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859616"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[証明書は、指定の統合された証明機関によって発行される必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e826246-c976-48f6-b03e-619bb92b3d82) |Digicert または GlobalSign など、キー コンテナーで証明書を発行できる、Azure と統合された証明機関を指定して組織のコンプライアンス要件を管理します。 |audit、deny、disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_SupportedCAs.json) |
|[証明書は、指定の統合されていない証明機関によって発行される必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa22f4a40-01d3-4c7d-8071-da157eeff341) |キー コンテナーで証明書を発行できるカスタムまたは内部の証明機関を指定して、組織のコンプライアンス要件を管理します。 |audit、deny、disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_CustomCAs.json) |
|[証明書には、指定された有効期間アクション トリガーが必要である](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12ef42cb-9903-4e39-9c26-422d29570417) |証明書の有効期間アクションを、有効期間の特定の割合でトリガーするか、有効期限から指定した日数前にトリガーするかを指定して、組織のコンプライアンス要件を管理します。 |audit、deny、disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_LifetimeAction.json) |
|[証明書には最長有効期間を指定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |キー コンテナー内での証明書の最長有効期間を指定して、組織のコンプライアンス要件を管理します。 |audit、deny、disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[証明書は、指定された日数内に期限が切れてはいけない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff772fb64-8e40-40ad-87bc-7706e1949427) |指定した日数内に期限が切れる証明書を、有効期限が切れる前に組織が証明書をローテーションするための十分な時間を確保できるように管理します。 |audit、deny、disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Expiry_ByDays.json) |
|[証明書は、許可されたキーの種類を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1151cede-290b-4ba0-8b38-0ad145ac888f) |証明書に許可されるキーの種類を制限して、組織のコンプライアンス要件を管理します。 |audit、deny、disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_AllowedKeyTypes.json) |
|[楕円曲線暗号を使用する証明書には、許可されている曲線名が必要である](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd78111f-4953-4367-9fd5-7e08808b54bf) |キー コンテナーに格納される ECC 証明書に対して許可されている楕円曲線名を管理します。 詳細については、[https://aka.ms/akvpolicy](https://aka.ms/akvpolicy)をご覧ください。 |audit、deny、disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_EC_AllowedCurveNames.json) |
|[RSA 暗号を使用する証明書に、キーの最小サイズを指定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcee51871-e572-4576-855c-047c820360f0) |キー コンテナーに格納される RSA 証明書の最小キー サイズを指定して、組織のコンプライアンス要件を管理します。 |audit、deny、disabled |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_RSA_MinimumKeySize.json) |
