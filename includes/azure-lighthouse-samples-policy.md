---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 09/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a9c9b5081232988cb4ab80bfcbb9f2d92d1d025f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336576"
---
これらのサンプルでは、Azure Lighthouse にオンボードされたサブスクリプションで Azure Policy を使用する方法を示します。

| **テンプレート** | **説明** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | 委任されたサブスクリプションに (modify 効果を使用して) タグを追加または削除するポリシーを割り当てます。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../articles/lighthouse/how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | 委任の割り当てを監査するポリシーを割り当てます。 |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | 管理グループ内のサブスクリプションが管理テナントに委任されていることを確認するポリシーを割り当てます。そうでない場合は、割り当てを作成します。
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | (deployIfNotExists 効果を使用して) 委任されたサブスクリプションの Azure Key Vault リソースの診断を有効にするポリシーを割り当てます。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../articles/lighthouse/how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | 委任されたサブスクリプションに対する診断を有効にするいくつかのポリシーを割り当て、Windows と Linux のすべての VM を、このポリシーで作成された Log Analytics のワークスペースに接続します。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../articles/lighthouse/how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | 1 つのイニシアチブ (複数の関連するポリシー定義) を委任されたサブスクリプションに適用します。 |

