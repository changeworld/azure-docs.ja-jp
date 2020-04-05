---
title: Azure AD サインイン ページで Microsoft アカウントが受け入れられるか | Microsoft Docs
description: サインイン中にユーザー名の参照が画面のメッセージに反映されるしくみ
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024282"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Azure Active Directory での Microsoft アカウントのサインイン オプション

Azure Active Directory (Azure AD) の Microsoft 365 サインイン ページでは、職場または学校アカウントと Microsoft アカウントがサポートされていますが、ユーザーの状況に応じて、それはどちらか一方または両方になる可能性があります。 たとえば、Azure AD サインイン ページでは、次がサポートされています。

* 両方の種類のアカウントからのサインインを受け入れるアプリ
* ゲストを受け入れる組織

## <a name="identification"></a>識別
ユーザー名フィールドのヒント テキストを見ると、組織が使用しているサインイン ページで Microsoft アカウントがサポートされているかどうかを判断できます。 ヒント テキストに "メール、電話、または Skype" と表示されている場合は、サインイン ページで Microsoft アカウントがサポートされています。

![アカウント サインイン ページの違い](./media/signin-account-support/ui-prompt.png)

[追加のサインイン オプションは個人用の Microsoft アカウントに対してのみ機能します](https://azure.microsoft.com/updates/microsoft-account-signin-options/ )が、職場または学校アカウント リソースへのサインインには使用できません。

## <a name="next-steps"></a>次のステップ

[サインイン ブランドのカスタマイズ](../fundamentals/add-custom-domain.md)