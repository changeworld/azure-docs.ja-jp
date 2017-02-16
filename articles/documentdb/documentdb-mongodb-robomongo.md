---
title: "MongoDB のプロトコル対応の Azure DocumentDB アカウントで Robomongo を使用する | Microsoft Docs"
description: "現在プレビューとして提供されている MongoDB のプロトコル対応の DocumentDB アカウントで Robomongo を使用する方法について説明します。"
keywords: robomongo
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a9d78597f708020eaec3fc03d8c8fc2652bddb50
ms.openlocfilehash: 6a7fb7052700bdeed5a3ca43da85304bba4230d8


---
# <a name="use-robomongo-with-a-documentdb-account-with-protocol-support-for-mongodb"></a>MongoDB のプロトコル対応の DocumentDB アカウントで Robomongo を使用する
Robomongo を使用して MongoDB のプロトコル対応の Azure DocumentDB アカウントに接続するには、次の要件を満たす必要があります。

* [Robomongo](https://robomongo.org/) をダウンロードしてインストールする
* MongoDB のプロトコル対応の DocumentDB アカウントに使用する [接続文字列](documentdb-connect-mongodb-account.md) 情報を取得する

## <a name="connect-using-robomongo"></a>Robomongo を使用して接続する
Robomongo MongoDB 接続に MongoDB のプロトコル対応の DocumentDB アカウントを追加するには、次の手順を実行します。

1. [ここ](documentdb-connect-mongodb-account.md)に記載されている手順に従って、MongoDB のプロトコル対応の DocumentDB に使用する接続情報を取得します。

    ![Screen shot of the connection string blade](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. *Robomongo.exe* を実行します

3. **[ファイル]** の接続ボタンをクリックして、接続を管理します。 次に、**[MongoDB Connections (MongoDB 接続)]** ウィンドウで **[作成]** をクリックし、**[接続の設定]** ウィンドウを開きます。

4. **[接続の設定]** ウィンドウで、名前を選択します。 次に、手順 1. の接続情報の **[ホスト]** と **[ポート]** に示されている**アドレス**と**ポート**をそれぞれ入力します。

    ![Robomongo 管理接続のスクリーンショット](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. **[認証]** タブで **[Perform authentication (認証の実行)]** をクリックします。 次に、データベース (既定値は*Admin*)、**ユーザー名**、**パスワード**を入力します。
この**ユーザー名**と**パスワード**は両方とも、手順 1 の接続情報に示されています。

    ![[Robomongo Authentication (Robomongo 認証)] タブのスクリーンショット](./media/documentdb-mongodb-robomongo/authentication.png)
6. **[SSL]** タブで、**[Use SSL protocol (SSL プロトコルを使用する)]** をオンにして、**[認証方法]** に **[自己署名証明書]** を指定します。

    ![[Robomongo SSL] タブのスクリーンショット](./media/documentdb-mongodb-robomongo/SSL.png)
7. 最後に、**[テスト]** をクリックして接続できることを確認し、**[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ
* MongoDB のプロトコル対応 DocumentDB の [サンプル](documentdb-mongodb-samples.md)を体験します。



<!--HONumber=Dec16_HO1-->


