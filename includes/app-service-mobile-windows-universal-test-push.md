---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 70e0d766fe3c9669912ce9f13f729fb1c757a26f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140481"
---
1. Windows ストア プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** をクリックしてから、F5 キーを押して Windows ストア アプリを実行します。

    アプリが起動すると、デバイスがプッシュ通知に登録されます。
2. Windows ストア アプリを停止し、Windows Phone ストア アプリに対して前の手順を繰り返します。

    この時点で、両方のデバイスがプッシュ通知を受信するように登録されています。

3. Windows ストア アプリを再度実行し、**[Insert a TodoItem (TodoItem の挿入)]** にテキストを入力してから、**[保存]** をクリックします。

    挿入が完了すると、Windows ストアと Windows Phone アプリの両方が WNS からのプッシュ通知を受信します。 通知は、アプリが実行していなくても Windows Phone に表示されます。

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
