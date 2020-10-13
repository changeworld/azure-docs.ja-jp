---
title: NCas T4 v3 シリーズ
description: NCas T4 v3 シリーズ VM の仕様。
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: 7c3423e8bd5cd3e38c05205842845bec2243ed83
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653504"
---
# <a name="ncast4_v3-series-in-preview"></a>NCasT4_v3 シリーズ (プレビュー段階) 

NCasT4_v3 シリーズ仮想マシンは、[Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU と AMD EPYC 7V12(Rome) CPU を搭載しています。 この VM には、最大 4 個の NVIDIA T4 GPU (それぞれに 16 GB のメモリを装備)、最大 64 個の非マルチスレッド AMD EPYC 7V12 (Rome) プロセッサ コア、440 GiB のシステム メモリが搭載されています。 これらの仮想マシンは、AI サービス (ユーザーが生成した要求のリアルタイム推論など) のデプロイや、NVIDIA の GRID ドライバーと仮想 GPU テクノロジを使用した対話型グラフィックスと視覚化ワークロードに最適です。 CUDA、TensorRT、Caffe、ONNX、およびその他のフレームワークをベースにした標準的な GPU コンピューティング ワークロードや、OpenGL と DirectX をベースにした GPU アクセラレーションを使用するグラフィック アプリケーションは、ユーザーのすぐ近くにある NCasT4_v3 シリーズに経済的にデプロイできます。

> [!NOTe]
> プレビュー プログラムに参加するための[要求を送信](https://aka.ms/NCT4v3Preview)してください。

<br>

[ACU](acu.md):230-260<br>
[Premium Storage](premium-storage-performance.md):サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md):サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md):サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md):サポートされていません<br>
[VM 世代サポート](generation-2.md):第 1 世代<br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Windows または Linux を実行している Azure NCasT4_v3 シリーズの GPU 機能を利用するには、Nvidia GPU ドライバーがインストールされている必要があります。

Nvidia GPU ドライバーを手動でインストールする場合、サポートされるオペレーティング システム、ドライバー、インストール、および検証手順について、[Windows 用 N シリーズ GPU ドライバーのセットアップ](./windows/n-series-driver-setup.md)に関する記事を参照してください。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
