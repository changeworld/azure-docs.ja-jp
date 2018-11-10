---
title: Avere vFXT for Azure のポート ホワイトリスト
description: Avere vFXT for Azure で使用されるポートの一覧
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 394415ffc7593d058d8d0bf1c0040b88ec25073b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669929"
---
# <a name="required-ports"></a>必須ポート

このセクションに一覧されているポートは、vFXT の受信と送信に使用されます。

vFXT クラスターまたはクラスター コントローラーのインスタンスを直接パブリック インターネットに公開しないでください。

## <a name="api"></a>API

| 受信 | | |
| --- | ---- | --- |
| TCP | 22  | SSH  |
| TCP | 443 | HTTPS|



| 送信 |     |       |
|----------|-----|-------|
| TCP      | 443 | HTTPS |

 
## <a name="nfs"></a>NFS

| 受信および送信  | | |
| --- | --- | ---|
| TCP/UDP | 111  | RPCBIND  |
| TCP/UDP | 2049 | NFS      |
| TCP/UDP | 4045 | NLOCKMGR |
| TCP/UDP | 4046 | MOUNTD   |
| TCP/UDP | 4047 | 状態   |

