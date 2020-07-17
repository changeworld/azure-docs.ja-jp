---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244668"
---
ここでは、書き込むことのできる Azure オブジェクトのサイズを示します。 アップロードするすべてのファイルがこれらの制限に準拠していることを確認してください。

| Azure オブジェクトの種類 | 既定の制限                                             |
|-------------------|-----------------------------------------------------------|
| ブロック BLOB        | ~ 4.75 TiB                                                 |
| ページ BLOB         | 8 TiB <br> ページ BLOB 形式でアップロードするファイルには 512 バイトをアライン (整数倍) する必要があります。そうでないと、アップロードが失敗します。 <br> VHD と VHDX には 512 バイトでアラインされます。 |
| Azure Files        | 1 TiB                                                      |
| マネージド ディスク     | 4 TiB <br> サイズと制限の詳細については、以下をご覧ください。 <li>[Standard SSD のスケーラビリティの目標](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Premium SSD のスケーラビリティの目標](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Standard HDD のスケーラビリティの目標](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[マネージド ディスクの価格と課金](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
