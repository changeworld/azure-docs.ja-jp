---
title: PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを実行する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソースおよび Azure AD ロールのアクセスをレビューする方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31a2d1b9e15f795da4931ffbff88f5222aad69c2
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669589"
---
# <a name="perform-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを実行する

Privileged Identity Management (PIM) を使用すると、企業が Azure Active Directory (AD) や他の Microsoft Online Services (Microsoft 365 や Microsoft Intune など) 内のリソースへの特権アクセスを管理する方法が簡略化されます。 この記事の手順に従って、ロールへのアクセスのレビューを実行します。

既に管理者ロールに割り当てられているユーザーは、組織の特権ロール管理者から、自分の業務にそのロールがまだ必要であるかどうかを定期的に確認するよう求められることがあります。 リンクが記載された電子メールが届く場合もあれば、直接 [Azure portal](https://portal.azure.com) にアクセスして開始することもできます。

特権ロール管理者またはグローバル管理者としてアクセス レビューに関心がある場合は、 [アクセス レビューを開始する方法](pim-create-azure-ad-roles-and-resource-roles-review.md)に関するページで詳細を確認できます。

## <a name="approve-or-deny-access"></a>アクセスの承認または拒否

ユーザーがまだロールへのアクセスを必要とするかどうかに基づいて、アクセスを承認または拒否することができます。 ロールに含めたままにする場合は **[承認]** を選択し、アクセスが不要な場合は **[拒否]** を選択します。 レビューが終了し、管理者が結果を適用するまで、ユーザーの割り当ての状態は変更されません。 拒否された特定のユーザーに結果を適用できない一般的なシナリオには、次のようなものがあります。

- **同期されたオンプレミスの Windows AD グループのメンバーのレビュー**: グループがオンプレミスの Windows AD から同期されている場合、グループを Azure AD で管理することはできません。そのため、メンバーシップを変更することはできません。
- **入れ子になったグループが割り当てられているロールのレビュー**: 入れ子になったグループのメンバーシップを持つユーザーの場合、アクセス レビューを行っても入れ子になったグループのメンバーシップは削除されないため、レビュー対象のロールへのアクセスが維持されます。
- **ユーザーが見つからないか、その他のエラー**: これらの事象が発生した場合も、適用結果がサポートされないことがあります。

アクセス レビューを検索して完了するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure Active Directory]** を選択し、**[Privileged Identity Management]** を開きます。
1. **[アクセスのレビュー]** を選択します。 保留中のアクセス レビューがある場合は、アクセス レビューのページに表示されます。

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png" alt-text="Azure AD ロールの [アクセスのレビュー] ブレードが選択された Privileged Identity Management アプリケーションのスクリーンショット" lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png":::

1. 完了するレビューを選択します。
1. **[承認]** または **[拒否]** を選択します。 **[理由の指定] ボックス** に、必要であれば、業務上の正当な理由を入力します。

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png" alt-text="Azure AD ロールのアクセス レビューが選択されている Privileged Identity Management アプリケーションのスクリーンショット。" lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png":::

## <a name="next-steps"></a>次のステップ

- [PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを作成する](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを完了する](pim-complete-azure-ad-roles-and-resource-roles-review.md)
