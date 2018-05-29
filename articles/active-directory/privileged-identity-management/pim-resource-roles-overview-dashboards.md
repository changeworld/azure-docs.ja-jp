---
title: '概要: Azure リソース向けの Privileged Identity Management でのアクセス レビューの実行 | Microsoft Docs'
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
ms.openlocfilehash: 61be9873cac462c096599680a6e071e104f3a54c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200622"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>アクセス レビューを実行するためのリソース ダッシュボードの使用

Azure リソース向けの Privileged Identity Management (PIM) でアクセス レビューを実行するためにリソース ダッシュボードを使用できます。 [管理者ビュー] のダッシュボードには、3 つの主要なコンポーネントがあります。

- リソース ロールのアクティブ化のグラフ表示。
- 割り当ての種類ごとにロールの割り当ての分布が表示された 2 つのグラフ。
- 新しいロールの割り当てに関連するデータ領域。

![グラフが表示された [管理者ビュー] のスクリーンショット](media/azure-pim-resource-rbac/rbac-overview-top.png)

![データの一覧が表示された [管理者ビュー] のスクリーンショット](media/azure-pim-resource-rbac/role-settings.png)

過去 7 日間のリソース ロールのアクティブ化がグラフィカルに表示されています。 このデータは、選択したリソースが対象となっていて、特に一般的なロール (所有者、共同作成者、ユーザー アクセス管理者) と、組み合わせられたすべてのロールのアクティブ化を表示しています。

アクティブ化のグラフの右側には 2 つのグラフがあり、ユーザーとグループの両方のロール割り当ての分布が割り当ての種類ごとに表示されています。 グラフのスライスを選択すると、値が割合 (またはその逆) に変更されます。

グラフの下には、過去 30 日間の新しいロール割り当てが行われたユーザーとグループの数と、合計の割り当て数で並べ替えられたロールの一覧 (降順) が表示されます。


