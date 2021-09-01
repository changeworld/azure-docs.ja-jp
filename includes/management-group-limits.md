---
title: インクルード ファイル
description: インクルード ファイル
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee564accd0eaf53f0089e0dba3bb00e3eb1665e1
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575651"
---
| リソース | 制限 |
| --- | --- |
| Azure AD テナントごとの管理グループ | 10,000 |
| 管理グループあたりのサブスクリプション | 無制限。 |
| 管理グループ階層のレベル | ルート レベル + 6 レベル<sup>1</sup> |
| 管理グループあたりの直接の親管理グループ | 1 つ |
| 場所あたりの[管理グループ レベルのデプロイ](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |
| [管理グループ レベルのデプロイ](../articles/azure-resource-manager/templates/deploy-to-management-group.md)の場所 | 10 |

<sup>1</sup>6 レベルにはサブスクリプション レベルは含まれません。

<sup>2</sup> デプロイ数の上限 800 に達した場合、履歴から、不要になったデプロイを削除します。 管理グループ レベルのデプロイを削除するには、[Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) または [az deployment mg delete](/cli/azure/deployment/mg#az_deployment_mg_delete) を使用します。
