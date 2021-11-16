---
title: 認証方法ポリシーに Microsoft によって管理される設定を使用する - Azure Active Directory
description: Microsoft Authenticator に Microsoft によって管理される設定を使用する方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/03/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4134998bd3c7967d5ec151b900d67364373a2659
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565780"
---
# <a name="how-to-use-microsoft-managed-settings---authentication-methods-policy"></a>Microsoft によって管理される設定を使用する方法 - 認証方法ポリシー

IT 管理者は、認証方法ポリシー設定を **[有効]** または **[無効]** のいずれかに設定する以外に、一部を **Microsoft が管理する設定** にすることができます。 **Microsoft が管理する設定** では、Azure AD はその設定を有効または無効にできます。 

## <a name="settings-that-can-be-microsoft-managed"></a>Microsoft が管理することにできる設定

次の表では、Microsoft が管理することにできる設定と、それが有効か無効かを一覧表示しています。 

| 設定         | 構成 |
|-----------------|---------------|
| [登録キャンペーン](how-to-nudge-authenticator-app.md)  | 無効      |
| 数値の一致        | 無効      |
| 追加コンテキスト  | 無効      |

## <a name="next-steps"></a>次の手順

[Azure Active Directory の認証方法 - Microsoft Authenticator アプリ](concept-authentication-authenticator-app.md)
