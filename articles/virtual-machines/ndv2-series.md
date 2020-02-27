---
title: NDv2 シリーズ - Azure Virtual Machines
description: NDv2 シリーズ VM の仕様。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 6c002838d5a4f515a594d61e5137196c4d391795
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605442"
---
# <a name="updated-ndv2-series-preview"></a>更新された NDv2 シリーズ (プレビュー)

NDv2 シリーズは、きわめて要求の厳しい GPU アクセラレーション AI、機械学習、シミュレーション、HPC ワークロードのニーズに合わせて設計された GPU ファミリに新たに追加された仮想マシンです。

NDv2 は、それぞれ 32 GB の GPU メモリを搭載した、NVIDIA Tesla V100 NVLINK 接続の GPU を 8 個備えています。 また、各 NDv2 VM には、ハイパースレッド非対応の Intel Xeon Platinum 8168 (Skylake) コアが 40 個と 672 GiB のシステム メモリが備わっています。

NDv2 インスタンスは、CUDA GPU 最適化計算カーネルと、GPU アクセラレーションを "標準" でサポートするさまざまな AI、ML、分析ツール (TensorFlow、Pytorch、Caffe、RAPIDS フレームワークなど) を活用して、HPC や AI ワークロードで優れたパフォーマンスを発揮します。

特筆すべき点として、NDv2 は、計算量の多いワークロードのスケールアップ (VM あたり 8 個の GPU を使用) とスケールアウト (複数の VM を連携) の両方に対応するように構築されています。 NDv2 シリーズは現在、HB シリーズの HPC VM と同様の 100 Gigabit InfiniBand EDR バックエンド ネットワークをサポートしており、ハイパフォーマンスのクラスタリングによって、AI と ML の分散トレーニングを含む並列シナリオに対応します。 このバックエンド ネットワークは、NVIDIA の NCCL2 ライブラリで採用されているプロトコルも含め、主要な InfiniBand プロトコルをすべてサポートしているため、GPU のシームレスなクラスタリングが実現します。


> [!NOTE]
> ND40rs_v2 VM で [InfiniBand を有効](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband)にする際は、4.7-1.0.0.1 Mellanox OFED ドライバーを使用してください。
>
> GPU メモリの増加により、新しい ND40rs_v2 VM では、[第 2 世代の VM](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) とマーケットプレース イメージを使用する必要があります。 
>
> [NDv2 仮想マシン プレビューをいち早くご利用になりたい方は、こちらからサインアップしてください](https://aka.ms/AzureNDrv2Preview)。
>
> 注意:GPU あたり 16 GB のメモリを備えた ND40s_v2 のプレビュー提供は終了し、最新の ND40rs_v2 に置き換えられました。

<br>

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

InfiniBand: サポートされています

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD): GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大ネットワーク帯域幅 | 最大 NIC 数 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000/800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Azure N シリーズ VM の GPU 機能を利用するには、NVIDIA GPU ドライバーをインストールする必要があります。

[NVIDIA GPU ドライバー拡張機能](/extensions/hpccompute-gpu-windows.md)は、N シリーズ VM 上に適切な NVIDIA CUDA または GRID ドライバーをインストールします。 この拡張機能は、Azure Portal または Azure PowerShell や Azure Resource Manager テンプレートなどのツールを使用してインストールまたは管理します。 サポートされるオペレーティング システムおよびデプロイ手順については、[NVIDIA GPU ドライバー拡張機能のドキュメント](/extensions/hpccompute-gpu-windows.md)を参照してください。 VM 拡張機能の一般情報については、「[Azure 仮想マシンの拡張機能と機能](/extensions/overview.md)」をご覧ください。

NVIDIA GPU ドライバーを手動でインストールすることを選択した場合、サポートされるオペレーティング システム、ドライバー、インストール、および検証の手順については、[Windows 用 N シリーズ GPU ドライバーのセットアップ](/windows/n-series-driver-setup.md)または [Linux 用 N シリーズ GPU ドライバーのセットアップ](/linux/n-series-driver-setup)に関する記事を参照してください。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
