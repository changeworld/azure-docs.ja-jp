---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008350"
---
プレビュー段階では、一部の種類のディスクでのみ、共有ディスクを有効にできます。 現在のところ、Ultra ディスクと Premium SSD のみで共有ディスクを有効にできます。 共有ディスクが有効になっているマネージド ディスクはそれぞれ、次の制約を受けます。制限はディスクの種類別に整理されています。

### <a name="ultra-disks"></a>Ultra ディスク

Ultra ディスクには、共有ディスクとは関係なく、独自の制限リストがあります。 Ultra ディスクの制限については、「[Azure Ultra ディスクの使用](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)」を参照してください。

Ultra ディスクを共有するとき、次の追加制限があります。

- 現在、米国西部 2 のみがサポートされています。
- Azure Resource Manager または SDK サポートに現在、制限されています。
- Windows Server フェールオーバー クラスターの一部のバージョンでは、ベーシック ディスクのみを使用できます。詳細については、「[フェールオーバー クラスタリングのハードウェア要件と記憶域オプション](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)」を参照してください。

### <a name="premium-ssds"></a>Premium SSD

- 現在、米国中西部リージョンのみでサポートされています。
- ディスクを共有するすべての仮想マシンは、同じ[近接通信配置グループ](../articles/virtual-machines/windows/proximity-placement-groups.md)にデプロイする必要があります。
- OS ディスクではなく、データ ディスクでのみ有効にすることができます。
- Windows Server フェールオーバー クラスターの一部のバージョンでは、ベーシック ディスクのみを使用できます。詳細については、「[フェールオーバー クラスタリングのハードウェア要件と記憶域オプション](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)」を参照してください。
- ReadOnly ホスト キャッシュは、`maxShares>1` の Premium SSD では使用できません。
- 可用性セットと仮想マシン スケール セットは、`FaultDomainCount` が 1 に設定されている場合にのみ使用できます。
- Azure Backup および Azure Site Recovery のサポートは、まだ使用できません。

共有ディスクの使用に関心がある場合は、[プレビューにサインアップ](https://aka.ms/AzureSharedDiskPreviewSignUp)します。
