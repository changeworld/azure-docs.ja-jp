---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240307"
---
1. **Android Studio** で **[Import project (Eclipse ADT, Gradle, etc.) (プロジェクトのインポート (Eclipse ADT、Gradle など))]** を使用して、プロジェクトを開きます。 JDK エラーを回避するために、このインポート オプションを必ず選択してください。

2. ZUMOAPPNAME/app/src/main/java/com/example/zumoappname フォルダーの `ToDoActivity.java` ファイルを開きます。 アプリケーション名は `ZUMOAPPNAME` です。

3. [Azure portal](https://portal.azure.com/) に移動し、作成したモバイル アプリに移動します。 `Overview` ブレードで、モバイル アプリのパブリック エンドポイントである URL を探します。 例 - アプリ名 "test123" のサイト名は https://test123.azurewebsites.net になります。

4. `onCreate()` メソッドで、`ZUMOAPPURL` パラメーターを上のパブリック エンドポイントに置き換えます。
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    →
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. **[アプリの実行]** を押してプロジェクトをビルドし、Android シミュレーターでアプリを開始します。

4. アプリで、意味のあるテキスト (たとえば、「 *チュートリアルの完了* 」) を入力し、[追加] ボタンをクリックします。 これにより、事前にデプロイした Azure バックエンドに POST 要求が送信されます。 バックエンドは要求から取得したデータを TodoItem SQL テーブルに挿入し、新しく格納されたアイテムに関する情報をモバイル アプリに返します。 モバイル アプリでは、このデータが一覧に表示されます。
    ![Android のクイック スタート](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)