---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935767"
---
現時点では、Ultra Disk には次のような追加の制限があります。

- 次のリージョンでサポートされており、可用性ゾーンの数はリージョンごとに異なります。
    - 米国東部 2
    - 米国東部
    - 米国西部 2
    - 東南アジア
    - 北ヨーロッパ
    - 西ヨーロッパ
    - 英国南部 
- 可用性ゾーンでのみ使用できます (ゾーン外の可用性セットと単一の VM デプロイでは、Ultra ディスクを接続できません)
- 次の VM シリーズでのみサポートされています。
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ultra Disk がサポートされているすべてのリージョンで、すべての VM サイズを使用できるわけではありません
- データ ディスクとしてのみ使用可能であり、4k 物理セクター サイズのみをサポートしています。 Ultra Disk の 4K ネイティブ セクター サイズのために、Ultra Disk と互換性のないアプリケーションがいくつかあります。 1 つの例が、Oracle Database です。この場合、Ultra Disk をサポートするには、リリース 12.2 以降が必要です。  
- 空のディスクとしてのみ作成できます  
- ディスク スナップショット、VM イメージ、可用性セット、Azure Disk Encryption はまだサポートされていません
- Azure Backup または Azure Site Recovery との統合はまだサポートされていません。
- GA VM での IOPS の現在の上限は 80,000 です。
- Ultra Disk で 16 万 IOPS を実現できる VM の限定プレビューに参加する場合は、UltraDiskFeedback@microsoft.com に電子メールを送信してください。