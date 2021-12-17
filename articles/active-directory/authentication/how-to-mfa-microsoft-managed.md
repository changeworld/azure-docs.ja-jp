---
title: 認証方法ポリシーに Microsoft によって管理される設定を使用する - Azure Active Directory
description: Microsoft Authenticator に Microsoft によって管理される設定を使用する方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d63cd4795bfa94e827125c630ae7bb8c49e072ff
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707836"
---
# <a name="how-to-use-microsoft-managed-settings---authentication-methods-policy"></a>Microsoft によって管理される設定を使用する方法 - 認証方法ポリシー

<!---what API--->

IT 管理者は、認証方法ポリシー設定を **[有効]** または **[無効]** のいずれかに設定する以外に、一部を **Microsoft が管理する設定** にすることができます。 **Microsoft が管理する設定** では、Azure AD はその設定を有効または無効にできます。 

Azure AD で設定を管理できるようにするオプションは、Microsoft が既定で機能を有効または無効にすることを許可する、組織にとって便利な方法です。 組織は、Microsoft を信頼して、機能を既定でいつ有効にする必要があるかを管理することで、自社のセキュリティ態勢をより簡単に改善できます。 **Microsoft が管理する** (Graph API では *default* という名前) と設定を構成することで、IT 管理者は Microsoft を信頼して、明示的に無効にしていないセキュリティ機能を有効にできます。 

## <a name="settings-that-can-be-microsoft-managed"></a>Microsoft が管理することにできる設定

次の表では、Microsoft が管理することにできる設定と、それが有効か無効かを一覧表示しています。 

| 設定                                                                                                                         | 構成 |
|---------------------------------------------------------------------------------------------------------------------------------|---------------|
| [登録キャンペーン](how-to-mfa-registration-campaign.md)                                                      | 無効      |
| [数値の一致](how-to-mfa-number-match.md)                                             | 無効      |
| [Microsoft Authenticator 通知での追加コンテキスト](how-to-mfa-additional-context.md) | 無効      |

## <a name="next-steps"></a>次の手順

[Azure Active Directory の認証方法 - Microsoft Authenticator アプリ](concept-authentication-authenticator-app.md)
