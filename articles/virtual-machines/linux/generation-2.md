---
title: Azure の第 2 世代 VM (プレビュー) | Microsoft Docs
description: Azure 第 2 世代 VM の概要
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: lahugh
ms.openlocfilehash: e6bb947503371e379e4d4972ddfc3614e129174b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835205"
---
# <a name="generation-2-vms-preview-on-azure"></a>Azure の第 2 世代 VM (プレビュー)

> [!IMPORTANT]
> 第 2 世代 VM は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure で第 2 世代 VM のサポートがパブリック プレビュー段階になりました。 作成後、仮想マシンの世代を変更することはできません。 そのため、世代を選択する前に[こちら](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)にある考慮事項とこのページの情報を確認することをお勧めします。

第 2 世代 VM サポートの主な特徴: メモリの増加、Intel® Software Guard Extensions (SGX)、仮想永続メモリ (vPMEM)。これらは第 1 世代 VM ではサポートされていませんでした。 第 2 世代 VM には、まだ Azure でサポートされていない機能がいくつかあります。 詳細については、「[特徴と機能](#features-and-capabilities)」セクションを参照してください。 

第 2 世代 VM では、第 1 世代 VM で使用された BIOS ベースのブート アーキテクチャではなく、新しい UEFI ベースのアーキテクチャが使用されます。 第 1 世代と比較し、第 2 世代 VM では、起動時間とインストール時間が改善されることがあります。 第 2 世代 VM の概要と、第 1 世代と第 2 世代の主な違いについては、「[Hyper-V では、第 1 世代と第 2 世代の仮想マシンのどちらを作成するべきですか?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)」を参照してください。

## <a name="generation-2-vm-sizes"></a>第 2 世代 VM のサイズ

第 1 世代 VM は、Azure のすべての VM サイズでサポートされています。 Azure は現在、選ばれた次の VM シリーズに対してパブリック プレビュー段階で第 2 世代サポートを提供しています。

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) および [Dsv3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS シリーズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [Ls シリーズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series)と [Lsv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace の第 2 世代 VM のイメージ

第 2 世代 VM では、次の Azure Marketplace イメージがサポートされています。

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>オンプレミスと Azure 第 2 世代 VM

Azure では現在、第 2 世代 VM に対してオンプレミス Hyper-V でサポートされる一部の機能がサポートされています。

| 第 2 世代の機能                | オンプレミス Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| セキュア ブート                         | :heavy_check_mark:  | :x:   |
| シールドされた VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 仮想化ベースのセキュリティ (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 形式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特徴と機能

### <a name="generation-1-vs-generation-2-features"></a>第 1 世代と第 2 世代の特徴

| 機能 | 第 1 世代 | 第 2 世代 |
|---------|--------------|--------------|
| ブート             | PCAT                      | UEFI                               |
| ディスク コントローラー | IDE                       | SCSI                               |
| VM サイズ         | すべての VM サイズで利用可能 | Premium Storage でサポートされる VM のみ |

### <a name="generation-1-vs-generation-2-capabilities"></a>第 1 世代と第 2 世代の機能

| 機能 | 第 1 世代 | 第 2 世代 |
|------------|--------------|--------------|
| 2 TB より大きい OS ディスク                    | :x:                        | :heavy_check_mark: |
| カスタム ディスク/イメージ/スワップ OS         | :heavy_check_mark:         | :heavy_check_mark: |
| 仮想マシン スケール セットのサポート | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/バックアップ                        | :heavy_check_mark:         | :x:                |
| 共有イメージ ギャラリー              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>第 2 世代 VM の作成

### <a name="marketplace-image"></a>Marketplace イメージ

第 2 世代 VM は、Azure portal または Azure CLI を使用して (UEFI ブートをサポートする) Marketplace イメージから作成できます。

`windowsserver-gen2preview` サービスには、Windows 第 2 世代イメージのみが含まれます。 これにより、第 1 世代と第 2 世代のイメージの混同が回避されます。 第 2 世代 VM を作成するには、このサービスから **[イメージ]** を選択し、標準の VM 作成プロセスに従います。

現在のところ、次の Windows 第 2 世代イメージが Azure Marketplace で公開されています。

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

サポートされている Marketplace イメージの一覧については、「機能」セクションを参照してください。第 2 世代をサポートするイメージは引き続き追加されます。

### <a name="managed-image-or-managed-disk"></a>マネージド イメージまたはマネージド ディスク

第 2 世代 VM は、第 1 世代 VM を作成する場合と同じ方法でマネージド イメージまたはマネージド ディスクから作成できます。

### <a name="virtual-machine-scale-sets"></a>仮想マシン スケール セット

第 2 世代 VM は、仮想マシン スケール セットを利用して作成することもできます。 Azure CLI から Azure 仮想マシン スケール セットを利用し、第 2 世代 VM を作成できます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

* **第 2 世代 VM は高速ネットワークに対応していますか?**  
    はい。第 2 世代 VM は[高速ネットワーク](../../virtual-network/create-vm-accelerated-networking-cli.md)に対応しています。

* **.vhdx は第 2 世代でサポートされていますか?**  
    いいえ。第 2 世代 VM は .vhd にのみ対応しています。

* **第 2 世代 VM は Ultra ソリッド ステート ドライブ (SSD) をサポートしていますか?**  
    はい。第 2 世代 VM は Ultra SSD に対応しています。

* **第 1 世代 VM から第 2 世代 VM に移行できますか?**  
    いいえ。変更後、VM の世代を変更することはできません。 VM の世代を切り替える必要がある場合、別の世代で新しい VM を作成する必要があります。

## <a name="next-steps"></a>次の手順

* 詳細については、[Hyper-V の第 2 世代仮想マシン](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)に関するページを参照してください。