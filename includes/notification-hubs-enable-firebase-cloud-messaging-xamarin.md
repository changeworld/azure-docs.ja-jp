---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "68728853"
---
1. [Firebase コンソール](https://firebase.google.com/console/)にサインインします。 Firebase プロジェクトがまだない場合は、新しく作成します。
2. プロジェクトを作成した後、 **[Add Firebase to your Android app]\(Android アプリに Firebase を追加する\)** を選択します。 

    ![Android アプリへの Firebase の追加](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. **[Android アプリへの Firebase の追加]** ページで、次の手順を実行します。 
    1. **[Android パッケージ名]** に、お使いのパッケージの名前を入力します。 (例: `tutorials.tutoria1.xamarinfcmapp`)。 

        ![パッケージ名を指定する](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. **[アプリの登録]** を選択します。  
    1. **[google-services.json のダウンロード]** を選択します。 次に、プロジェクトのフォルダーにファイルを保存し、 **[次へ]** を選択します。 まだ Visual Studio プロジェクトを作成していない場合は、プロジェクトの作成後にこの手順を実行できます。 

        ![google-services.json をダウンロードする](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. **[次へ]** を選択します。 
    7. **[この手順をスキップする]** を選択します。 

        ![最後の手順をスキップする](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Firebase コンソールで、プロジェクトの歯車アイコンを選択します。 次に、 **[Project Settings]\(プロジェクト設定\)** を選択します。

    ![[Project Settings]\(プロジェクト設定\) の選択](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. **google-services.json** ファイルをダウンロードしていない場合は、このページでダウンロードできます。 

    ![[全般] タブから google services. json をダウンロードする](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. 上部にある **[クラウド メッセージング]** タブに切り替えます。 後で使用するために、**サーバー キー**をコピーし、保存します。 この値を使用して通知ハブを構成します。

    ![サーバー キーのコピー](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
