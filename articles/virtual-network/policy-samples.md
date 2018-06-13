---
title: Porlicy のサンプル テンプレート | Microsoft Docs
description: 仮想ネットワーク用の Azure Policy のサンプル テンプレート。
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 2b8766a5353b015030872176e9032034afb7cb9d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779558"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>仮想ネットワーク用の Azure Policy のサンプル テンプレート

次の表に、サンプルの [Azure Policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) テンプレートへのリンクを示します。 サンプルは、[Azure Policy のサンプル リポジトリ](https://github.com/Azure/azure-policy)にあります。

| | |
|---|---|
|**ネットワーク**||
| [すべての NIC 上の NSG X](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 特定のネットワーク セキュリティ グループが、すべての仮想ネットワーク インターフェイスで使用される必要があります。 使用するネットワーク セキュリティ グループの ID を指定します。 |
| [すべてのサブネット上の NSG X](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 特定のネットワーク セキュリティ グループが、すべての仮想サブネットで使用される必要があります。 使用するネットワーク セキュリティ グループの ID を指定します。 |
| [ルート テーブルなし](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |仮想ネットワークがルート テーブルを使ってデプロイされることを禁止します。 |
| [VM ネットワーク インターフェイスでの承認されたサブネットの使用](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 承認されたサブネットがネットワーク インターフェイスで使用される必要があります。 承認されたサブネットの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認された vNet の使用](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 承認された仮想ネットワークがネットワーク インターフェイスで使用される必要があります。 承認された仮想ネットワークの ID を指定します。 |
|**監視**||
| [診断設定の監査](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 指定されたリソースの種類で診断設定が有効になっていない場合に監査を行います。 リソースの種類の配列を指定して、診断設定が有効になっているかどうかを確認します。 |
|**名前およびテキストの表記規則**||
| [複数の名前パターンを許可する](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 多数の名前パターンのいずれかをリソースに使うことを許可します。 |
| [類似パターンを要求する](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | リソース名がパターンの *like* 条件を満たすことを保証します。 |
| [一致パターンを要求する](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | リソース名が、指定された名前付けパターンと一致することを保証します。 |
| [タグ一致パターンを要求する](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | タグの値がテキストのパターンと一致することを保証します。 |
|**タグ**||
| [課金タグ ポリシーのイニシアティブ](../azure-policy/scripts/billing-tags-policy-init.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | コスト センターと製品名に対して指定したタグの値が必要です。 組み込みのポリシーを使用して、必要なタグを強制的に適用します。 タグに対して必須の値を指定します。  |
| [リソース グループへのタグと値の強制](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 1 つのリソース グループに 1 つのタグと値が必要です。 必要なタグ名と値を指定します。  |
| [タグとその値を強制](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 指定したタグの名前と値が必要です。 強制的に適用するタグ名と値を指定します。  |
| [タグとその既定値の適用](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 指定されたタグがない場合、そのタグ名と値を付加します。 適用するタグ名と値を指定します。  |
|**全般**||
| [許可される場所](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | すべてのリソースが承認済みの場所にデプロイされる必要があります。 承認された場所の配列を指定します。  |
| [許可されるリソースの種類](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 必ず承認されたリソースの種類のみがデプロイされるようにします。 許可されているリソースの種類の配列を指定します。  |
| [許可されないリソースの種類](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | 指定されたリソースの種類のデプロイを禁止します。 ブロックするリソースの種類の配列を指定します。  |