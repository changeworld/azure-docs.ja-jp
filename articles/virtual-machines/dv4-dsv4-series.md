---
title: Dv4 および Dsv4 シリーズ - Azure Virtual Machines
description: Dv4 および Dsv4 シリーズ VM の仕様。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: b86da0ddee576e654433b70cbb902daa6916c776
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401442"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 および Dsv4 シリーズ

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 柔軟なスケール セット :heavy_check_mark: 均一のスケール セット

Dv4 および Dsv4 シリーズは、ハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサ上で実行され、ほとんどの汎用ワークロードに対してより優れた価値提案が提供されています。 3\.4 GHz の全コア ターボ クロック速度を特徴とし、[Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)、[Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)、[Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) の機能を備えています。 また、[Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html) がサポートされています。 

> [!NOTE]
> よく寄せられる質問については、[ローカル一時ディスクを持たない Azure VM のサイズ](azure-vms-no-temp-disk.yml)に関する記事をご覧ください。

## <a name="dv4-series"></a>Dv4 シリーズ

Dv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Dv4 シリーズのサイズでは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、およびリモート ストレージ オプションの組み合わせが提供されます。 Dv4 シリーズ VM は [Intel&reg; ハイパースレッディング テクノロジ](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)を備えています。


リモート データ ディスク ストレージは、仮想マシンとは別に課金されます。 Premium Storage ディスクを使用するには、Dsv4 サイズを使用してください。 Dsv4 サイズの価格および課金の計算方法は、Dv4 シリーズと同じです。

> [!NOTE]
> 再起動後、*Data_loss_warning.txt* という名前のファイルが、ドライブ C (Azure portal からアタッチされた最初のデータ ディスク) の横に表示される場合があります。 このとき、ファイル名が示すようなデータの損失はディスクで発生していません。 *Data_loss_warning.txt* ファイルは通常、一時ドライブにコピーされます。 一時ドライブがない VM を使用している場合、WindowsAzureGuestAgent が誤って、ファイルを最初のドライブ文字にコピーしてしまいます。 v4 VM では、最初のドライブ文字はデータ ディスクです。
>
> この問題の解決策は、VM エージェントの最新バージョン (バージョン 2.7.41491.999) で適用されました。

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): サポートされていません<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされていません<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています <br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): サポートされています <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4<sup>1</sup> | 2 | 8 | リモート ストレージのみ | 4 | 2|5000 |
| Standard_D4_v4 | 4 | 16  | リモート ストレージのみ | 8 | 2|10000 |
| Standard_D8_v4 | 8 | 32 | リモート ストレージのみ | 16 | 4|12500 |
| Standard_D16_v4 | 16 | 64 | リモート ストレージのみ | 32 | 8|12500 |
| Standard_D32_v4 | 32 | 128 | リモート ストレージのみ | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | リモート ストレージのみ | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | リモート ストレージのみ | 32 | 8|30000 |

<sup>1</sup> 高速ネットワークは、1 つの NIC にのみ適用できます。 


## <a name="dsv4-series"></a>Dsv4 シリーズ

Dsv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Dv4 シリーズのサイズでは、ほとんどの運用環境のワークロードに適した vCPU、メモリ、およびリモート ストレージ オプションの組み合わせが提供されます。 Dsv4 シリーズ VM は [Intel&reg; ハイパースレッディング テクノロジ](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)を備えています。 リモート データ ディスク ストレージは、仮想マシンとは別に課金されます。

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされています<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされています<br>
[VM 世代サポート](generation-2.md): 第 1 世代と第 2 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
[入れ子になった仮想化](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization): サポートされています <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | バースト キャッシュが無効なディスクの最大スループット: IOPS/MBps<sup>1</sup> | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v4<sup>2</sup> | 2 | 8  | リモート ストレージのみ | 4 | 3200/48 | 4000/200 |2|5000 |
| Standard_D4s_v4 | 4 | 16 | リモート ストレージのみ | 8 | 6400/96 | 8000/200 |2|10000 |
| Standard_D8s_v4 | 8 | 32 | リモート ストレージのみ | 16 | 12800/192 | 16000/400 |4|12500 |
| Standard_D16s_v4 | 16 | 64  | リモート ストレージのみ | 32 | 25600/384 | 32000/800 |8|12500 |
| Standard_D32s_v4 | 32 | 128 | リモート ストレージのみ | 32 | 51200/768 | 64000/1600 |8|16000 |
| Standard_D48s_v4 | 48 | 192 | リモート ストレージのみ | 32 | 76800/1152 | 80000/2000 |8|24000 |
| Standard_D64s_v4 | 64 | 256 | リモート ストレージのみ | 32 | 80000/1200 | 80000/2000 |8|30000 |

<sup>1</sup>  Dsv4 シリーズの VM では、ディスクのパフォーマンスを[バースト](./disk-bursting.md)でき、一度に最大 30 分間バーストを最大にしておくことができます。<br>
<sup>2</sup> 高速ネットワークは、1 つの NIC にのみ適用できます。 

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
