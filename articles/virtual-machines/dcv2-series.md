---
title: DC シリーズ - Azure Virtual Machines
description: DC シリーズ VM の仕様。
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: fd8f01f0fc7180d271404ffee4496ff9cbac7222
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205927"
---
# <a name="preview-dcv2-series"></a>プレビュー:DCv2 シリーズ


DCv2 シリーズは、パブリック クラウドで処理中のデータおよびコードの機密性と整合性を保護するために役立ちます。 これらのマシンは、最新世代の Intel XEON E-2288G プロセッサと SGX テクノロジによって支援されています。 Intel Turbo Boost Technology により、これらのマシンは最大 5.0 GHz まで高速化できます。 DCv2 シリーズのインスタンスを使用すると、安全なエンクレーブ ベースのアプリケーションを構築して、コードやデータを使用中に保護することができます。

使用例としては、機密のマルチパーティ データ共有、不正行為の検出、マネー ロンダリング対策、ブロックチェーン、機密の利用状況分析、インテリジェンス分析、機密の機械学習などがあります。

Premium Storage: サポート対象*

Premium Storage キャッシュ:サポート対象*

ライブ移行:サポートされていません

メモリ保持更新:サポートされていません

*Standard_DC8_v2 を除く



| サイズ             | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- DCv2 シリーズの VM は、[第 2 世代の VM](./linux/generation-2.md#creating-a-generation-2-vm) であり、`Gen2` イメージのみをサポートしています。
- 現在英国南部でのみ使用できます。
- 前世代の機密コンピューティング VM:[DC シリーズ](sizes-previous-gen.md)
- Azure Portal を使用する DCv2 VM の作成 [VM の作成 - ポータル](./linux/quick-create-portal.md)



## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
