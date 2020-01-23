---
title: Windows 用 Azure N シリーズ AMD GPU ドライバーのセットアップ
description: Azure で Windows Server または Windows を実行する N シリーズ VM 用の AMD GPU ドライバーの設定方法
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 7527cd59ef812c108cc53f84ad19f4d2cff9ec5e
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893560"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Windows を実行している N シリーズ VM に AMD GPU ドライバーをインストールする

Windows を実行している新しい Azure NVv4 シリーズ VM の GPU 機能を利用するには、AMD GPU ドライバーがインストールされている必要があります。 AMD ドライバーの拡張機能は、今後数週間の内に利用可能になる予定です。 この記事では、サポートされているオペレーティングシステム、ドライバー、および手動のインストールと検証手順について説明します。

基本仕様、ストレージの容量、およびディスクの詳細については、「[GPU Windows VM のサイズ](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。



## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

| OS | Driver |
| -------- |------------- |
| Windows 10 EVD - ビルド 1903 <br/><br/>Windows 10 - ビルド 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [19.Q4.1](https://download.microsoft.com/download/7/e/5/7e558ac0-3fff-413d-af62-800285a2fc53/Radeon-Pro-Software-for-Enterprise-19.Q4.1-Technical-Preview.exe) (.exe) |

## <a name="driver-installation"></a>ドライバーのインストール

1. リモートデスクトップで、各 NVv4 シリーズ VM に接続します。

1. ドライバーのセットアップ ファイルをダウンロードし、抽出します。 フォルダーに移動して 'setup.exe' を実行し、ご使用の Windows オペレーティング システム用のサポートされるドライバーをインストールします。

## <a name="verify-driver-installation"></a>ドライバーのインストールの確認

ドライバーのインストールはデバイス マネージャーで確認できます。 次の例は、Azure NVv4 VM での Radeon Instinct MI25 カードの正常な構成を示しています。
<br />
![GPU ドライバーのプロパティ](./media/n-series-amd-driver-setup/device-manager.png)

dxdiag を使用して、ビデオ RAM などの GPU 表示プロパティを確認できます。 次の例は、Azure NVv4 VM での Radeon Instinct MI25 カードの 1/8 パーティションを示しています。
<br />
![GPU ドライバーのプロパティ](./media/n-series-amd-driver-setup/dxdiag.png)
