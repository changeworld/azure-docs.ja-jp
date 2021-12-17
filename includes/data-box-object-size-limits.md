---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 2978fff57cfc7ae3dd4629450f573758978058d5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130077968"
---
ここでは、書き込むことのできる Azure オブジェクトのサイズを示します。 アップロードするすべてのファイルがこれらの制限に準拠していることを確認してください。

| Azure オブジェクトの種類 | 既定の制限                                             |
|-------------------|-----------------------------------------------------------|
| ブロック BLOB        | ~ 4.75 TiB                                                 |
| ページ BLOB         | 8 TiB <br> ページ BLOB 形式でアップロードするファイルには 512 バイトをアライン (整数倍) する必要があります。そうでないと、アップロードが失敗します。 <br> VHD と VHDX には 512 バイトでアラインされます。 |
| Azure Files        | 1 TiB                                                      |
| マネージド ディスク     | 4 TiB <br> サイズと制限の詳細については、以下をご覧ください。 <li>[Standard SSD のスケーラビリティの目標](../articles/virtual-machines/disks-types.md#standard-ssds)</li><li>[Premium SSD のスケーラビリティの目標](../articles/virtual-machines/disks-types.md#standard-hdds)</li><li>[Standard HDD のスケーラビリティの目標](../articles/virtual-machines/disks-types.md#premium-ssds)</li><li>[マネージド ディスクの価格と課金](../articles/virtual-machines/disks-types.md#billing)</li>  
