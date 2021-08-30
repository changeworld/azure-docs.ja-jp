---
title: Azure サブスクリプション管理者を追加または変更する
description: Azure ロールベースのアクセス制御 (Azure RBAC) を使用して Azure サブスクリプション管理者を追加または変更する方法について説明します。
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 06/27/2021
ms.author: banders
ms.openlocfilehash: b4bd2615dd99145bd75fd7c1095056961d3b2d80
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988357"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Azure サブスクリプション管理者を追加または変更する


Azure リソースへのアクセスを管理するには、適切な管理者ロールが必要です。 Azure には、組み込みロールを選択できる [Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md) (Azure RBAC) と呼ばれる承認システムがあります。 管理グループ、サブスクリプション、リソース グループなどのさまざまなスコープでこれらのロールを割り当てることができます。 既定では、新しい Azure サブスクリプションを作成するユーザーは、サブスクリプションへの管理アクセス権を他のユーザーに割り当てることができます。

この記事では、サブスクリプション スコープで Azure RBAC を使用してユーザーの管理者ロールを追加または変更する方法について説明します。

Microsoft では、Azure RBAC を使用してリソースへのアクセスを管理することをお勧めしています。 ただし、まだクラシック デプロイ モデルを使用し、[Azure サービス管理 PowerShell モジュール](/powershell/module/servicemanagement/azure.service)を使用してクラシック リソースを管理している場合は、従来の管理者を使用する必要があります。

> [!TIP]
> クラシック リソースの管理に Azure portal だけを使用している場合は、従来の管理者を使用する必要はありません。

詳細については、「[Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について](../../azure-resource-manager/management/deployment-models.md)」および「[Azure classic subscription administrators (Azure の従来のサブスクリプション管理者)](../../role-based-access-control/classic-administrators.md)」を参照してください。

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>サブスクリプション管理者を割り当てる

ユーザーを Azure サブスクリプションの管理者にするには、既存の管理者が、サブスクリプションのスコープで[所有者](../../role-based-access-control/built-in-roles.md#owner)ロール (Azure ロール) を割り当てます。 これにより、他のユーザーへアクセス権を委任する権限を含め、サブスクリプションにあるすべてのリソースへのフル アクセスがユーザーに付与されます。 次の手順は、他のロールの割り当てと同じです。

サブスクリプションのアカウント管理者が不明な場合は、次の手順で確認します。

1. [Azure portal で [サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)を開きます。
1. 確認するサブスクリプションを選択し、 **[設定]** を調べます。
1. **[プロパティ]** を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。

### <a name="to-assign-a-user-as-an-administrator"></a>ユーザーを管理者に割り当てるには

- 所有者ロールをサブスクリプション スコープでユーザーに割り当てます。  
     詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次のステップ

* [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)
* [Azure での各種ロールについて](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Azure Active Directory での管理者ロールのアクセス許可](../../active-directory/roles/permissions-reference.md)