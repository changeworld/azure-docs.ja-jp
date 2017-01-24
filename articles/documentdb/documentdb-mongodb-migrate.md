---
title: "MongoDB のプロトコル対応の Azure DocumentDB アカウントでデータを移行する | Microsoft Docs"
description: "mongoimport と mongorestore を使用して、現在プレビューとして提供されている MongoDB のプロトコル対応の DocumentDB アカウントにデータをインポートする方法について説明します。"
keywords: migrate
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
ms.date: 12/07/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 2dc98400ed4eaa2263d73f0718f5eaee260d48f5
ms.openlocfilehash: 80665bc84948696b05b04536a066ebebb5a81e89


---
# <a name="migrate-data-to-documentdb-with-protocol-support-for-mongodb"></a>MongoDB のプロトコル対応 DocumentDB にデータを移行する
MongoDB のプロトコル対応の Azure DocumentDB アカウントにデータを移行するには、以下を実行する必要があります。

* *mongoimport.exe* または *mongorestore.exe* のいずれかを [MongoDB Download Center](https://www.mongodb.com/download-center) からダウンロードします。
* [MongoDB 対応 DocumentDB 接続文字列](documentdb-connect-mongodb-account.md)を取得します。

## <a name="before-you-begin"></a>開始する前に

* スループットを上げる: データの移行にかかる時間は、コレクションに対して設定したスループットの量に依存します。 大規模なデータ移行では、スループットが上がっていることを確認します。 移行が完了したら、コストを節約するためにスループットを下げます。 [Azure Portal](https://portal.azure.com) でスループットを上げることの詳細については、「[DocumentDB のパフォーマンス レベルと価格レベル](documentdb-performance-levels.md)」を参照してください。

* SSL を有効にする: DocumentDB には、厳密なセキュリティ要件と基準が存在します。 アカウントを操作するときは、SSL が有効になっていることを確認してください。 この記事で説明する手順の中に、*mongoimport* と *mongorestore*で SSL を有効にする方法が含まれています。

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>接続文字列情報 (ホスト、ポート、ユーザー名、およびパスワード) を見つける

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**[NoSQL (DocumentDB)]** エントリをクリックします。
2. **[サブスクリプション]** ウィンドウで、自分のアカウント名を選択します。
3. **[接続文字列]** ブレードで、**[接続文字列]** をクリックします。  
右側のウィンドウに、自分のアカウントに正常に接続するために必要なすべての情報が表示されます。

    ![[接続文字列] ブレード](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-documentdb-with-protocol-support-for-mongodb-with-mongoimport"></a>mongoimport を使用して MongoDB のプロトコル対応 DocumentDB にデータをインポートする

DocumentDB アカウントにデータをインポートするには、次のテンプレートを使用してインポートを実行します。 *ホスト*、*ユーザー名*および*パスワード*には、自分のアカウントに固有の値を入力します。  

テンプレート:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

例:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-documentdb-with-protocol-support-for-mongodb-with-mongorestore"></a>mongorestore を使用して MongoDB のプロトコル対応 DocumentDB にデータをインポートする

DocumentDB アカウントにデータを復元するには、次のテンプレートを使用してインポートを実行します。 *ホスト*、*ユーザー名*および*パスワード*には、自分のアカウントに固有の値を入力します。

テンプレート:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

例:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>次のステップ
* 詳細については、「[DocumentDB における MongoDB のプロトコル対応の例](documentdb-mongodb-samples.md)」を参照してください。



<!--HONumber=Dec16_HO3-->


