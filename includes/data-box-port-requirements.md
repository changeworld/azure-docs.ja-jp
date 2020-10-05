---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505905"
---
| ポート番号| インまたはアウト | ポート範囲| 必須| メモ |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|場所|LAN|はい|このポートは、HTTP 経由で Data Box BLOB ストレージ REST API に接続するために使用されます。 REST API に接続していない場合は、8443 経由でローカル Web UI に自動的にリダイレクトされます。 |
| TCP 443 (HTTPS)|場所|LAN|はい|このポートは、HTTPS 経由で Data Box BLOB ストレージ REST API に接続するために使用されます。 REST API に接続していない場合は、8443 経由でローカル Web UI に自動的にリダイレクトされます。 |
| TCP 8443 (HTTPS-Alt)|場所|LAN|はい|これは HTTPS 用の代替ポートで、デバイス管理のためにローカル Web UI に接続するときに使用されます。 |
| TCP 445 (SMB)|アウト/イン|LAN|場合により必須<br>メモを参照|このポートは、SMB を介して接続している場合にのみ必要です。 |
| TCP 2049 (NFS)|アウト/イン|LAN|場合により必須<br>メモを参照|このポートは、NFS を介して接続している場合にのみ必要です。 |
| TCP 111 (NFS)|アウト/イン|LAN|場合により必須<br>メモを参照|このポートは rpcbind/ポート マッピングに使用され、NFS 経由で接続している場合にのみ必要です。  |
