---
title: NVv4 シリーズ - Azure Virtual Machines
description: NVv4 シリーズ VM の仕様。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 3b1deceb8d5c6e2a22254b2719449e5adb069def
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565399"
---
# <a name="nvv4-series-preview"></a>NVv4 シリーズ (プレビュー)

NVv4 シリーズ仮想マシンは [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU および AMD EPYC 7V12 (Rome) CPU を搭載しています。 NVv4 シリーズの場合、Azure では、部分的な GPU を備えた仮想マシンを導入しています。 16 GiB フレーム バッファーを備えた完全な GPU に対して 2 GiB フレーム バッファーを備えた 8 分の 1 の GPU を下限として、GPU で強化されたグラフィックス アプリケーションと仮想デスクトップに対して適正なサイズの仮想マシンを選択します。 NVv4 仮想マシンでは現在、Windows ゲスト オペレーティング システムのみがサポートされています。

[ぜひプレビュー期間中にサインアップして、これらのマシンをご利用ください](https://aka.ms/nvv4signup)。
<br>

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> NVv4 シリーズ VM では、AMD 同時実行マルチスレッド技術を考慮しています

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Windows を実行している Azure N シリーズ VM の GPU 機能を利用するには、NVIDIA または AMD GPU ドライバーがインストールされている必要があります。

[NVIDIA GPU ドライバー拡張機能](/extensions/hpccompute-gpu-windows.md)は、Windows N シリーズ VM 上に適切な NVIDIA CUDA または GRID ドライバーをインストールします。 この拡張機能は、Azure Portal または Azure PowerShell や Azure Resource Manager テンプレートなどのツールを使用してインストールまたは管理します。 サポートされるオペレーティング システムおよびデプロイ手順については、[NVIDIA GPU ドライバー拡張機能のドキュメント](/extensions/hpccompute-gpu-windows.md)を参照してください。 VM 拡張機能の一般情報については、「[Azure 仮想マシンの拡張機能と機能](/extensions/overview.md)」をご覧ください。

NVIDIA GPU ドライバーを手動でインストールすることを選択した場合、サポートされるオペレーティング システム、ドライバー、インストール、および検証手順については、「[Windows 用 N シリーズ GPU ドライバーのセットアップ](/windows/n-series-driver-setup.md)」を参照してください。

AMD GPU ドライバーを手動でインストールする場合、サポートされるオペレーティング システム、ドライバー、インストールおよび検証手順については、[Windows 用 N シリーズ AMD GPU ドライバーのセットアップ](/windows/n-series-amd-driver-setup.md)に関する記事を参照してください。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。