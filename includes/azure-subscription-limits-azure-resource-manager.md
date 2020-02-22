---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133707"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| サブスクリプションあたりの[リソース グループ数](../articles/azure-resource-manager/management/overview.md) |980 |980 |
| Azure Resource Manager API 要求サイズ |4,194,304 バイト。 |4,194,304 バイト。 |
| サブスクリプションあたりのタグ数<sup>1</sup> |無制限。 |無制限。 |
| サブスクリプションあたりの一意のタグの計算<sup>1</sup> | 10,000 | 10,000 |
| 場所あたりの[サブスクリプション レベルのデプロイ](../articles/azure-resource-manager/templates/deploy-to-subscription.md)数 | 800<sup>2</sup> | 800 |
| Azure Active Directory テナントごとのサブスクリプション | 無制限。 | 無制限。 |
| サブスクリプションあたりの[共同管理者](../articles/cost-management-billing/manage/add-change-subscription-administrator.md)数 |無制限。 |無制限。 |

<sup>1</sup> 1 つのサブスクリプションで適用できるタグの数に制限はありません。 リソースまたはリソース グループあたりのタグの数は、50 に制限されています。 サブスクリプションに存在する[一意のタグ名と値のリスト](/rest/api/resources/tags)が Resource Manager から返されるのは、タグの数が 10,000 以下の場合に限られます。 タグの数が 10,000 を超える場合でも、タグでリソースを検出することはできます。  

<sup>2</sup> デプロイ数の上限 800 に達した場合、履歴から、不要になったデプロイを削除します。 サブスクリプション レベルのデプロイを削除するには、[Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) または [az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete) を使用します。
