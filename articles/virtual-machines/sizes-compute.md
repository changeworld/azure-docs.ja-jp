---
title: Azure VM のサイズ - コンピューティング最適化済み |Microsoft Docs
description: Azure の仮想マシンで使用できるさまざまなコンピューティング最適化済みのサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: edc1dbd3d0c2250bf2dd4db870a9813602477073
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688647"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>コンピューティング最適化済み仮想マシンのサイズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

> [!TIP]
> **[仮想マシン セレクター ツール](https://aka.ms/vm-selector)** を使って、ワークロードに最適な他のサイズを見つけてください。

コンピューティング最適化 VM サイズは高い CPU 対メモリ比を備えています。 これらのサイズは、中程度のトラフィックの Web サーバー、ネットワーク アプライアンス、バッチ プロセス、およびアプリケーション サーバーに適しています。 この記事では、vCPU、データ ディスク、および NIC の数に関する情報を提供します。 また、このグループ内のサイズごとのストレージ スループットとネットワーク帯域幅に関する情報も含まれています。

- [Fsv2 シリーズ](fsv2-series.md) は、第 2 世代 Intel® Xeon® Platinum 8272CL (Cascade Lake) プロセッサと Intel® Xeon® Platinum 8168 (Skylake) プロセッサ上で実行されます。 これは、持続する 3.4 GHz の全コア ターボ クロック速度と 3.7 GHz の最大シングルコア ターボ周波数を備えています。 Intel® AVX-512 命令は、Intel スケーラブル プロセッサでの新機能です。 これらの命令は、単精度浮動小数点演算と倍精度浮動小数点演算の両方でベクトル処理ワークロードに最大 2 倍のパフォーマンス向上を提供します。 つまり、これらは、あらゆるコンピューティング ワークロードで実際に高速です。 Fsv2 シリーズは時間あたりの料金が抑えられており、vCPU あたりの Azure コンピューティング ユニット (ACU) に基づく Azure ポートフォリオにおいて、最もコスト パフォーマンスに優れています。

- [FX シリーズ](fx-series.md)は、Intel® Xeon® Gold 6246R (Cascade Lake) プロセッサで実行されます。 これは、4.0 GHz の全コア ターボ周波数、仮想 CPU あたり 21 GB の RAM、最大 1 TB の合計 RAM、ローカル一時ストレージを備え、 高い CPU クロック速度、高いメモリ対 CPU 比を必要とするワークロード、コア ベースのライセンス コストが高いワークロード、優れたシングル コア パフォーマンスを必要とするアプリケーションに役立ちます。 FX シリーズの一般的なユース ケースは、Electronic Design Automation (EDA) ワークロードです。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。

Azure で、その VM の名前付けが行われる方法の詳細については、「[Azure 仮想マシンのサイズの名前付け規則](./vm-naming-conventions.md)」を参照してください。
