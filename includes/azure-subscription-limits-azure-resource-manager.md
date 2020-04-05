---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334728"
---
| リソース | 制限 |
| --- | --- |
| Azure Active Directory テナントごとのサブスクリプション | 無制限。 |
| サブスクリプションあたりの[共同管理者](../articles/cost-management-billing/manage/add-change-subscription-administrator.md)数 |無制限。 |
| サブスクリプションあたりの[リソース グループ数](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure Resource Manager API 要求サイズ |4,194,304 バイト。 |
| サブスクリプションあたりのタグ数<sup>1</sup> |50 |
| サブスクリプションあたりの一意のタグの計算<sup>1</sup> | 10,000 |
| 場所あたりの[サブスクリプション レベルのデプロイ](../articles/azure-resource-manager/templates/deploy-to-subscription.md)数 | 800<sup>2</sup> |

<sup>1</sup>サブスクリプションにはタグを最大で 50 個直接適用することができます。 ただし、サブスクリプション内のリソース グループとリソースに適用する場合には、サブスクリプションに無制限にタグを含めることができます。 リソースまたはリソース グループあたりのタグの数は、50 に制限されています。 サブスクリプションに存在する[一意のタグ名と値のリスト](/rest/api/resources/tags)が Resource Manager から返されるのは、タグの数が 10,000 以下の場合に限られます。 タグの数が 10,000 を超える場合でも、タグでリソースを検出することはできます。  

<sup>2</sup> デプロイ数の上限 800 に達した場合、履歴から、不要になったデプロイを削除します。 サブスクリプション レベルのデプロイを削除するには、[Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) または [az deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete) を使用します。
