---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7a6962a0fa1374edb5a9f43641a0adf398708acf
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570706"
---
## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) ドライバー

NC、NCv2、NCv3、NCasT4_v3、ND、および NDv2 シリーズ VM (NV シリーズではオプション) の NVIDIA Tesla (CUDA) ドライバーは、次の表に示されているオペレーティング システムでのみサポートされます。 ドライバーのダウンロード リンクは、公開時現在のものです。 最新のドライバーについては、[NVIDIA](https://www.nvidia.com/) Web サイトを参照してください。

> [!TIP]
> CUDA ドライバーを手動で Windows Server VM にインストールする代わりに、Azure [データ サイエンス仮想マシン](../articles/machine-learning/data-science-virtual-machine/overview.md) イメージをデプロイすることもできます。 Windows Server 2016 用の DSVM エディションでは、NVIDIA CUDA ドライバーや CUDA Deep Neural Network ライブラリなどのツールが事前にインストールされています。


| OS | Driver |
| -------- |------------- |
| Windows Server 2019 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (.exe) |
| Windows Server 2016 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID ドライバー

Microsoft では、仮想ワークステーションまたは仮想アプリケーションとして使用される NV および NVv3 シリーズ VM 用の NVIDIA GRID ドライバーのインストーラーを再分配します。 次の表に記載されているオペレーティング システム上でのみ、これらの GRID ドライバーを Azure NV シリーズ VM にインストールします。 これらのドライバーには、Azure での GRID 仮想 GPU ソフトウェアのライセンスが含まれています。 NVIDIA vGPU ソフトウェア ライセンス サーバーを設定する必要はありません。

Azure によって再頒布された GRID ドライバーは、NV シリーズ以外の VM (NCv2、NCv3、ND、NDv2 シリーズの VM など) では機能しません。 1 つの例外として、NCas_T4_V3 VM シリーズがあります。このシリーズでは、GRID ドライバーによって、NV シリーズに似たグラフィックス機能が有効になります。

Nvidia K80 GPU を搭載した NC シリーズでは、GRID およびグラフィックスのアプリケーションはサポートされません。  

Nvidia の拡張機能では、常に最新のドライバーがインストールされます。 次に、古いバージョンに依存しているお客様用に、以前のバージョンへのリンクを示します。

Windows Server 2019、Windows Server 2016、Windows 10 (ビルド 2004 以下):
- [GRID 11.3 (452.77)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRID 11.2 (452.57)](https://download.microsoft.com/download/1/b/1/1b15516a-de49-4ba4-8651-6abda4f7fb82/452.57_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

Windows Server 2012 R2: 
- [GRID 11.3 (452.77)](https://download.microsoft.com/download/5/4/3/54323644-3c84-4aa1-97ec-35491f94c866/452.77_grid_server2012R2_64bit_azure_swl.exe) (.exe) 
- [GRID 11.0 (451.48)](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) (.exe)

以前のすべての Nvidia GRID ドライバー リンクの完全な一覧については、[GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) を参照してください。
