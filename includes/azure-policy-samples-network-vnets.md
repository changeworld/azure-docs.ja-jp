---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: df9c291136fc6a48effb08cd59eeb66486eb641c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003857"
---
### <a name="virtual-networks"></a>仮想ネットワーク

|  |  |
|---------|---------|
| [許可されたアプリケーション ゲートウェイ SKU](../articles/governance/policy/samples/allowed-app-gate-sku.md) | アプリケーション ゲートウェイで適切な SKU を使用する必要があります。 承認された SKU の配列を指定します。 |
| [ER ネットワークへのネットワーク ピアリングがない](../articles/governance/policy/samples/no-peering-er-net.md) | 指定されたリソース グループ内のネットワークに、ネットワーク ピアリングを関連付けることを禁止します。 集中管理されているネットワーク インフラストラクチャとの接続を防ぐために使用します。 関連付けを防ぐリソース グループの名前を指定します。 |
| [ユーザー定義ルート テーブルがない](../articles/governance/policy/samples/no-user-def-route-table.md)  |仮想ネットワークがユーザー定義のルート テーブルを使ってデプロイされることを禁止します。 |
| [すべてのサブネット上の NSG X](../articles/governance/policy/samples/nsg-on-subnet.md) | 特定のネットワーク セキュリティ グループが、すべての仮想サブネットで使用される必要があります。 使用するネットワーク セキュリティ グループの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認されたサブネットの使用](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | 承認されたサブネットがネットワーク インターフェイスで使用される必要があります。 承認されたサブネットの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認された vNet の使用](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | 承認された仮想ネットワークがネットワーク インターフェイスで使用される必要があります。 承認された仮想ネットワークの ID を指定します。 |