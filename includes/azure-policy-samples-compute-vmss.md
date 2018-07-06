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
ms.openlocfilehash: 725d37a6521c874d14291ea7e3c53ea94907eb17
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138101"
---
### <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

|  |  |
|---------|---------|
| [VM が管理ディスクを使っていない場合の監査](../articles/azure-policy/scripts/create-vm-managed-disk.md) | 管理ディスクを使用していない仮想マシンが作成されたときに監査が行われます。|
| [管理ディスクを使用した VM の作成](../articles/azure-policy/scripts/use-managed-disk-vm.md) | 仮想マシンで管理ディスクを使用する必要があります。|
| [Azure Hybrid Use Benefit の拒否](../articles/azure-policy/scripts/deny-hybrid-use.md) | Azure Hybrid Use Benefit (AHUB) の使用を禁止します。 オンプレミス ライセンスの使用を許可したくない場合に利用します。 |
| [特定の VM プラットフォーム イメージのみの許可](../articles/azure-policy/scripts/allow-certain-vm-image.md) | 仮想マシンが特定のバージョンの UbuntuServer を使用する必要があります。 |