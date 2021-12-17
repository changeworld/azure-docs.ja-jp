---
title: Azure Active Directory による NIST Authenticator Assurance Levels の達成
description: 概要
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: ''
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500b22546089335563fd12953414aa74612cedf3
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110537072"
---
# <a name="configure-azure-active-directory-to-meet-nist-authenticator-assurance-levels"></a>NIST Authenticator Assurance Levels を満たすように Azure Active Directory を構成する

連邦政府機関向けのサービスを提供することは、満たす必要がある標準の数と複雑さによって複雑になります。 クラウド サービス プロバイダー (CSP) または連邦機関は、関連するすべての標準に準拠していることを確認する責任があります。 Azure と Azure Active Directory (Azure AD) により、Microsoft の認定を使用し、さらに特定の要件を構成できることで、これが容易になります。

Azure は、この執筆時点で 90 以上のコンプライアンス認証を認定しています。 詳細については、[「クラウドを信頼する」](https://azure.microsoft.com/overview/trusted-cloud/)を参照してください。

## <a name="why-meet-nist-standards"></a>NIST 標準を満たす理由 

米国国立標準技術研究所 (NIST) は、ID ソリューションを実装する米国政府機関の技術要件を策定します。 連邦政府機関と協力している組織も、これらの要件を満たしている必要があります。 NIST の ID 要件の詳細については、[「特殊出版 800-63 Revision 3](https://pages.nist.gov/800-63-3/sp800-63-3.html) (nist SP 800-63-3)」を参照してください。

NIST SP 800-63 は以下でも参照されています
* Electronic Prescription of Controlled Substances [ECPS](https://deadiversion.usdoj.gov/ecomm/e_rx/) プログラム。 
* [Financial Industry Regulatory Authority (FINRA) の要件](https://www.finra.org/rules-guidance)。 
* 医療、防衛、およびその他の業界団体では、ID およびアクセス管理要件のベースラインとして NIST SP 800-63-3 がよく使われています。

NIST のガイドラインは他の標準で参照されており、中でも注目すべきなのは CSP 向けの Federal Risk and Authorization Management Program (FedRAMP) です。 Azure は FedRAMP High Impact 認定を受けています。 

NIST のデジタル ID ガイドラインでは、従業員、パートナー、サプライヤー、顧客、市民などのユーザーのプルーフィングと認証を扱っています。 

NIST SP 800-63-3 デジタル ID ガイドラインは、次の 3 つの領域にわたります。

* [SP 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html) では、登録およプルーフィングを扱っています。

* [SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) では認証およびライフサイクル管理を扱っています。

* [SP 800-63C](https://pages.nist.gov/800-63-3/sp800-63c.html) ではフェデレーションおよびアサーションを扱っています。

各領域で、保証レベルが策定されています。 この記事では、Azure AD およびその他の Microsoft ソリューションを使用して、NIST SP 800-63B の Authenticator Assurance Level (AAL) を達成するためのガイダンスを提供します。

## <a name="next-steps"></a>次の手順 

[AAL について確認する](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST 認証システムの種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を達成する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を達成する](nist-authenticator-assurance-level-3.md) 
