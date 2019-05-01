---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/12/2018
origin.date: 05/17/2018
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: 4e2412a98b9f292398b972099daa1642534f180d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865962"
---
### <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

|  |  |
|---------|---------|
| [VM が管理ディスクを使っていない場合の監査](../articles/governance/policy/samples/create-vm-managed-disk.md) | マネージド ディスクを使用していない仮想マシンが作成されたときに監査が行われます。|
| [管理ディスクを使用した VM の作成](../articles/governance/policy/samples/use-managed-disk-vm.md) | 仮想マシンでマネージド ディスクを使用する必要があります。|
| [Azure Hybrid Use Benefit の拒否](../articles/governance/policy/samples/deny-hybrid-use.md) | Azure Hybrid Use Benefit (AHUB) の使用を禁止します。 オンプレミス ライセンスの使用を許可したくない場合に利用します。 |
| [特定の VM プラットフォーム イメージのみの許可](../articles/governance/policy/samples/allow-certain-vm-image.md) | 仮想マシンが特定のバージョンの UbuntuServer を使用する必要があります。 |