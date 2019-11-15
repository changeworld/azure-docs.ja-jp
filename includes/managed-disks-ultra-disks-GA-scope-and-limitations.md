---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600974"
---
現時点では、Ultra Disk には次のような追加の制限があります。

- 次のリージョンでサポートされており、可用性ゾーンの数はリージョンごとに異なります。
    - 米国東部 2
    - East US
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
- Ultra Disk がサポートされているすべてのリージョンで、すべての VM サイズを使用できるわけではありません。
- データ ディスクとしてのみ使用可能であり、4k 物理セクター サイズのみをサポートしています  
- 空のディスクとしてのみ作成できます  
- ディスク スナップショット、VM イメージ、可用性セット、Azure Disk Encryption はまだサポートされていません
- Azure Backup または Azure Site Recovery との統合はまだサポートされていません。
- GA VM での IOPS の現在の上限は 80,000 です。