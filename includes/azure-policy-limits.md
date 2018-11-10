---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0a54dfdb810ea578c1e7c8fcc7ca0343e72164ae
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2018
ms.locfileid: "50964603"
---
Azure Policy では、オブジェクトの種類ごとに最大数があります。 _Scope_ というエントリは、サブスクリプションまたは[管理グループ](../articles/governance/management-groups/overview.md)のいずれかを意味します。

| Where | 対象 | 最大数 |
|---|---|---|
| Scope (スコープ) | ポリシーの定義 | 250 |
| Scope (スコープ) | イニシアチブの定義 | 100 |
| Tenant | イニシアチブの定義 | 1,000 |
| Scope (スコープ) | ポリシー/イニシアティブ割り当て | 100 |
| ポリシーの定義 | parameters | 20 |
| イニシアチブの定義 | ポリシー | 100 |
| イニシアチブの定義 | parameters | 100 |
| ポリシー/イニシアティブ割り当て | 除外 (notScopes) | 250 |
| ポリシー規則 | 入れ子になった条件 | 512 |
