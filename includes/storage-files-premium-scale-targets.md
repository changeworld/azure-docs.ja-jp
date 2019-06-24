---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: c9561f868c70920a0ffb80777f1fc57abafb0df9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073744"
---
#### <a name="additional-premium-file-share-limits"></a>Premium ファイル共有の追加の制限

|領域  |ターゲット  |
|---------|---------|
|最小サイズ増減    |1 GiB      |
|ベースライン IOPS    |GiB あたり 1 IOPS (最大 100,000)|
|IOPS バースト    |GiB あたり 3x IOPS (最大 100,000)|
|エグレス レート         |60 MiB/s + 0.06 * プロビジョニング済み GiB        |
|イングレス レート| 40 MiB/s + 0.04 * プロビジョニング済み GiB |
|スナップショットの最大数        |200       |

#### <a name="premium-file-limits"></a>Premium ファイルの制限

|領域  |ターゲット  |
|---------|---------|
|Size                  |1 TiB         |
|ファイルあたりの最大 IOPS     |5,000         |
|コンカレント処理数    |2,000         |