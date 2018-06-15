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
ms.openlocfilehash: 388d6641fb77d09c1706623b84ec256573807747
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664564"
---
### <a name="network-interfaces"></a>ネットワーク インターフェイス

|  |  |
|---------|---------|
| [すべての NIC 上の NSG X](../articles/azure-policy/scripts/nsg-on-nic.md) | 特定のネットワーク セキュリティ グループが、すべての仮想ネットワーク インターフェイスで使用される必要があります。 使用するネットワーク セキュリティ グループの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認されたサブネットの使用](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | 承認されたサブネットがネットワーク インターフェイスで使用される必要があります。 承認されたサブネットの ID を指定します。 |
| [VM ネットワーク インターフェイスでの承認された vNet の使用](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | 承認された仮想ネットワークがネットワーク インターフェイスで使用される必要があります。 承認された仮想ネットワークの ID を指定します。 |