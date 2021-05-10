---
title: オーストラリアとニュージーランドの顧客データの保存 - Azure AD
description: Azure Active Directory によってオーストラリアとニュージーランド国内の顧客関連データがどこに保存されるかについて説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55504cf893081b7de4cced7afc43b46127f5bdfd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478728"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory でのオーストラリアとニュージーランドの顧客データの保存 

Azure Active Directory (Azure AD) の顧客データは、お客様が Microsoft オンライン サービスにサインアップしたときに指定した国に基づいて地理的な場所に保存されます。 Microsoft オンライン サービスには、Microsoft 365 や Azure があります。 

Azure AD およびその他の Microsoft サービスのデータが配置される場所の詳細については、Microsoft トラスト センターの[お客様のデータの場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)に関するセクションを参照してください。

2020 年 2 月 26 日から、Microsoft は、オーストラリアまたはニュージーランドの請求先住所を含む新しいテナントについて、Azure AD の顧客データをオーストラリアのデータセンター内に格納し始めました。

また、一部の Azure AD 機能では、オーストラリアでの顧客データの格納がまだサポートされていません。 特定の機能の情報については、[Azure AD データ マップ](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)に関するページを参照してください。 たとえば、Microsoft Azure AD Multi-Factor Authentication の場合、顧客データは米国内に格納され、グローバルに処理されます。 「[Azure AD Multi-Factor Authentication のデータの保存場所と顧客データ](../authentication/concept-mfa-data-residency.md)」を参照してください。

> [!NOTE]
> Azure AD と統合される Microsoft の製品およびサービス、ならびにサードパーティのアプリケーションは、顧客データにアクセスすることができます。 使用する製品、サービス、アプリケーションをそれぞれ評価して、その特定の製品、サービス、アプリケーションによる顧客データの処理方法を確認し、それらが会社のデータ ストレージ要件を満たしているかどうかを判断してください。 Microsoft サービスのデータ保存場所の詳細については、Microsoft Trust Center の[データの保存場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)に関するセクションを参照してください。
