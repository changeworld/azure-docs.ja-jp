---
title: Azure AD サインイン ページで Microsoft アカウントが受け入れられるか | Microsoft Docs
description: サインイン中にユーザー名の参照が画面のメッセージに反映されるしくみ
services: active-directory
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93bd814075bf4fc2603bc4dd55caec1b7e5543f5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985487"
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