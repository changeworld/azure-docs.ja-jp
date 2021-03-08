---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 10/12/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 20d80fe1a09d388552b6289f8a9b23c878672f94
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974289"
---
これらのサンプルでは、Azure Lighthouse にオンボードされたサブスクリプションで Azure Policy を使用する方法を示します。

| **テンプレート** | **説明** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | 委任されたサブスクリプションに (modify 効果を使用して) タグを追加または削除するポリシーを割り当てます。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../articles/lighthouse/how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | Azure Lighthouse の委任を特定の管理テナントに制限するポリシーを割り当てます。 |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | 委任の割り当てを監査するポリシーを割り当てます。 |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | 管理グループ内のサブスクリプションが管理テナントに委任されていることを確認するポリシーを割り当てます。そうでない場合は、割り当てを作成します。
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | (deployIfNotExists 効果を使用して) 委任されたサブスクリプションの Azure Key Vault リソースの診断を有効にするポリシーを割り当てます。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../articles/lighthouse/how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | 委任されたサブスクリプションに対する診断を有効にするいくつかのポリシーを割り当て、Windows と Linux のすべての VM を、このポリシーで作成された Log Analytics のワークスペースに接続します。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../articles/lighthouse/how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | 1 つのイニシアチブ (複数の関連するポリシー定義) を委任されたサブスクリプションに適用します。 |

