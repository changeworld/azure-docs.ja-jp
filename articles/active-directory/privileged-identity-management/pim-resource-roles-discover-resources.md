---
title: Azure リソース向けの Privileged Identity Management - Azure リソースの探索と管理を行う | Microsoft Docs
description: Azure リソースを保護する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>Azure リソースを探索して管理する

組織が既に Azure AD PIM を使用してディレクトリで管理者を保護しているか、自分自身が運用リソースをセキュリティで保護することを検討しているサブスクリプション所有者である場合は、この記事が最適です。

Azure リソース向けの PIM の初回の有効化では、PIM を使用して保護するリソースを探索して選択する必要があります。 PIM を使用して管理できるリソースの数に制限はありませんが、最も重要な (運用) リソースから始めることをお勧めします。

> [!Note]
> 管理するために検索して選択できるのは、サブスクリプション リソースだけです。 PIM でサブスクリプションを管理することを選択すると、すべての子リソースの管理も有効になります。

## <a name="discover-resources"></a>リソースを探索する

Azure AD PIM に移動し、左側のナビゲーション メニューの [管理] セクションで [Azure リソース] を選択します。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Azure リソースに対して PIM を初めて使用する場合は、探索を実行して、管理するリソースを見つける必要があります。
画面の中央にある [リソースの探索] ボタンをクリックして、探索を開始します。

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Azure リソースが組織内の他のリソースまたはディレクトリ管理者によって PIM を使用して既に管理されている場合、または自分がリソースに対して有資格ロールが割り当てられている場合は、"リソースを探索します。続行するには有資格ロールの割り当てをアクティブ化してください" という意味のメッセージがリスト ビューに表示されます。 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

操作バーまたは画面中央の [リソースの探索] ボタンを選択すると、管理可能なサブスクリプションの一覧が表示されます。 この時点で強調表示されているサブスクリプションがあれば、それらのサブスクリプションは PIM によって保護されています。

> [!Note]
> 別のリソース管理者が PIM 設定を削除するのを防ぐために、管理の対象になっているサブスクリプションの管理を解除することはできません。

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

PIM で保護するサブスクリプションで、行の左端のボックスをオンにします。 一度に複数のサブスクリプションを選択できます。

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

オンボード プロセスを開始するには、画面上部のバーの [リソースの管理] ボタンを選択します。

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

これで、選択したリソースが PIM によって管理されます。 ページの右上隅の [X] を使用して探索画面を閉じます。[Azure リソースの管理] 画面で、画面上部のバーの [更新] をクリックして、PIM 設定とメンバーの割り当てを開始します。

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>次の手順

[ロール設定の構成](pim-resource-roles-configure-role-settings.md)

[PIM でロールを割り当てる](pim-resource-roles-assign-roles.md)
