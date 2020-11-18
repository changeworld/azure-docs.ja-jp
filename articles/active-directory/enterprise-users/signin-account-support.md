---
title: Azure AD サインイン ページで Microsoft アカウントが受け入れられるか | Microsoft Docs
description: サインイン中にユーザー名の参照が画面のメッセージに反映されるしくみ
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2a3c2257311f208ae704a54f1369e10b2ff160b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646850"
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