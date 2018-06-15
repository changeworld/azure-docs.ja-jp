---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b7bbf1fb8ffc1f57a53682d3baf288077b144572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664581"
---
### <a name="virtual-networks"></a>仮想ネットワーク

|  |  |
|---------|---------|
| [許可されたアプリケーション ゲートウェイ SKU](../articles/azure-policy/scripts/allowed-app-gate-sku.md) | アプリケーション ゲートウェイで適切な SKU を使用する必要があります。 承認された SKU の配列を指定します。 |
| [ER ネットワークへのネットワーク ピアリングがない](../articles/azure-policy/scripts/no-peering-er-net.md) | 指定されたリソース グループ内のネットワークに、ネットワーク ピアリングを関連付けることを禁止します。 集中管理されているネットワーク インフラストラクチャとの接続を防ぐために使用します。 関連付けを防ぐリソース グループの名前を指定します。 |
| [ユーザー定義ルート テーブルがない](../articles/azure-policy/scripts/no-user-def-route-table.md)  |仮想ネットワークがユーザー定義のルート テーブルを使ってデプロイされることを禁止します。 |
| [すべてのサブネット上の NSG X](../articles/azure-policy/scripts/nsg-on-subnet.md) | 特定のネットワーク セキュリティ グループが、すべての仮想サブネットで使用される必要があります。 使用するネットワーク セキュリティ グループの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認されたサブネットの使用](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | 承認されたサブネットがネットワーク インターフェイスで使用される必要があります。 承認されたサブネットの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認された vNet の使用](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | 承認された仮想ネットワークがネットワーク インターフェイスで使用される必要があります。 承認された仮想ネットワークの ID を指定します。 |