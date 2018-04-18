---
title: Azure リソース向けの Privileged Identity Management でアクセス レビューを実行する方法 | Microsoft Docs
description: このドキュメントでは、Azure リソース向けの PIM でアクセス レビューを実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: f1bcf114b997c3056016b84cafc28253ea1af28e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="resource-dashboards"></a>リソースのダッシュボード

[管理者ビュー] のダッシュボードには、4 つの主要なコンポーネントが含まれます。 過去 7 日間のリソース ロールのアクティブ化がグラフィカルに表示されています。 このデータは、選択したリソースが対象となっていて、特に一般的なロール (所有者、共同作成者、ユーザー アクセス管理者) と、組み合わせられたすべてのロールのアクティブ化を表示しています。

アクティブ化のグラフの右側には 2 つのグラフがあり、ユーザーとグループの両方のロール割り当ての分布が割り当ての種類ごとに表示されています。 グラフのスライスを選択すると、値が割合 (またはその逆) に変更されます。

![](media/azure-pim-resource-rbac/rbac-overview-top.png)

グラフの下には、過去 30 日間の新しいロール割り当てが行われたユーザーとグループの数と、合計の割り当て数で並べ替えられたロールの一覧 (降順) が表示されます。

![](media/azure-pim-resource-rbac/role-settings.png)
