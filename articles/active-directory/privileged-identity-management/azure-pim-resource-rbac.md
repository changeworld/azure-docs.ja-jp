---
title: PIM で Azure リソース ロールを割り当てられているユーザーを表示する | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールを割り当てられているユーザーを表示します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: ce7c96d92938c4e3b4cc0b53271df48350083754
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465233"
---
# <a name="view-who-has-azure-resource-roles-in-pim"></a>PIM で Azure リソース ロールを割り当てられているユーザーを表示する

組織内の Azure リソースに対するアクセス権を Azure Active Directory Privileged Identity Management (PIM) で管理、制御、監視できます。 対象には、サブスクリプション、リソース グループ、さらに仮想マシンも含まれます。 Azure のロールベースのアクセス制御 (RBAC) 機能を利用する Azure portal 内のすべてのリソースで、Azure AD PIM のセキュリティおよびライフサイクル管理機能を利用できます。 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>Azure リソース向けの PIM はリソース管理者に便利

- 管理対象の Azure リソースに おいて、ロールが割り当てられているユーザーとグループを確認する
- サブスクリプション、リソース グループなどのリソース管理のために、オンデマンドで "必要な時に必要な分だけ" のアクセスを有効にする
- 新しい期限付きの割り当て設定により、割り当てられたユーザー/グループのリソース アクセスを自動的に期限切れにする
- クイック タスクまたはオンコール スケジュールのために、一時的なリソース アクセスを割り当てる
- 組み込みまたはカスタム ロールのリソース アクセスのために、多要素認証を適用する 
- ユーザーのアクティブなセッション中にリソース アクティビティと相関関係のあるリソース アクセスに関するレポートを取得する
- 新しいユーザーまたはグループがリソース アクセスの割り当てを受けたときや、そのようなユーザーまたはグループが資格のある割り当てをアクティブ化するときにアラートを取得する

## <a name="view-activation-and-azure-resource-activity"></a>アクティブ化と Azure リソースのアクティビティを表示する

各リソースに対して特定のユーザーが実行した操作を確認する必要がある場合は、指定したアクティブ化期間に関連付けられている Azure リソース アクティビティを表示できます (適格なユーザーに限ります)。 最初に、[メンバー] ビューまたは特定のロールのメンバー一覧からユーザーを選択します。 結果には Azure リソースに対する日別のユーザーの操作と、同一期間の最近のロールのアクティブ化がグラフィカルに表示されます。

![](media/azure-pim-resource-rbac/user-details.png)

特定のロールのアクティブ化を選択すると、ロールのアクティブ化の詳細とそのユーザーがアクティブな間に発生した、対応する Azure リソース アクティビティが表示されます。

![](media/azure-pim-resource-rbac/audits.png)

## <a name="review-who-has-access-in-a-subscription"></a>サブスクリプションでアクセス権を持つユーザーをレビューする

サブスクリプション内のロールの割り当てをレビューするには、左側のナビゲーションから [メンバー] タブを選択するかロールを選択して、メンバーをレビューする特定のロールを選択します。 

既存のアクセス レビューを表示するには操作バーから [レビュー] を選択し、新しいレビューを作成するには [追加] を選択します。

![](media/azure-pim-resource-rbac/owner.png)

アクセス レビューの詳細については、[こちら](pim-how-to-perform-security-review.md)を参照してください

>[!NOTE]
現時点で、レビューはサブスクリプションのリソースでのみサポートされています。

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールを割り当てる](pim-resource-roles-assign-roles.md)
- [PIM で Azure リソース ロールに対する要求を承認または拒否する](pim-resource-roles-approval-workflow.md)
- [Azure の組み込みロール](../../role-based-access-control/built-in-roles.md)
