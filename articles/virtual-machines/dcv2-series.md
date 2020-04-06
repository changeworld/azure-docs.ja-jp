---
title: DC シリーズ - Azure Virtual Machines
description: DC シリーズ VM の仕様。
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 7834c8a32d4d85fc354bac209e13f19f3b8315fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256931"
---
# <a name="preview-dcsv2-series"></a>プレビュー:DCsv2 シリーズ


DCsv2 シリーズは、パブリック クラウドで処理中のデータおよびコードの機密性と整合性を保護するために役立ちます。 これらのマシンは、最新世代の Intel XEON E-2288G プロセッサと SGX テクノロジによって支援されています。 Intel Turbo Boost Technology により、これらのマシンは最大 5.0 GHz まで高速化できます。 DCsv2 シリーズのインスタンスを使用すると、安全なエンクレーブ ベースのアプリケーションを構築して、コードやデータを使用中に保護することができます。

使用例としては、機密のマルチパーティ データ共有、不正行為の検出、マネー ロンダリング対策、ブロックチェーン、機密の利用状況分析、インテリジェンス分析、機密の機械学習などがあります。

Premium Storage: サポート対象*

Premium Storage キャッシュ:サポート対象*

ライブ移行:サポートされていません

メモリ保持更新: サポートされていません

*Standard_DC8_v2 を除く



| サイズ             | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- DCsv2 シリーズの VM は、[第 2 世代の VM](./linux/generation-2.md#creating-a-generation-2-vm) であり、`Gen2` イメージのみをサポートしています。
- 現在、英国南部およびカナダ中部のみでご利用いただけます。
- 前世代の機密コンピューティング VM:[DC シリーズ](sizes-previous-gen.md)
- DCsv2 VM は Azure Portal を使用して作成します (「[VM の作成 - ポータル](./linux/quick-create-portal.md)」)



## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
