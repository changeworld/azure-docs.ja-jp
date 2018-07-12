---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740557"
---
[Azure のポリシー](/azure/azure-policy/)は、サブスクリプション内のすべてのリソースが会社の基準を順守するために役立ちます。 ポリシーを使用し、展開オプションを承認されたリソースの種類と SKU のみに制限することでコストを削減します。 リソースのルールとアクションを定義すると、それらのルールは展開時に自動的に適用されます。 たとえば、展開されているリソースの種類を制御できます。 また、リソースの承認済みの場所を制限することもできます。 アクションを拒否するポリシーや、アクションの監査を設定するポリシーがあります。

ポリシーはロールベースのアクセス制御 (RBAC) を補完するものです。 RBAC はユーザー アクセスに重点を置き、既定で禁止し、明示的に許可するシステムです。 ポリシーは、展開中と展開後のリソース プロパティに焦点を当てています。 ポリシーは既定で許可し、明示的に禁止するシステムです。

ポリシーを理解するには、*ポリシー定義*と*ポリシー割り当て*という 2 つの概念があります。 ポリシー定義には、適用する管理条件を指定します。 ポリシーの割り当ては、特定のスコープのアクションにポリシー定義を設定します。

![ポリシーの割り当て](./media/resource-manager-governance-policy/policy-concepts.png)

Azure には、変更せずに使用できる組み込みのポリシー定義がいくつか用意されています。 パラメーター値を渡して、スコープ内で許可する値を指定します。 組み込みポリシー定義が要件を満たしていない場合は、[カスタムのポリシー定義を作成](../articles/azure-policy/create-manage-policy.md)することができます。
