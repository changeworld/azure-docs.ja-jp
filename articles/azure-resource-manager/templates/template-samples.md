---
title: テンプレートのサンプル
description: ロールやロックといった管理機能をデプロイするための Azure Resource Manager テンプレート サンプル。
ms.topic: sample
ms.date: 11/16/2018
ms.openlocfilehash: 61d46d4b032a4eaf326acd21e85b332bdb407f40
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75471787"
---
# <a name="azure-resource-manager-templates-for-management-features"></a>管理機能に使用する Azure Resource Manager テンプレート

次の表は、Resource Manager に備わっている機能用の Azure Resource Manager テンプレートのリンク一覧です。

| | |
|-|-|
|**ロールの割り当て**||
| [リソース グループのロールを割り当てる](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)| 既存のリソース グループのユーザーに組み込みロールを割り当てます。 |
| [既存の仮想マシンのロールを割り当てる](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)| 既存の VM のユーザーに組み込みロールを割り当てます。 |
| [複数の仮想マシンのロールを割り当てる](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)| 複数の仮想マシンのユーザーに組み込みロールを割り当てます。 |
| [Azure サブスクリプションのロールを割り当てる](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/subscription-role-assigment)| Azure サブスクリプションのユーザーにロールを割り当てます。 |
|**ロール定義**||
| [カスタム ロールの定義を作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-role-def)| Azure サブスクリプションに新しいロールの定義を作成します。 |
|**リソース ロック**||
| [リソース グループをロックする](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)| リソース グループを作成し、そこに **DoNotDelete** ロックを適用します。 ユーザーには投稿者ロールを割り当てます。 |
| | |
