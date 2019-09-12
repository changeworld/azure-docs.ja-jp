---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "67133035"
---
Azure Policy では、オブジェクトの種類ごとに最大数があります。 _Scope_ というエントリは、サブスクリプションまたは[管理グループ](../articles/governance/management-groups/overview.md)のいずれかを意味します。

| Where | 対象 | 最大数 |
|---|---|---|
| Scope (スコープ) | ポリシーの定義 | 500 |
| Scope (スコープ) | イニシアチブ定義 | 100 |
| Tenant | イニシアチブ定義 | 1,000 |
| Scope (スコープ) | ポリシーとイニシアティブの割り当て | 100 |
| ポリシー定義 | parameters | 20 |
| イニシアチブ定義 | ポリシー | 100 |
| イニシアチブ定義 | parameters | 100 |
| ポリシーとイニシアティブの割り当て | 除外 (notScopes) | 400 |
| ポリシー規則 | 入れ子になった条件 | 512 |
