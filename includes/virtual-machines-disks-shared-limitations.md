---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d2cf7dbcd97c8f740447607eaf443bc3ea4a6733
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500602"
---
一部の種類のディスクでのみ、共有ディスクを有効にできます。 現在のところ、Ultra ディスクと Premium SSD のみで共有ディスクを有効にできます。 共有ディスクが有効になっているマネージド ディスクはそれぞれ、次の制約を受けます。制限はディスクの種類別に整理されています。

### <a name="ultra-disks"></a>Ultra ディスク

Ultra ディスクには、共有ディスクとは関係なく、独自の制限リストがあります。 Ultra ディスクの制限については、「[Azure Ultra ディスクの使用](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)」を参照してください。

Ultra ディスクを共有するとき、次の追加制限があります。

- Azure Resource Manager または SDK サポートに現在、制限されています。 
- Windows Server フェールオーバー クラスターの一部のバージョンでは、ベーシック ディスクのみを使用できます。詳細については、「[フェールオーバー クラスタリングのハードウェア要件と記憶域オプション](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)」を参照してください。

### <a name="premium-ssds"></a>Premium SSD

- 現在、米国中西部リージョンのみでサポートされています。
- Azure Resource Manager または SDK サポートに現在、制限されています。 
- OS ディスクではなく、データ ディスクでのみ有効にすることができます。
- **ReadOnly** ホスト キャッシュは、`maxShares>1` の Premium SSD では使用できません。
- ディスク バーストは、`maxShares>1` の Premium SSD では使用できません。
- Azure 共有ディスクで可用性セットと仮想マシン スケール セットを使用している場合、仮想マシン障害ドメインによる[ストレージ障害ドメイン配置](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set)は共有データ ディスクには適用されません。
- [近接配置グループ (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md) を使用する場合は、ディスクを共有するすべての仮想マシンが同じ PPG に含まれている必要があります。
- Windows Server フェールオーバー クラスターの一部のバージョンでは、ベーシック ディスクのみを使用できます。詳細については、「[フェールオーバー クラスタリングのハードウェア要件と記憶域オプション](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)」を参照してください。
- Azure Backup および Azure Site Recovery のサポートは、まだ使用できません。

共有ディスクの使用に関心がある場合は、[サインアップしてアクセスしてください](https://aka.ms/AzureSharedDiskGASignUp)。