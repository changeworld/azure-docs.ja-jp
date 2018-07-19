---
title: Azure Active Directory で管理者ロールのメンバーとロールのアクセス許可を表示する | Microsoft Docs
description: ポータルで、Azure AD 管理者ロールのメンバーを表示して管理できるようになりました。 ロールの割り当てを頻繁に管理するユーザーの場合。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/10/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 5a42f48e85eea95211b36e0c08dcb0edb4928a20
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38489924"
---
# <a name="view-members-and-descriptions-of-administrator-roles-in-azure-active-directory"></a>Azure Active Directory で管理者ロールのメンバーとその説明を表示する

Azure Active Directory ポータルで、管理者ロールのすべてのメンバーを表示して管理できるようになりました。 ロールの割り当てを頻繁に管理する場合は、このエクスペリエンスが望ましいでしょう。 "これらのロールで何ができるか" については、Azure AD 管理者ロールごとのアクセス許可の詳細な一覧を参照してください。

自分のアクセス許可も簡単に確認することができます。 **[ロール]** をクリックすれば、自分に割り当てられているすべてのアクティブ ロールの一覧を示すユーザー ページにすばやくアクセスできます。 各ロールの右側にある省略記号をクリックすると、ロールの詳細説明が開きます。

![Azure AD ポータルのロールの一覧](./media/directory-manage-roles-portal/role-list.png)

ロール全体を選択すると、割り当てられているメンバーの一覧が表示されます。 追加の管理機能の **[Manage in PIM]\(PIM で管理\)** を選択できます。 特権ロール管理者は、"永続的な" (ロールで常にアクティブ) 割り当てを、"資格のある" (昇格された場合にのみ、ロールに存在) に変更することができます。 PIM がない場合でも、**[Manage in PIM]\(PIM で管理\)** を選択して、試用版にサインアップできます。 Privileged Identity Management には、[Azure AD Premium P2 ライセンス プラン](../privileged-identity-management/subscription-requirements.md)が必要です。

![管理者ロールのメンバーの一覧](./media/directory-manage-roles-portal/member-list.png)

グローバル管理者または特権ロール管理者の場合は、簡単にメンバーを追加または削除したり、一覧をフィルター処理したり、メンバーを選択してユーザー ページに移動し、割り当てられているアクティブ ロールを表示することができます。 

## <a name="detailed-role-permissions-in-the-portal"></a>ポータルの詳細なロールのアクセス許可

ロールのメンバーを表示しているときに、**[説明]** を選択すると、ロール割り当てで付与されたアクセス許可の完全な一覧が表示されます。 ページには、ディレクトリ ロールの管理に役立つ関連ドキュメントへのリンクが含まれます。

![管理者ロールのアクセス許可の一覧](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>次の手順

* [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)を介してご意見をお寄せください。
* ロールと管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](directory-assign-admin-roles.md)に関するページを参照してください。
* 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)に関するページを参照してください。
