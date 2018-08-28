---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/16/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0e5d1214a8e1af8299cb40d1a3b31ff6eafc8c5c
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "40185223"
---
Azure Policy では、オブジェクトの種類ごとに最大数があります。 _Scope_ というエントリは、サブスクリプションまたは[管理グループ](../articles/azure-resource-manager/management-groups-overview.md)のいずれかを意味します。

| Where | 対象 | 最大数 |
|---|---|---|
| Scope (スコープ) | ポリシーの定義 | 250 |
| Scope (スコープ) | イニシアチブの定義 | 100 |
| Tenant | イニシアチブの定義 | 1,000 |
| Scope (スコープ) | ポリシー/イニシアティブ割り当て | 100 |
| ポリシーの定義 | parameters | 20 |
| イニシアチブの定義 | ポリシー | 100 |
| イニシアチブの定義 | parameters | 100 |
| ポリシー/イニシアティブ割り当て | 除外 (notScopes) | 100 |
| ポリシー規則 | 入れ子になった条件 | 512 |
