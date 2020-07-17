---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "67181680"
---
1. [Azure Portal](https://portal.azure.com/) で、 **[すべて参照]**  >  **[App Services]** の順にクリックし、Mobile Apps バックエンドをクリックします。 **[設定]** で、 **[App Service Push (App Service プッシュ)]** 、通知ハブ名の順にクリックします。
2. **[Google (GCM)]** をクリックし、前の手順で Firebase から取得した **[サーバー キー]** の値を入力し、 **[保存]** をクリックします。

    ![ポータルで API キーを設定する](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

これで、Mobile Apps バックエンドは、Firebase Cloud Messaging を使用するように構成されました。 これにより、通知ハブを使用して、Android デバイスで実行中のアプリにプッシュ通知を送信できます。
