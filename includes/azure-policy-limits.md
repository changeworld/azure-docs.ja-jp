---
title: インクルード ファイル
description: インクルード ファイル
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: 1c63d79c82f667fbd7cfc9a62a741eab275757dd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131571034"
---
Azure Policy では、オブジェクトの種類ごとに最大数があります。 定義について、_Scope_ というエントリは [管理グループ](../articles/governance/management-groups/overview.md)またはサブスクリプションを意味します。 割り当てと除外の場合、_スコープ_ というエントリは [管理グループ](../articles/governance/management-groups/overview.md)、サブスクリプション、リソース グループ、または個々のリソースを意味します。

| Where | 対象 | 最大数 |
|---|---|---|
| Scope | ポリシーの定義 | 500 |
| Scope | イニシアチブ定義 | 200 |
| Tenant | イニシアチブ定義 | 2,500 |
| Scope | ポリシーとイニシアティブの割り当て | 200 |
| スコープ | 適用除外 | 1000 |
| ポリシー定義 | パラメーター | 20 |
| イニシアチブ定義 | ポリシー | 1000 |
| イニシアチブ定義 | パラメーター | 300 |
| ポリシーとイニシアティブの割り当て | 除外 (notScopes) | 400 |
| ポリシー規則 | 入れ子になった条件 | 512 |
| 修復タスク | リソース | 500 |
