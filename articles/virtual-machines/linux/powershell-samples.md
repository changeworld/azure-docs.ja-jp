---
title: PowerShell のサンプル
description: VM 向け PowerShell サンプルの一覧
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: b1a154e562db9f8fdd74300bb8eb3d6839cb2ef4
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321924"
---
# <a name="azure-vm-powershell-samples-for-creating-and-managing-linux-vms"></a>Linux VM の作成と管理のための、Azure VM PowerShell サンプル

次の表には、Linux 仮想マシンを作成および管理する PowerShell サンプル スクリプトへのリンクが掲載されています。

| スクリプト | 説明 |
|---|---|
|**仮想マシンの作成**||
| [完全に構成された仮想マシンの作成](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | リソース グループ、仮想マシン、およびすべての関連リソースを作成します。|
| [Docker が有効になっている VM の作成](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Docker ホストとしてこの VM を構成し、NGINX コンテナーを実行します。 |
| [VM の作成と構成スクリプトの実行](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して NGINX をインストールします。 |
| [WordPress がインストールされている VM の作成](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して WordPress をインストールします。 |
| [管理 OS ディスクから VM を作成する](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 既存の管理ディスクを OS ディスクとして接続することで仮想マシンを作成します。 |
| [スナップショットから VM を作成する](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | スナップショットからマネージド ディスクを作成し、その新しいマネージド ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 |
|**ストレージの管理**||
| [同じまたは別のサブスクリプションの VHD からマネージド ディスクを作成する](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 同じまたは別のサブスクリプションで、OS ディスクとして特殊化した VHD から、またはデータ ディスクとしてデータ VHD から、マネージド ディスクを作成します。  |
| [スナップショットからマネージド ディスクを作成する](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | スナップショットからマネージド ディスクを作成します。 |
| [ストレージ アカウントに VHD としてスナップショットをエクスポートする](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 管理スナップショットを VHD として別のリージョンのストレージ アカウントにエクスポートします。 |
| [ストレージ アカウントにマネージド ディスクの VHD をエクスポートする](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 別のリージョンのストレージ アカウントに、マネージド ディスクの基盤となる VHD をエクスポートします。 |
| [VHD からスナップショットを作成する](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | VHD からスナップショットを作成した後、そのスナップショットを使用して複数の同じマネージド ディスクを短時間で作成します。  |
| [同じサブスクリプションまたは別のサブスクリプションにスナップショットをコピーする](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 同じサブスクリプションまたは親スナップショットと同じリージョン内の別のサブスクリプションに、スナップショットをコピーします。 |
|**仮想マシンの監視**||
| [Azure Monitor ログを使用して VM を監視する](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 仮想マシンを作成し、Log Analytics エージェントをインストールして、VM を Log Analytics ワークスペースに登録します。  |
| [同じサブスクリプションまたは別のサブスクリプションにマネージド ディスクをコピーする](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 同じサブスクリプションまたは親マネージド ディスクと同じリージョン内の別のサブスクリプションに、マネージド ディスクをコピーします。
| [PowerShell を使用してサブスクリプション内のすべての VM に関する詳細情報を収集する](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 指定されたサブスクリプション内の VM の VM 名、リソース グループ名、リージョン、仮想ネットワーク、サブネット、プライベート IP アドレス、OS の種類、およびパブリック IP アドレスを含む csv を作成します。
| | |
