---
title: インクルード ファイル
description: インクルード ファイル
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: e9faea1d5913a19dfdeff662e26992529dc1b22d
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84466949"
---
Azure Policy では、オブジェクトの種類ごとに最大数があります。 _Scope_ というエントリは、サブスクリプションまたは[管理グループ](../articles/governance/management-groups/overview.md)のいずれかを意味します。

| Where | 対象 | 最大数 |
|---|---|---|
| Scope | ポリシーの定義 | 500 |
| Scope | イニシアチブ定義 | 100 |
| Tenant | イニシアチブ定義 | 2,500 |
| Scope | ポリシーとイニシアティブの割り当て | 100 |
| ポリシー定義 | パラメーター | 20 |
| イニシアチブ定義 | ポリシー | 100 |
| イニシアチブ定義 | パラメーター | 100 |
| ポリシーとイニシアティブの割り当て | 除外 (notScopes) | 400 |
| ポリシー規則 | 入れ子になった条件 | 512 |
| 修復タスク | リソース | 500 |
