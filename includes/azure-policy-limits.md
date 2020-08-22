---
title: インクルード ファイル
description: インクルード ファイル
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: e22594a50a9c0d814ef7d66443f8253f5832cb1d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038488"
---
Azure Policy では、オブジェクトの種類ごとに最大数があります。 _Scope_ というエントリは、サブスクリプションまたは[管理グループ](../articles/governance/management-groups/overview.md)のいずれかを意味します。

| Where | 対象 | 最大数 |
|---|---|---|
| Scope | ポリシーの定義 | 500 |
| Scope | イニシアチブ定義 | 200 |
| Tenant | イニシアチブ定義 | 2,500 |
| Scope | ポリシーとイニシアティブの割り当て | 200 |
| ポリシー定義 | パラメーター | 20 |
| イニシアチブ定義 | ポリシー | 1000 |
| イニシアチブ定義 | パラメーター | 100 |
| ポリシーとイニシアティブの割り当て | 除外 (notScopes) | 400 |
| ポリシー規則 | 入れ子になった条件 | 512 |
| 修復タスク | リソース | 1000 |
