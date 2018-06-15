---
title: Windows の Azure CLI サンプル | Microsoft Docs
description: Windows の Azure CLI サンプル
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/01/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c9837ae7b218fd4fdf6d0b97c0218fdfc9de3c53
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726195"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Windows 仮想マシン用の Azure CLI サンプル

次の表には、Windows 仮想マシンをデプロイしている Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

| | |
|---|---|
|**仮想マシンの作成**||
| [仮想マシンの作成](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | 最小の構成で Windows 仮想マシンを作成します。 |
| [完全に構成された仮想マシンの作成](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | リソース グループ、仮想マシン、およびすべての関連リソースを作成します。|
| [可用性が高い仮想マシンの作成](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | 可用性が高く、負荷分散がされた構成で仮想マシンを複数作成します。 |
| [VM の作成と構成スクリプトの実行](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して IIS をインストールします。 |
| [VM の作成と DSC 構成の実行](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | 仮想マシンを作成し、Azure Desired State Configuration (DSC) の拡張機能を使用して IIS をインストールします。 |
|**ネットワークの仮想マシン**||
| [仮想マシン間のネットワーク トラフィックのセキュリティ保護](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | 2 つの仮想マシン、すべての関連リソース、および内部と外部のネットワーク セキュリティ グループ (NSG) を作成します。 |
|**セキュリティ保護された仮想マシン**||
| [VM とデータ ディスクを暗号化する](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Key Vault、暗号化キー、およびサービス プリンシパルを作成し、VM を暗号化します。 |
|**仮想マシンの監視**||
| [Operations Management Suite による VM の監視](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | 仮想マシンを作成し、Operations Management Suite エージェントをインストールし、OMS ワークスペースに VM を登録します。  |
| | |
