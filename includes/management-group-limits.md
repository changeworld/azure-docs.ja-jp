---
title: インクルード ファイル
description: インクルード ファイル
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: cdcf6215973755444da9e513761de7ac71e479d4
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98738762"
---
| リソース | 制限 |
| --- | --- |
| Azure AD テナントごとの管理グループ | 10,000 |
| 管理グループあたりのサブスクリプション | 無制限。 |
| 管理グループ階層のレベル | ルート レベル + 6 レベル<sup>1</sup> |
| 管理グループあたりの直接の親管理グループ | 1 つ |
| 場所あたりの[管理グループ レベルのデプロイ](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup>6 レベルにはサブスクリプション レベルは含まれません。

<sup>2</sup> デプロイ数の上限 800 に達した場合、履歴から、不要になったデプロイを削除します。 管理グループ レベルのデプロイを削除するには、[Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) または [az deployment mg delete](/cli/azure/deployment/mg#az-deployment-mg-delete) を使用します。
