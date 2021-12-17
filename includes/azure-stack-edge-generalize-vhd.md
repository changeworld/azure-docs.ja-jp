---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/18/2021
ms.openlocfilehash: 95604a5b7d0bc62568e0e941e5cf035c956f141b
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968454"
---
1. VM 内でコマンド プロンプトを開きます。

1. 次のコマンドを実行して VHD を一般化します。 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    詳しくは、[Sysprep (システム準備) の概要](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)に関するページをご覧ください。

1.  コマンドが完了すると、VM はシャットダウンされます。 **VM は再起動しないでください**。
