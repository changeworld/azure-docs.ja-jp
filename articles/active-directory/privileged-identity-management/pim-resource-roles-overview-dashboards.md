---
title: PIM でのアクセス レビュー用のリソース ダッシュボード - Azure AD |Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) でアクセス レビューを実行するためにリソース ダッシュボードを使用する方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: markwahl-msft
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
ms.openlocfilehash: d87ff7dc27dea5b25995dcfbd7f969a6d01fec56
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669266"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Privileged Identity Management でリソース ダッシュボードを使用してアクセス レビューを実行する

Privileged Identity Management (PIM) でアクセス レビューを実行するためにリソース ダッシュボードを使用できます。 Azure Active Directory (Azure AD) の [管理者ビュー] ダッシュボードには、次の 3 つの主要なコンポーネントがあります。

- リソース ロールのアクティブ化のグラフ表示
- 割り当ての種類ごとにロールの割り当ての分布を表示するグラフ
- 新しいロールの割り当てに関する情報を含むデータ領域

![グラフが表示された [管理者ビュー] のスクリーンショット](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![データの一覧が表示された [管理者ビュー] ダッシュボードのスクリーンショット](media/pim-resource-roles-overview-dashboards/role-settings.png)

過去 7 日間のリソース ロールのアクティブ化がグラフィカルに表示されています。 このデータは、選択したリソースが対象となっていて、特に一般的なロール (所有者、共同作成者、ユーザー アクセス管理者) と、組み合わせられたすべてのロールのアクティブ化を表示しています。

アクティブ化のグラフの片側には 2 つのグラフがあり、ユーザーとグループの両方のロール割り当ての分布が割り当ての種類ごとに表示されています。 グラフのスライスを選択すると、値が割合 (またはその逆) に変更されます。

グラフの下には、過去 30 日間の新しいロール割り当てが行われたユーザーとグループの数と、合計の割り当て数の降順で並べ替えられたロールが一覧表示されます。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールのアクセス レビューを開始する](./pim-create-azure-ad-roles-and-resource-roles-review.md)