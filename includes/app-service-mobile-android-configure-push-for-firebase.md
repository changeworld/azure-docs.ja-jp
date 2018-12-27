---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: bb03e4b5b04a0272d8fa9b032da5adb50878b620
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809443"
---
1. [Azure Portal](https://portal.azure.com/) で、**[すべて参照]** > **[App Services]** の順にクリックし、Mobile Apps バックエンドをクリックします。 **[設定]** で、**[App Service Push (App Service プッシュ)]**、通知ハブ名の順にクリックします。
2. **[Google (GCM)]** をクリックし、前の手順で Firebase から取得した **[サーバー キー]** の値を入力し、**[保存]** をクリックします。

    ![ポータルで API キーを設定する](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

これで、Mobile Apps バックエンドは、Firebase Cloud Messaging を使用するように構成されました。 これにより、通知ハブを使用して、Android デバイスで実行中のアプリにプッシュ通知を送信できます。
