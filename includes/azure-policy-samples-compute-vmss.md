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
ms.openlocfilehash: e9f2f6eaab1b3504e549171ce6c60ecd9b3b5c7b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664521"
---
### <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

|  |  |
|---------|---------|
| [VM が管理ディスクを使っていない場合の監査](../articles/azure-policy/scripts/create-vm-managed-disk.md) | 管理ディスクを使用していない仮想マシンが作成されたときに監査が行われます。|
| [管理ディスクを使用した VM の作成](../articles/azure-policy/scripts/use-managed-disk-vm.md) | 仮想マシンで管理ディスクを使用する必要があります。|
| [Azure Hybrid Use Benefit の拒否](../articles/azure-policy/scripts/deny-hybrid-use.md) | Azure Hybrid Use Benefit (AHUB) の使用を禁止します。 オンプレミス ライセンスの使用を許可したくない場合に利用します。 |
| [特定の VM プラットフォーム イメージのみの許可](../articles/azure-policy/scripts/allow-certain-vm-image.md) | 仮想マシンが特定のバージョンの UbuntuServer を使用する必要があります。 |