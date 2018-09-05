---
title: PIM での電子メール通知 - Azure | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) でのメール通知について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 07/24/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 1a1635e0ba16bdc0fa6dc90aa0e1a417f43e04ac
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190178"
---
# <a name="email-notifications-in-pim"></a>PIM での電子メール通知

Azure AD Privileged Identity Management (PIM) で重要なイベントが発生すると、関連する管理者またはユーザーに電子メール通知が送信されます。 たとえば、PIM は、次のイベントに対して電子メールを送信します。

- 特権ロールのアクティブ化が承認待ちのとき
- 特権ロールのアクティブ化要求が承認されたとき
- 特権ロールがアクティブ化されたとき
- 特権ロールが割り当てられたとき
- Azure AD PIM が有効になったとき

2018 年 7 月より、PIM を通じて送信される電子メール通知の送信者のメール アドレスが新しくなり、ビジュアル デザインも新しくなります。 この更新は、Azure AD 用の PIM と Azure リソース用の PIM の両方に影響します。 以前に電子メール通知がトリガーされていたすべてのイベントは、引き続き電子メールが送信されます。 一部のメールでは内容が更新されて、提供する情報が増える予定です。

## <a name="sender-email-address"></a>送信者のメール アドレス

2018 年 7 月より、電子メール通知のメール アドレスは次のアドレスに変わります。

- メール アドレス: **azure-noreply@microsoft.com**
- 表示名: Microsoft Azure

以前の電子メール通知のメール アドレスは、次のアドレスでした。

- メール アドレス:  **azureadnotifications@microsoft.com**
- 表示名: Microsoft Azure AD Notification Service

## <a name="email-subject-line"></a>メールの件名

2018 年 7 月末より、Azure AD と Azure リソース ロールの両方で件名に **PIM** プレフィックスが付きます。 次に例を示します。

- PIM: Alain Charon にバックアップ リーダー ロールが永続的に割り当てられました。

## <a name="pim-emails-for-azure-ad-roles"></a>Azure AD ロール向けの PIM 電子メール

2018 年 7 月末より、Azure AD ロール向けの PIM 電子メール通知のデザインが変わります。 ユーザーが架空の Contoso 組織の特権ロールをアクティブ化したときに送信される電子メールの例を次に示します。

![Azure AD ロール向けの新しい PIM 電子メール](./media/pim-email-notifications/email-directory-new.png)

以前は、ユーザーが特権ロールをアクティブ化すると、次のような電子メールが送信されていました。

![Azure AD ロール向けの古い PIM 電子メール](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>Azure リソース ロール向けの PIM 電子メール

2018 年 7 月末より、Azure リソース ロール向けの PIM 電子メール通知のデザインが変わります。 ユーザーに架空の Contoso 組織の特権ロールが割り当てられたときに送信される電子メールの例を次に示します。

![Azure リソース ロール向けの新しい PIM 電子メール](./media/pim-email-notifications/email-resources-new.png)

以前は、ユーザーに特権ロールが割り当てられると、次のような電子メールが送信されていました。

![Azure リソース ロール向けの古い PIM 電子メール](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ディレクトリ ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [PIM で Azure AD ディレクトリ ロールに対する要求を承認または拒否する](azure-ad-pim-approval-workflow.md)
