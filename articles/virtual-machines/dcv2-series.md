---
title: DCsv2 シリーズ - Azure Virtual Machines
description: DCsv2 シリーズ VM の仕様。
author: mmcrey
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.custom: ignite-fall-2021
ms.openlocfilehash: 54ae1665d34093fcb2ba44d0b5eb0f4f4a9bb0f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008476"
---
# <a name="dcsv2-series"></a>DCsv2 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

DCsv2 シリーズの仮想マシンは、パブリック クラウドで処理中のデータおよびコードの機密性と整合性を保護するために役立ちます。 DCsv2 シリーズでは、Intel® Software Guard Extensions を利用します。これにより、顧客は Secure Enclave をデータ保護に使用できます。

これらのマシンは、SGX テクノロジが搭載された 3.7 GHz Intel® Xeon E-2288G (Coffee Lake) によってサポートされています。 インテル® ターボ ブースト マックス テクノロジー 3.0 により、これらのマシンは最大 5.0 GHz まで高速化できます。 

> [!NOTE]
> ハイパースレッディングは、セキュリティの状態を追加する場合は無効になります。 価格は、物理コアと仮想コアでパフォーマンスが良いほうと、DC シリーズの独自のセキュリティ機能に基づいています。

機密の使用例としては、データベース、ブロックチェーン、マルチパーティ データ分析、不正行為の検出、マネー ロンダリング対策、利用状況分析、インテリジェンス分析、機械学習などがあります。

## <a name="configuration"></a>構成

[ターボ ブースト マックス 3.0](https://www.intel.com/content/www/us/en/gaming/resources/turbo-boost.html): サポートされています (テナント VM では 3.7 GHz が報告されますが、ターボ速度に達します)<br>
[ハイパー スレッディング](https://www.intel.com/content/www/us/en/gaming/resources/hyper-threading.html): サポートされていません<br>
[Premium Storage](premium-storage-performance.md): サポートされています (Standard_DC8_v2 ではサポートされていません)<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
[VM 世代サポート](generation-2.md): 第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされていません<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされています <br>

## <a name="technical-specifications"></a>技術仕様

| サイズ             | 物理コア | メモリ GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) | EPC メモリ MiB |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2,000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4,000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8,000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16,000/128                                                                                         | 2   | 168                                         |


## <a name="get-started"></a>はじめに

- [Azure portal](./linux/quick-create-portal.md) または [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) を使用して DCsv2 VM を作成します
- DCsv2 シリーズの VM は[第 2 世代の VM](./generation-2.md#creating-a-generation-2-vm) であり、`Gen2` イメージのみをサポートしています。
- 現在、「[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)」にリストされているリージョンで利用できます。

## <a name="more-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)
- [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
- [ディスクの種類の詳細](./disks-types.md#ultra-disks)

料金計算ツール:[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
