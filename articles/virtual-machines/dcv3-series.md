---
title: DCsv3 および DCdsv3 シリーズ - Azure Virtual Machines
description: DCsv3 および DCdsv3 シリーズ VM の仕様。
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: mmcrey
ms.custom: ignite-fall-2021
ms.openlocfilehash: 09d4c88bec94c881fd9895557aa35264ea4ffab4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561216"
---
# <a name="dcsv3-and-dcdsv3-series"></a>DCsv3 および DCdsv3 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

> [!NOTE] 
> DCsv3 と DCdsv3 は、2021 年 11 月 1 日現在、パブリック プレビューの対象です

DCsv3 および DCdsv3 シリーズの仮想マシンは、パブリック クラウドで処理中のコードとデータの機密性と整合性を保護するために役立ちます。 Intel® Software Guard Extensions と Intel® Total Memory Encryption - Multi Keyを利用することで、使用中のデータが常に暗号化され保護されていることを確認できます。 

これらのマシンは、最新の第 3 世代 Intel® Xeon スケーラブル プロセッサを搭載し、Intel® Turbo Boost Max Technology 3.0 を活用して 3.5 GHz を実現しています。 

この世代では、CPU コアが 6 倍 (最大 48 個の物理コア)、暗号化メモリ (EPC) が 1500 倍の 256 GB に、標準メモリが 12 倍の 384 GB に増加しました。 これらすべての変更により、Gen-on-Gen のパフォーマンスが大幅に向上し、全く新しいシナリオが展開されます。 

> [!NOTE]
> ハイパースレッディングは、セキュリティの状態を追加する場合は無効になります。 価格は、物理コアと仮想コアでパフォーマンスが良いほうと、DC シリーズの独自のセキュリティ機能に基づいています。

ワークロードがローカル ディスクから恩恵を受けているかどうかに応じて、2 つのバリエーションを提供します。 ローカル ディスクを使用する VM を選択するしないにかかわらず、すべての VM にリモートの永続ディスク ストレージを接続できます。 リモート ディスク オプション (VM ブート ディスクなど) は、常に VM とは別に請求されます。 

## <a name="configuration"></a>構成

CPU: 第 3 世代 Intel® Xeon スケーラブル プロセッサ 8370C<br>
ベース オール コア周波数: 2.8 GHz<br>
[Turbo Boost Max 3.0](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html): 有効、最大周波数 3.5 GHz<br>
[ハイパー スレッディング](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html): サポートされていません<br>
[合計メモリ暗号化 - マルチキー](https://itpeernetwork.intel.com/memory-encryption/): 有効<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Ultra-Disk Storage](disks-enable-ultra-ssd.md): サポートされています<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md): サポートされています (CLI プロビジョニングまたは ARM テンプレートのみ)<br>
[Azure Kubernetes Service](../aks/intro-kubernetes.md): サポートされています (CLI のプロビジョニングは最初のみ)<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 2 世代<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>
[Dedicated Host](dedicated-hosts.md): 近日公開予定<br>

## <a name="dcsv3-series-technical-specifications"></a>DCsv3 シリーズの技術仕様

| サイズ             | 物理コア | メモリ (GB) | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 最大 NIC 数 |  EPC メモリ GB |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1s_v3 | 1              | 8           | 該当なし                    | 4              | 2     |  4                 |
| Standard_DC2s_v3 | 2              | 16          | 該当なし                    | 8              | 2     |  8                 |
| Standard_DC4s_v3 | 4              | 32          | 該当なし                    | 16             | 4     |  16                |
| Standard_DC8s_v3 | 8              | 64          | 該当なし                    | 32             | 8     |  32                |
| Standard_DC16s_v3  | 16           | 128         | 該当なし                    | 32             | 8     |  64                |
| Standard_DC24s_v3  | 24           | 192         | 該当なし                    | 32             | 8     |  128               |
| Standard_DC32s_v3  | 32           | 256         | 該当なし                    | 32             | 8     |  192               |
| Standard_DC48s_v3  | 48           | 384         | 該当なし                    | 32             | 8     |  256               |

## <a name="dcdsv3-series-technical-specifications"></a>DCdsv3 シリーズの技術仕様

| サイズ             | 物理コア | メモリ (GB) | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 最大 NIC 数 |  EPC メモリ GB |
|------------------|----------------|-------------|------------------------|----------------|---------|---------------------|
| Standard_DC1ds_v3 | 1              | 8           | 75                    | 4              | 2     |  4                 |
| Standard_DC2ds_v3 | 2              | 16          | 150                    | 8              | 2     |  8                 |
| Standard_DC4ds_v3 | 4              | 32          | 300                    | 16             | 4     |  16                |
| Standard_DC8ds_v3 | 8              | 64          | 600                    | 32             | 8     |  32                |
| Standard_DC16ds_v3  | 16           | 128         | 1200                    | 32             | 8     |  64                |
| Standard_DC24ds_v3  | 24           | 192         | 1800                    | 32             | 8     |  128               |
| Standard_DC32ds_v3  | 32           | 256         | 2400                    | 32             | 8     |  192               |
| Standard_DC48ds_v3  | 48           | 384         | 2400                    | 32             | 8     |  256               |

## <a name="get-started"></a>はじめに

- [Azure portal](./linux/quick-create-portal.md) を使用した DCsv3 と DCdsv3 VM の作成
- DCsv3 と DCdsv3 の VM は[第 2 世代の VM](./generation-2.md#creating-a-generation-2-vm) であり、`Gen2` イメージのみをサポートしています。
- 現在、「[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)」にリストされているリージョンで利用できます。

## <a name="more-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)
- [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

料金計算ツール:[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
