---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 0ed42e4ace17db1e681152589cc46d82c26dddff
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
## <a name="set-up-your-project"></a>プロジェクトの設定

代わりに、このサンプルの Android Studio プロジェクトをダウンロードすることもできます。 [プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)したら、[構成手順](#register-your-application)に進んでサンプル コードを構成してから実行してください。

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する 
1.  Android Studio を開き、**[File]\(ファイル\)** > **[New]\(新規\)** > **[New Project]\(新しいプロジェクト\)** を選択します。
2.  アプリケーションに名前を付け、**[Next]\(次へ\)** をクリックします。
3.  **API 21 以降 (Android 5.0)** を選択し、**[Next]\(次へ\)** をクリックします。
4.  **[Empty Activity]\(空のアクティビティ\)** をそのままにして、**[Next]\(次へ\)** をクリックし、**[Finish]\(完了\)** をクリックします。


### <a name="add-msal-to-your-project"></a>プロジェクトに MSAL を追加する
1.  Android Studio で、**[Gradle Scripts]\(Gradle スクリプト\)** > **build.gradle (Module: app)** を選択します。
2.  **[Dependencies]\(依存関係\)** で、次のコードを貼り付けます。

    ```ruby  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>このパッケージについて

上記のコードのパッケージによって、Microsoft Authentication Library がインストールされます。 MSAL は、Azure Active Directory v2 エンドポイントで保護されている API へのアクセスに使用するユーザー トークンの取得、キャッシュ、更新を処理します。
<!--end-collapse-->

## <a name="create-the-application-ui"></a>アプリケーション UI を作成する

1. **[res]** > **[layout]** に移動し、**activity_main.xml** を開きます。 
2. アクティビティのレイアウトを `android.support.constraint.ConstraintLayout` などから `LinearLayout` に変更します。
3. `android:orientation="vertical"` プロパティを `LinearLayout` ノードに追加します。
4. `LinearLayout` ノードに次のコードを貼り付け、現在の内容を置き換えます。

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
