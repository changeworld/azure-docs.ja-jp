---
title: "Azure 仮想マシンの PowerShell のサンプル | Microsoft Docs"
description: "Azure 仮想マシンの PowerShell のサンプル"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/16/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: c57bf0efc1eb97d34d76483b8889d884ccfd0988
ms.contentlocale: ja-jp
ms.lasthandoff: 05/16/2017


---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure 仮想マシンの PowerShell のサンプル

次の表には、Windows 仮想マシンを作成および管理する PowerShell サンプル スクリプトへのリンクが掲載されています。

| | |
|---|---|
|**仮想マシンの作成**||
| [完全に構成された仮想マシンの作成](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | リソース グループ、仮想マシン、およびすべての関連リソースを作成します。|
| [VM の作成と構成スクリプトの実行](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して IIS をインストールします。 |
| [VM の作成と DSC 構成の実行](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 仮想マシンを作成し、Azure Desired State Configuration (DSC) の拡張機能を使用して IIS をインストールします。 |
| [管理 OS ディスクから VM を作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 既存の管理ディスクを OS ディスクとして接続することで仮想マシンを作成します。 |
| [スナップショットから VM を作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | スナップショットから管理ディスクを作成し、その新しい管理ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 |
|**仮想マシンの監視**||
| [Operations Management Suite による VM の監視](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 仮想マシンを作成し、Operations Management Suite エージェントをインストールし、OMS ワークスペースに VM を登録します。  |
| | |

