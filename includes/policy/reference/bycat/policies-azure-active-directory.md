---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b599d32c2d51aa95a69d2f6c322aad4050cd2a6c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103330"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Active Directory Domain Services managed domains should use TLS 1.2 only mode (Azure Active Directory Domain Services マネージド ドメインで TLS 1.2 専用モードを使用する必要がある)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |マネージド ドメインに対して TLS 1.2 専用モードを使用します。 既定では、Azure AD Domain Services で、NTLM v1 や TLS v1 などの暗号を使用できます。 これらの暗号は、一部のレガシ アプリケーションで必要になる場合がありますが、脆弱と見なされており、不要であれば無効にできます。 TLS 1.2 専用モードが有効になっている場合、TLS 1.2 を使用せずに要求を行うクライアントはすべて失敗します。 詳細については、[https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](../../../../articles/active-directory-domain-services/secure-your-domain.md) をご覧ください。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |