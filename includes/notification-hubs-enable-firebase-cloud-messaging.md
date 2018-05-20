---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4dc67175f2ccc569ab6dfe5338607fa6bebe3882
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
1. [Firebase コンソール](https://firebase.google.com/console/)にサインインします。 Firebase プロジェクトがまだない場合は、新しく作成します。
2. プロジェクトを作成した後、**[Add Firebase to your Android app]\(Android アプリに Firebase を追加する\)** を選択します。 次に、表示される手順に従います。 **google-services.json** ファイルをダウンロードします。 

    ![Android アプリへの Firebase の追加](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Firebase コンソールで、プロジェクトの歯車アイコンを選択します。 次に、**[Project Settings]\(プロジェクト設定\)** を選択します。

    ![[Project Settings]\(プロジェクト設定\) の選択](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. プロジェクト設定で **[General]\(一般\)** タブを選択します。 サーバーの API キーとクライアント ID が含まれた **google-services.json** ファイルをダウンロードします。
5. プロジェクト設定の **[Cloud Messaging]\(クラウド メッセージ\)** タブを選択し、**[Legacy server key]\(レガシ サーバー キー\)** の値をコピーします。 この値を使用して通知ハブのアクセス ポリシーを構成します。
