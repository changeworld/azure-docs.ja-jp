---
title: Privileged Identity Management を使用した Azure リソースの探索と管理 | Microsoft Docs
description: PIM を使用して Azure リソースを保護する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: c5b26c01028e2a5746132939a2058cacdcad859f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622104"
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Privileged Identity Management を使用した Azure リソースの探索と管理

Azure Active Directory (Azure AD) の Privileged Identity Management (PIM) を使用しているときの Azure リソースの探索および管理方法を説明します。 この情報は、既に PIM を使用して管理者のリソースを保護している組織と、運用リソースをセキュリティで保護しようとしているサブスクリプションの所有者に役立ちます。

PIM を Azure リソース用に初めて設定するときは、PIM を使って保護するリソース探索し、選択する必要があります。 PIM を使って管理できるリソースの数に制限はありません。 ただし、最も重要な (運用) リソースから始めることをお勧めします。

> [!NOTE]
> 検索して選択できるのは、PIM を使用して管理するサブスクリプションのリソースのみです。 PIM でサブスクリプションを管理するときは、サブスクリプション内の子リソースを管理することもできます。

## <a name="discover-resources"></a>リソースを探索する

Azure Portal で、**[Privileged Identity Management]** ウィンドウに移動します。 左側のメニューの **[管理]** セクションで、**[Azure リソース]** を選択します。

![[Privileged Identity Management] の [Azure リソース] ウィンドウ](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Azure リソース用に PIM を初めて使用する場合は、最初に探索を実行して、管理するリソースを見つけます。 **[リソースの探索]** ウィンドウで **[リソースの探索]** ボタンを選択して、探索を開始します。

![[リソースの探索] ウィンドウ](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Azure リソースが組織内の他のリソースまたはディレクトリ管理者によって PIM を使用して既に管理されている場合、またはリソースの資格のあるロールに自分が割り当てられている場合は、"**リソースを探索します。続行するには有資格ロールの割り当てをアクティブ化してください**" というメッセージがリスト ビューに表示されます。 

![[Privileged Identity Manager] の [Azure リソース] ウィンドウの [リソースの探索] ボタン](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

上部のメニューまたはウィンドウの中央の **[リソースの探索]** ボタンを選択すると、管理できるサブスクリプションの一覧が表示されます。 強調表示されているサブスクリプションは、既に PIM で保護されています。

> [!NOTE]
> 別のリソース管理者が PIM 設定を削除するのを防ぐため、サブスクリプションを管理対象に設定した後は、そのサブスクリプションを非管理対象にすることはできません。

![[Azure リソース] の [探索] ウィンドウ](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

**[リソース]** 列で、PIM で保護するサブスクリプションの上にマウスを置きます。 次に、リソース名の左側にあるチェック ボックスをオンにします。 一度に複数のサブスクリプションを選択できます。

![[Azure リソース] の [探索] ウィンドウのリソースの一覧](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

オンボード プロセスを開始するには、上部のメニューで **[リソースの管理]** を選択します。

![[Azure リソース] の [探索] ウィンドウの [リソースの管理] ボタン](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

これで、選択したリソースが PIM によって管理されます。 探索画面を閉じるには、右上隅にある **[X]** を選択します。PIM 設定の管理とメンバーの割り当てを開始するには、**[Privileged Identity Management] の [Azure リソース]** ウィンドウの上部のメニューで、**[更新]** ボタンを選択します。

![[Privileged Identity Management] の [Azure リソース] ウィンドウの上部のメニューの [更新] ボタン](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>次の手順

- [ロール設定の構成](pim-resource-roles-configure-role-settings.md)
- [PIM でロールを割り当てる](pim-resource-roles-assign-roles.md)
