---
title: オーストラリア国内のお客様の識別データの保存 - Azure AD
description: Azure Active Directory によってオーストラリア国内のお客様の識別関連データがどこに保存されるかについて説明します。
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbe4befa824990c2f8b2627e7709913f564c0486
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371481"
---
# <a name="identity-data-storage-for-australian-customers-in-azure-active-directory"></a>Azure Active Directory でのオーストラリア国内のお客様の識別データの保存

Office 365 や Azure などの Microsoft Online サービスをサブスクライブしている場合、識別データは Azure AD により、組織によって提供されたアドレスに基づく地理的な場所に格納されます。 ご自身の顧客識別データがどこに保存されるかについては、Microsoft セキュリティ センターの「[お客様のデータの場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)」セクションを参照してください。

> [!NOTE]
> 顧客識別データは、Azure AD と統合されるサービスとアプリケーションによってアクセスされます。 使用する各サービスとアプリケーションを評価して、その特定のサービスまたはアプリケーションによる顧客識別データの処理方法と、それらが会社のデータ ストレージ要件を満たしているかどうかを確認してください。 Microsoft サービスのデータ保存場所の詳細については、Microsoft セキュリティ センターのデータの「お客様のデータの場所保存」セクションを参照してください。

オーストラリア国内の住所を提供したお客様の場合、Azure AD では、次のサービスの識別データはオーストラリア国内のデータセンターに保持されます。 
- Azure AD Directory の管理 
- 認証

その他のすべての Azure AD サービスでは、お客様のデータは、グローバル データセンターに格納されます。 サービスのデータセンターを特定するには、「[Azure Active Directory – お客様のデータの場所](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)」を参照してください。

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure 多要素認証 (MFA)

MFA では、顧客識別データは、グローバル データセンターに格納されます。 クラウドベースの Azure MFA と Azure MFA Server によって収集されて保存されるユーザー情報の詳細については、[Azure Multi-Factor Authentication でのユーザー データの収集](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
上記の機能の詳細については、以下の記事を参照してください。
- [Multi-Factor Authentication とは](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
