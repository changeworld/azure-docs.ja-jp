---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 06/04/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 1197b15f313746c0a8542bfadeb4e0123818fca8
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575636"
---
| リソース | 制限 |
| --- | --- |
| [Azure Active Directory テナントに関連付けられている](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)サブスクリプション | 無制限 |
| サブスクリプションあたりの[共同管理者](../articles/cost-management-billing/manage/add-change-subscription-administrator.md)数 |無制限 |
| サブスクリプションあたりの[リソース グループ数](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure Resource Manager API 要求サイズ |4,194,304 バイト |
| サブスクリプションあたりのタグ数<sup>1</sup> |50 |
| サブスクリプションあたりの一意のタグの計算<sup>1</sup> | 80,000 |
| 場所あたりの[サブスクリプション レベルのデプロイ](../articles/azure-resource-manager/templates/deploy-to-subscription.md)数 | 800<sup>2</sup> |
| [サブスクリプションレベルのデプロイ](../articles/azure-resource-manager/templates/deploy-to-subscription.md)の場所 | 10 |

<sup>1</sup>サブスクリプションにはタグを最大で 50 個直接適用することができます。 ただし、サブスクリプション内のリソース グループとリソースに適用する場合には、サブスクリプションに無制限にタグを含めることができます。 リソースまたはリソース グループあたりのタグの数は、50 に制限されています。 サブスクリプションに存在する[一意のタグ名と値のリスト](/rest/api/resources/tags)が Resource Manager から返されるのは、タグの数が 80,000 以下の場合に限られます。 タグの数が 80,000 を超える場合でも、タグでリソースを検出することはできます。

<sup>2</sup>この制限に近づくと、デプロイは履歴から自動的に削除されます。 詳細については、「[デプロイ履歴からの自動削除](../articles/azure-resource-manager/templates/deployment-history-deletions.md)」を参照してください。