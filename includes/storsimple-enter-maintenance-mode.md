---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9bd2ee708f7d27cff5d07ca7f86d925ca6d2741d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165208"
---
<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>メンテナンス モードを開始するには
1. シリアル コンソール メニューで、オプション 1 を選択し、 **フル アクセスでログイン**します。
2. パスワードを入力します。 既定のパスワードは **Password1**です。
3. コマンド プロンプトに、次のコマンドを入力します。
   
     `Enter-HcsMaintenanceMode`
4. メンテナンス モードにすると、すべての I/O 要求が中断されAzure クラシック ポータルへの接続が切断されるという警告メッセージが表示され、確認が求められます。 メンテナンス モードを開始するには、「 **Y** 」と入力します。
   
    両方のコントローラーが再起動します。 再起動が完了すると、デバイスがメンテナンス モードであることを示すメッセージが表示されます。

