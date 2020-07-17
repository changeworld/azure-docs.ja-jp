---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67181202"
---
#### <a name="to-enter-maintenance-mode"></a>メンテナンス モードを開始するには
1. シリアル コンソール メニューで、オプション 1 を選択し、 **フル アクセスでログイン**します。
2. パスワードを入力します。 既定のパスワードは **Password1** です。
3. コマンド プロンプトに、次のコマンドを入力します。
   
     `Enter-HcsMaintenanceMode`
4. メンテナンス モードにすると、すべての I/O 要求が中断されAzure クラシック ポータルへの接続が切断されるという警告メッセージが表示され、確認が求められます。 メンテナンス モードを開始するには、「 **Y** 」と入力します。
   
    両方のコントローラーが再起動します。 再起動が完了すると、デバイスがメンテナンス モードであることを示すメッセージが表示されます。

