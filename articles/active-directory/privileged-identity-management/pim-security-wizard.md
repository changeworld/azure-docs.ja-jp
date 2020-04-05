---
title: PIM での Azure AD ロール セキュリティ ウィザード - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) を使用して永続的な特権 Azure AD ロールの割り当てを対象的に変換するために使用できるセキュリティ ウィザードについて説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231807"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Privileged Identity Management での Azure AD ロール セキュリティ ウィザード

Azure Active Directory (Azure AD) 組織内で Privileged Identity Management (PIM) を初めて実行するユーザーには、開始のためのウィザードが表示されます。 このウィザードを使用すると、特権 ID のセキュリティ リスクと、Privileged Identity Management を使用してそのリスクを軽減する方法がわかります。 既存のロール割り当てを後で変更する場合は、ウィザードで実行する必要はありません。

## <a name="wizard-overview"></a>ウィザードの概要

組織で Privileged Identity Management の使用を開始する前の段階では、すべてのロール割り当ては永続的な割り当てになっています。そのため、ユーザーが現在その権限を必要としていない場合でも、常にロールは割り当てられている状態です。 ウィザードの最初の手順には、高い権限を持つロールの一覧と、それらのロールに割り当てられたユーザーの数が表示されます。 ロールの中によく知らないものがある場合は、そのロールの詳細を表示して、ユーザーを詳しく確認できます。

ウィザードの 2 番目の手順では、管理者のロール割り当てを変更できます。  

> [!WARNING]
> ここで重要なのは、グローバル管理者が少なくとも 1 人存在し、組織アカウント (Microsoft アカウントではない) を持つ特権ロール管理者が複数存在していることです。 特権ロール管理者が 1 人しかいない場合、そのアカウントが削除されると、組織は Privileged Identity Management を管理できなくなります。
> また、ユーザーに Microsoft アカウント (つまり、Skype、Outlook.com などの Microsoft サービスへのサインインに使用するアカウント) がある場合は、ロールの割り当てを永続的に維持してください。 そのロールのアクティブ化に多要素認証を要求すると、ユーザーはロックアウトされます。

## <a name="run-the-wizard"></a>ウィザードの実行

1. [Azure portal](https://portal.azure.com/) にサインインする

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択し、 **[ウィザード]** を選択します。

    ![[Azure AD ロール] - ウィザードを実行するための 3 つの手順を示すウィザード ページ](./media/pim-security-wizard/wizard-start.png)

1. **[1 特権ロールの検出]** を選択します。

1. 特権ロールの一覧で、どのユーザーが永続的または対象的であるかを確認します。

    ![[特権ロールの検出] - 永続的メンバーと対象的メンバーを示すロール ウィンドウ](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. **[次へ]** を選択して、対象的にするユーザーまたはグループを選択します。

    ![ロールの対象にするメンバーを選択するためのオプションがある [メンバーを対象的に変換] ページ](./media/pim-security-wizard/convert-members-eligible.png)

1. ユーザーまたはグループを選択したら、 **[次へ]** を選択します。

    ![変換される永続ロールが割り当てられているメンバーを示す [変更の確認] ページ](./media/pim-security-wizard/review-changes.png)

1. **[OK]** を選択して、永続的な割り当てを対象に変換します。

    変換が完了したら、通知が表示されます。

    ![変換の状態を示す通知](./media/pim-security-wizard/notification-completion.png)

その他の特権ロールの割り当てを対象的に変換する必要がある場合は、もう一度ウィザードを実行できます。 ウィザードの代わりに Privileged Identity Management インターフェイスを使用する場合は、「[Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management を管理するために、他の管理者にアクセス権を付与する](pim-how-to-give-access-to-pim.md)
