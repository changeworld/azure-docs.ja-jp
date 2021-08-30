---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/16/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a2009a1898abd8390b4a747efb10db01964f55a7
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252579"
---
一部の種類のディスクでのみ、共有ディスクを有効にできます。 現在のところ、Ultra ディスク、Premium SSD、Standard SSD のみで共有ディスクを有効にできます。 共有ディスクが有効になっているマネージド ディスクはそれぞれ、次の制約を受けます。制限はディスクの種類別に整理されています。

### <a name="ultra-disks"></a>Ultra ディスク

Ultra ディスクには、共有ディスクとは関係なく、独自の制限リストがあります。 Ultra ディスクの制限については、「[Azure Ultra ディスクの使用](../articles/virtual-machines/disks-enable-ultra-ssd.md)」を参照してください。

Ultra ディスクを共有するとき、次の追加制限があります。

- Windows Server フェールオーバー クラスターの一部のバージョンでは、ベーシック ディスクのみを使用できます。詳細については、「[フェールオーバー クラスタリングのハードウェア要件と記憶域オプション](/windows-server/failover-clustering/clustering-requirements)」を参照してください。
- [サーバー側暗号化](../articles/virtual-machines/disk-encryption.md)のみがサポートされており、[Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) は現在サポートされていません。

共有 Ultra ディスクは、既定で、Ultra ディスクをサポートするすべてのリージョンで利用でき、それらを使用するためのアクセスに対してサインアップする必要はありません。

### <a name="premium-ssds"></a>Premium SSD

- OS ディスクではなく、データ ディスクでのみ有効にすることができます。
- **ReadOnly** ホスト キャッシュは、`maxShares>1` の Premium SSD では使用できません。
- ディスク バーストは、`maxShares>1` の Premium SSD では使用できません。
- Azure 共有ディスクで可用性セットと仮想マシン スケール セットを使用している場合、仮想マシン障害ドメインによる[ストレージ障害ドメイン配置](../articles/virtual-machines/availability.md)は共有データ ディスクには適用されません。
- [近接配置グループ (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md) を使用する場合は、ディスクを共有するすべての仮想マシンが同じ PPG に含まれている必要があります。
- Windows Server フェールオーバー クラスターの一部のバージョンでは、ベーシック ディスクのみを使用できます。詳細については、「[フェールオーバー クラスタリングのハードウェア要件と記憶域オプション](/windows-server/failover-clustering/clustering-requirements)」を参照してください。
- Azure Site Recovery のサポートは、まだ使用できません。
- Azure Backup は [Azure ディスク バックアップ](../articles/backup/disk-backup-overview.md) を通じて利用できます。
- [サーバー側暗号化](../articles/virtual-machines/disk-encryption.md)のみがサポートされており、[Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) は現在サポートされていません。

#### <a name="premium-ssd-regional-availability"></a>Premium SSD リージョンの可用性

すべての Premium SSD サイズの共有ディスクは、マネージド ディスクが使用可能なすべてのリージョンで利用できます。


### <a name="standard-ssds"></a>Standard SSD

- OS ディスクではなく、データ ディスクでのみ有効にすることができます。
- Azure 共有ディスクで可用性セットと仮想マシン スケール セットを使用している場合、仮想マシン障害ドメインによる[ストレージ障害ドメイン配置](../articles/virtual-machines/availability.md)は共有データ ディスクには適用されません。
- [近接配置グループ (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md) を使用する場合は、ディスクを共有するすべての仮想マシンが同じ PPG に含まれている必要があります。
- Windows Server フェールオーバー クラスターの一部のバージョンでは、ベーシック ディスクのみを使用できます。詳細については、「[フェールオーバー クラスタリングのハードウェア要件と記憶域オプション](/windows-server/failover-clustering/clustering-requirements)」を参照してください。
- Azure Site Recovery のサポートは、まだ使用できません。
- Azure Backup は [Azure ディスク バックアップ](../articles/backup/disk-backup-overview.md) を通じて利用できます。
- [サーバー側暗号化](../articles/virtual-machines/disk-encryption.md)のみがサポートされており、[Azure Disk Encryption](../articles/virtual-machines/windows/disk-encryption-overview.md) は現在サポートされていません。

#### <a name="standard-ssd-regional-availability"></a>Standard SSD リージョンの可用性

すべての Standard SSD サイズの共有ディスクは、マネージド ディスクが使用可能なすべてのリージョンで利用できます。
