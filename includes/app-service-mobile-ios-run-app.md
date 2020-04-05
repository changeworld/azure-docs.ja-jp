---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240269"
---
1. Xcode を使用して、ダウンロードしたクライアント プロジェクトを開きます。

2. [Azure portal](https://portal.azure.com/) に移動し、作成したモバイル アプリに移動します。 `Overview` ブレードで、モバイル アプリのパブリック エンドポイントである URL を探します。 例 - アプリ名 "test123" のサイト名は https://test123.azurewebsites.net になります。

3. Swift プロジェクトの場合は、ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift フォルダーの `ToDoTableViewController.swift` ファイルを開きます。 アプリケーション名は `ZUMOAPPNAME` です。

4. `viewDidLoad()` メソッドで、`ZUMOAPPURL` パラメーターを上のパブリック エンドポイントに置き換えます。

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    →
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Objective-C プロジェクトの場合は、ZUMOAPPNAME/ZUMOAPPNAME フォルダーの `QSTodoService.m` ファイルを開きます。 アプリケーション名は `ZUMOAPPNAME` です。

6. `init` メソッドで、`ZUMOAPPURL` パラメーターを上のパブリック エンドポイントに置き換えます。

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    →
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. **[Run]** をクリックしてプロジェクトをビルドし、iOS シミュレーターでアプリを起動します。

8. アプリケーションでプラス アイコン ( **+** ) をクリックし、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、保存ボタンをクリックします。 これにより、事前にデプロイした Azure バックエンドに POST 要求が送信されます。 バックエンドは要求から取得したデータを TodoItem SQL テーブルに挿入し、新しく格納されたアイテムに関する情報をモバイル アプリに返します。 モバイル アプリでは、このデータが一覧に表示されます。

   ![iOS で実行されているクイック スタート アプリ](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
