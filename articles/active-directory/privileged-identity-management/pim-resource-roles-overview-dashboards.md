---
title: PIM でアクセス レビューを実行するためのリソース ダッシュボードの使用 - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) でアクセス レビューを実行するためにリソース ダッシュボードを使用する方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5404d1b85821458aedef64b72ae635ea49aa1ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602477"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>PIM でアクセス レビューを実行するためのリソース ダッシュボードの使用

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) でアクセス レビューを実行するためにリソース ダッシュボードを使用できます。 [管理者ビュー] のダッシュボードには、3 つの主要なコンポーネントがあります。

- リソース ロールのアクティブ化のグラフ表示。
- 割り当ての種類ごとにロールの割り当ての分布が表示された 2 つのグラフ。
- 新しいロールの割り当てに関連するデータ領域。

![グラフが表示された [管理者ビュー] のスクリーンショット](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![データの一覧が表示された [管理者ビュー] ダッシュボードのスクリーンショット](media/pim-resource-roles-overview-dashboards/role-settings.png)

過去 7 日間のリソース ロールのアクティブ化がグラフィカルに表示されています。 このデータは、選択したリソースが対象となっていて、特に一般的なロール (所有者、共同作成者、ユーザー アクセス管理者) と、組み合わせられたすべてのロールのアクティブ化を表示しています。

アクティブ化のグラフの右側には 2 つのグラフがあり、ユーザーとグループの両方のロール割り当ての分布が割り当ての種類ごとに表示されています。 グラフのスライスを選択すると、値が割合 (またはその逆) に変更されます。

グラフの下には、過去 30 日間の新しいロール割り当てが行われたユーザーとグループの数と、合計の割り当て数で並べ替えられたロールの一覧 (降順) が表示されます。

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールのアクセス レビューを開始する](pim-resource-roles-start-access-review.md) 
