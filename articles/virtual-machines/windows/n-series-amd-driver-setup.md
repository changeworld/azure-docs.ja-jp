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
ms.openlocfilehash: 883dbc95ee77d03aee4c3231c6ab8c03f9f7f6e4
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387837"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Windows を実行している N シリーズ VM に AMD GPU ドライバーをインストールする

Windows を実行している新しい Azure NVv4 シリーズ VM の GPU 機能を利用するには、AMD GPU ドライバーがインストールされている必要があります。 AMD ドライバーの拡張機能は、今後数週間の内に利用可能になる予定です。 この記事では、サポートされているオペレーティングシステム、ドライバー、および手動のインストールと検証手順について説明します。

基本仕様、ストレージの容量、およびディスクの詳細については、「[GPU Windows VM のサイズ](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。



## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

| OS | Driver |
| -------- |------------- |
| Windows 10 EVD - ビルド 1903 <br/><br/>Windows 10 - ビルド 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>ドライバーのインストール

1. リモートデスクトップで、各 NVv4 シリーズ VM に接続します。

2. NVv4 プレビューのお客様は、VM を停止し、停止 (割り当て解除) 状態に移行するまでお待ちください。

3. VM を起動し、最新の [AMD Cleanup Utility](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) をダウンロードしてください。 "amdcleanuputility-x64.exe" を実行して、既存のドライバーをアンインストールします。 以前のドライバーと共にインストールされた既存のクリーンナップ ユーティリティは使用しないでください。  

4. 最新のドライバーをダウンロードしてインストールします。

5. VM を再起動してください。

## <a name="verify-driver-installation"></a>ドライバーのインストールの確認

ドライバーのインストールはデバイス マネージャーで確認できます。 次の例は、Azure NVv4 VM での Radeon Instinct MI25 カードの正常な構成を示しています。
<br />
![GPU ドライバーのプロパティ](./media/n-series-amd-driver-setup/device-manager.png)

dxdiag を使用して、ビデオ RAM などの GPU 表示プロパティを確認できます。 次の例は、Azure NVv4 VM での Radeon Instinct MI25 カードの 1/8 パーティションを示しています。
<br />
![GPU ドライバーのプロパティ](./media/n-series-amd-driver-setup/dxdiag.png)
