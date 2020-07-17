---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67181672"
---
1. Visual Studio ソリューション エクスプローラーで、Windows Store アプリ プロジェクトを右クリックします。 **[ストア]**  >  **[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![アプリを Windows ストアと関連付ける](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. ウィザードで **[次へ]** を選択します。 次に、Microsoft アカウントを使ってサインインします。 **[新しいアプリケーション名の予約]** で、アプリの名前を入力し、 **[予約]** を選択します。
3. アプリの登録が正常に作成されたら、新しいアプリ名を選択します。 **[次へ]** 、 **[関連付け]** の順に選択します。 このプロセスにより、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。
4. Windows ストア アプリ用に以前に作成したものと同じ登録を使用して、Windows Phone ストア アプリ プロジェクトに対して手順 1. と 3. を繰り返します。  
5. [Windows デベロッパー センター](https://dev.windows.com/en-us/overview)に移動し、Microsoft アカウントでサインインします。 **[マイ アプリ]** で、新しいアプリの登録を選択します。 次に、 **[サービス]**  >  **[プッシュ通知]** の順に展開します。
6. **[プッシュ通知]** ページで、 **[Windows Push Notification Services (WNS) and Microsoft Azure Mobile Apps]\(Windows プッシュ通知サービス (WNS) と Microsoft Azure Mobile Apps\)** の **[Live Services site]\(Live サービス サイト\)** を選択します。  **[パッケージ SID]** の値と *[アプリケーション シークレット]* の "**現在**" の値を書き留めておきます。 

    ![デベロッパー センターでのアプリの設定](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > アプリケーション シークレットおよびパッケージ SID は、重要なセキュリティ資格情報です。 こうした値は、他のユーザーと共有したり、アプリケーションで配布したりしないでください。
   >
   >
