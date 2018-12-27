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
ms.openlocfilehash: 75567fd1521c48c1731c8d7756da69dfef153a61
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003801"
---
### <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

|  |  |
|---------|---------|
| [VM が管理ディスクを使っていない場合の監査](../articles/governance/policy/samples/create-vm-managed-disk.md) | マネージド ディスクを使用していない仮想マシンが作成されたときに監査が行われます。|
| [管理ディスクを使用した VM の作成](../articles/governance/policy/samples/use-managed-disk-vm.md) | 仮想マシンでマネージド ディスクを使用する必要があります。|
| [Azure Hybrid Use Benefit の拒否](../articles/governance/policy/samples/deny-hybrid-use.md) | Azure Hybrid Use Benefit (AHUB) の使用を禁止します。 オンプレミス ライセンスの使用を許可したくない場合に利用します。 |
| [特定の VM プラットフォーム イメージのみの許可](../articles/governance/policy/samples/allow-certain-vm-image.md) | 仮想マシンが特定のバージョンの UbuntuServer を使用する必要があります。 |