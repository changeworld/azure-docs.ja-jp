---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240225"
---
1. クライアント プロジェクト (.sln) のソリューション ファイルに移動し、Visual Studio を使用してファイルを開きます。

2. Visual Studio で、開始用の矢印の横にあるドロップダウン リストからソリューション プラットフォーム (Android、iOS、または Windows) を選択します。 緑色の矢印のドロップダウン リストをクリックして、特定のデプロイ デバイスまたはエミュレーターを選択します。 既定の Android プラットフォームと Ripple エミュレーターを使用できます。 高度なチュートリアル (プッシュ通知など) では、サポートされているデバイスまたはエミュレーターを選択する必要があります。

3. ZUMOAPPNAME/app/src/main/java/com/example/zumoappname フォルダーの `ToDoActivity.java` ファイルを開きます。 アプリケーション名は `ZUMOAPPNAME` です。

4. [Azure portal](https://portal.azure.com/) に移動し、作成したモバイル アプリに移動します。 `Overview` ブレードで、モバイル アプリのパブリック エンドポイントである URL を探します。 例 - アプリ名 "test123" のサイト名は https://test123.azurewebsites.net になります。

5. ZUMOAPPNAME/www/js/index.js の `index.js` ファイル内の `onDeviceReady()` メソッドに移動し、`ZUMOAPPURL` パラメーターを上のパブリック エンドポイントに置き換えます。

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    →
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. F5 キーを押すか、緑色の矢印をクリックして、Cordova アプリをビルドして実行します。 ネットワークへのアクセスを要求するエミュレーターのセキュリティ ダイアログが表示された場合は、承認します。

7. デバイスまたはエミュレーターでアプリが起動したら、 **[Enter new text]\(新しいテキストを入力\)** に意味のあるテキスト ("*チュートリアルの完了*" など) を入力し、 **[追加]** をクリックします。

バックエンドは要求から取得したデータを SQL Database の TodoItem テーブルに挿入し、新しく格納されたアイテムに関する情報をモバイル アプリに返します。 モバイル アプリでは、このデータが一覧に表示されます。

他のプラットフォームを追加する際は、手順 3. ～ 5. を繰り返します。