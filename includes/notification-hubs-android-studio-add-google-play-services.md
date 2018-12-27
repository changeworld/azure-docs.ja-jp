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
ms.openlocfilehash: 540c9775ae56798ce2d2d87fed4924244c31412f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33835767"
---
1. Android Studio のツールバーのアイコンをクリックするか、またはメニューから **[ツール]**  >  **[Android]**  >  **[SDK マネージャー]** を順にクリックして、Android SDK マネージャーを開きます。 まだインストールしていない場合は、プロジェクトで使用する目的のバージョンの Android SDK を探し、**[Show Package Details]\(パッケージの詳細を表示\)** をクリックして開き、**[Google APIs]\(Google API\)** をオンにします。
2. **[SDK Tools]** タブをクリックします。Google Play Services をまだインストールしていない場合は、次に示すように **[Google Play Services]** をクリックします。 次に、 **[適用]** をクリックしてインストールします。 
   
    SDK のパスは後の手順で使用するので、メモしておいてください。 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. アプリ ディレクトリの **build.gradle** ファイルを開きます。
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. 次の行を *dependencies*の下に追加します。 
    
    ```java
        compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. ツール バーで **[Sync Project with Gradle Files]** をクリックします。
6. **AndroidManifest.xml** を開き、このタグを *application* タグに追加します。
   
    ```java
    <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
    ```

