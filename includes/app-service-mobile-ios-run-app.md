---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 8146489a913ce863cee7534331231a248a3ea7ac
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56891006"
---
1. Mac で [Azure portal] にアクセスします。 **[すべてのサービス]** > **[App Services]** の順にクリックして、先ほど作成したバックエンドをクリックします。 モバイル アプリの設定で、優先する言語を選択します。

    - Objective-C &ndash; **[クイック スタート]** > **[iOS (Objective-C)]**
    - Swift &ndash; **[クイック スタート]** > **[iOS (Swift)]**

    **[3. クライアント アプリケーションの構成]** で、**[ダウンロード]** をクリックします。 これにより、バックエンドに接続するよう事前に構成された Xcode プロジェクト一式がダウンロードされます。 Xcode を使用してプロジェクトを開きます。

1. **[Run]** をクリックしてプロジェクトをビルドし、iOS シミュレーターでアプリを起動します。

1. アプリケーションでプラス アイコン (**+**) をクリックし、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、保存ボタンをクリックします。 これにより、事前にデプロイした Azure バックエンドに POST 要求が送信されます。 バックエンドは要求から取得したデータを TodoItem SQL テーブルに挿入し、新しく格納されたアイテムに関する情報をモバイル アプリに返します。 モバイル アプリでは、このデータが一覧に表示されます。

   ![iOS で実行されているクイック スタート アプリ](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure Portal]: https://portal.azure.com/
