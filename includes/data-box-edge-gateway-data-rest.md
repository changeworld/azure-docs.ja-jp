---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684470"
---
保存データの場合:

- 保存データの場合、BitLocker XTS AES-256 暗号化を使用して、ローカル データが保護されます。
- 共有内にあるデータの場合、共有へのアクセスを制限します。

    - 共有データにアクセスする SMB クライアントでは、共有に関連付けられているユーザーの資格情報が必要になります。 これらの資格情報は、共有の作成時点で定義されます。
    - 共有にアクセスする NFS クライアントでは、共有作成時に、クライアントの IP アドレスを追加する必要があります。