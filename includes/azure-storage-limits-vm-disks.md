---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 10599b47d55a0b4ea1eb79cda3c31a2479efb7c0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755595"
---
Azure 仮想マシンには複数のデータ ディスクを接続できます。 VM のデータ ディスクのスケーラビリティとパフォーマンスの目標に基づいて、パフォーマンスとキャパシティの要件を満たすために必要なディスクの数と種類を決定します。

> [!IMPORTANT]
> パフォーマンスを最適化するには、仮想マシンに接続する使用率が高いディスク数を制限して、スロットルを回避するようにします。 接続されているすべてのディスクの使用率が同時に高くならなければ、仮想マシンは多数のディスクに対応できます。

**Azure Managed Disks の場合:**

次の表は、サブスクリプションあたりのリージョンごとのリソース数の既定の制限と上限を示しています。 制限は、プラットフォーム マネージド キーまたはカスタマー マネージド キーで暗号化されたディスクに関係なく変わりません。 リソース グループあたりの Managed Disks、スナップショット、イメージの数に制限はありません。  

> | リソース | 制限 |
> | --- | --- |
> | Standard マネージド ディスク | 50,000 |
> | Standard SSD マネージド ディスク | 50,000 |
> | Premium マネージド ディスク | 50,000 |
> | Standard_LRS スナップショット | 50,000 |
> | Standard_ZRS スナップショット | 50,000 |
> | マネージド イメージ | 50,000 |

**Standard ストレージ アカウントの場合:** Standard ストレージ アカウントには、20,000 IOPS という最大合計要求レートがあります。 Standard ストレージ アカウントの仮想マシン ディスク全体の合計 IOPS は、この制限を超えることはできません。
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**Premium ストレージ アカウントの場合:** Premium ストレージ アカウントの最大合計スループット レートは 50 Gbps です。 すべての VM ディスク全体の合計スループットは、この制限を超えることはできません。

