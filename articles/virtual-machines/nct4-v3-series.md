---
title: NCas T4 v3 シリーズ
description: NCas T4 v3 シリーズ VM の仕様。
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
author: vikancha-MSFT
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: 8a93d84fea79935f96498eff7d391271e6f4c7ef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013101"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 シリーズ 

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

NCasT4_v3 シリーズ仮想マシンは、[Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU と AMD EPYC 7V12(Rome) CPU を搭載しています。 この VM には、最大 4 個の NVIDIA T4 GPU (それぞれに 16 GB のメモリを装備)、最大 64 個の非マルチスレッド AMD EPYC 7V12 (Rome) プロセッサ コア (ベース周波数 2.45 GHz、全コアのピーク周波数 3.1 GHz、単一コアのピーク周波数 3.3 GHz)、440 GiB のシステム メモリが搭載されています。 これらの仮想マシンは、AI サービス (ユーザーが生成した要求のリアルタイム推論など) のデプロイや、NVIDIA の GRID ドライバーと仮想 GPU テクノロジを使用した対話型グラフィックスと視覚化ワークロードに最適です。 CUDA、TensorRT、Caffe、ONNX、およびその他のフレームワークをベースにした標準的な GPU コンピューティング ワークロードや、OpenGL と DirectX をベースにした GPU アクセラレーションを使用するグラフィック アプリケーションは、ユーザーのすぐ近くにある NCasT4_v3 シリーズに経済的にデプロイできます。

<br>

[ACU](acu.md):230-260<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[Ultra Disk](disks-types.md#ultra-disks): サポートされています (可用性、使用状況、およびパフォーマンスについては、[詳細をご覧ください](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)) <br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています<br>
Nvidia NVLink Interconnect:サポートされていません<br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4/8,000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8/8,000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8 / 32000  |




## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Windows または Linux を実行している Azure NCasT4_v3 シリーズの GPU 機能を利用するには、Nvidia GPU ドライバーがインストールされている必要があります。

Nvidia GPU ドライバーを手動でインストールする場合、サポートされるオペレーティング システム、ドライバー、インストール、および検証手順について、[Windows 用 N シリーズ GPU ドライバーのセットアップ](./windows/n-series-driver-setup.md)に関する記事を参照してください。

Azure Nvidia GPU ドライバー拡張機能は、CUDA ドライバーを NCasT4_v3 シリーズ VM にデプロイします。 グラフィックスと視覚化のワークロードについては、Azure でサポートされている GRID ドライバーを手動でインストールしてください。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール:[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細については、「[Azure で利用できるディスクの種類](disks-types.md)」を参照してください

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
