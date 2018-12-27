---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: c86210208b9f747cbef1d9231528fa6cedbdb5d2
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42814999"
---
1. Windows ストア プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** をクリックしてから、F5 キーを押して Windows ストア アプリを実行します。

    アプリが起動すると、デバイスがプッシュ通知に登録されます。
2. Windows ストア アプリを停止し、Windows Phone ストア アプリに対して前の手順を繰り返します。

    この時点で、両方のデバイスがプッシュ通知を受信するように登録されています。

3. Windows ストア アプリを再度実行し、**[Insert a TodoItem (TodoItem の挿入)]** にテキストを入力してから、**[保存]** をクリックします。

    挿入が完了すると、Windows ストアと Windows Phone アプリの両方が WNS からのプッシュ通知を受信します。 通知は、アプリが実行していなくても Windows Phone に表示されます。

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
