---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839767"
---
| ポート番号| インまたはアウト | ポート範囲| 必須| メモ |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|イン|LAN|はい|このポートは、HTTP 経由で Data Box Blog ストレージ REST API に接続するために使用されます。 REST API に接続していない場合は、8443 経由でローカル Web UI に自動的にリダイレクトされます。 |
| TCP 443 (HTTPS)|イン|LAN|はい|このポートは、HTTPS 経由で Data Box Blog ストレージ REST API に接続するために使用されます。 REST API に接続していない場合は、8443 経由でローカル Web UI に自動的にリダイレクトされます。 |
| TCP 8443 (HTTPS-Alt)|イン|LAN|はい|これは HTTPS 用の代替ポートで、デバイス管理のためにローカル Web UI に接続するときに使用されます。 |
| TCP 445 (SMB)|アウト/イン|LAN|場合により必須<br>メモを参照|このポートは、SMB を介して接続している場合にのみ必要です。 |
| TCP 2049 (NFS)|アウト/イン|LAN|場合により必須<br>メモを参照|このポートは、NFS を介して接続している場合にのみ必要です。 |
| TCP 111 (NFS)|アウト/イン|LAN|場合により必須<br>メモを参照|このポートは rpcbind/ポート マッピングに使用され、NFS 経由で接続している場合にのみ必要です。  |
