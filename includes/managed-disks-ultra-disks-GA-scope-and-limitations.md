---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008643"
---
現時点では、Ultra Disk には次のような追加の制限があります。

Ultra ディスクで現在利用できる結い位置のインフラストラクチャ冗長オプションは可用性ゾーンです。 他の冗長オプションを使用している VM では、Ultra ディスクを接続できません。

Ultra ディスクが利用できるリージョンとそれに該当する可用性オプションをまとめたものが次の表です。

> [!NOTE]
> これらのリージョンに含まれる一部の可用性ゾーンでは、Ultra ディスクが提供されていません。

|リージョン  |インフラストラクチャの冗長性なし  |可用性ゾーン  |
|---------|---------|---------|
|米国西部     |はい         |いいえ         |
|米国西部 2    |いいえ         |はい         |
|米国東部     |いいえ         |はい         |
|米国東部 2     |いいえ         |はい         |
|東南アジア     |いいえ         |はい         |
|北ヨーロッパ     |いいえ         |はい         |
|西ヨーロッパ     |いいえ         |はい         |
|英国南部     |いいえ         |はい         |

- 次の VM シリーズでのみサポートされています。
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ultra Disk がサポートされているすべてのリージョンで、すべての VM サイズを使用できるわけではありません
- データ ディスクとしてのみ使用可能であり、4k 物理セクター サイズのみをサポートしています。 Ultra Disk の 4K ネイティブ セクター サイズのために、Ultra Disk と互換性のないアプリケーションがいくつかあります。 1 つの例が、Oracle Database です。この場合、Ultra Disk をサポートするには、リリース 12.2 以降が必要です。  
- 空のディスクとしてのみ作成できます  
- ディスク スナップショット、VM イメージ、可用性セット、Azure 専用ホスト、Azure Disk Encryption は現在のところサポートされていません
- Azure Backup または Azure Site Recovery との統合は現在のところサポートされていません
- GA VM での IOPS の現在の上限は 80,000 です。

Azure Ultra ディスクからは、既定でサブスクリプション別にリージョンあたり最大 16 TiB が提供されますが、Ultra ディスクでは要求があればそれを超える容量が提供されます。 容量の増加を依頼するには、Azure サポートにお問い合わせください。