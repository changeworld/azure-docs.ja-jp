---
title: Microsoft Excel で Azure Blockchain Workbench データを使用する
description: Microsoft Excel で Azure Blockchain Workbench SQL DB のデータを読み込んで表示する方法を説明します。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: e8c20f4b8e39615e2a8c486130d7c8bec655a936
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Microsoft Excel で Azure Blockchain Workbench データを表示する

Microsoft Excel を使用して、Azure Blockchain Workbench の SQL DB のデータを表示することができます｡ この記事では､次のことを行うために必要な手順を説明してています｡

* Microsoft Excel から Blockchain Workbench データベースに接続する
* Blockchain Workbench データベースのテーブルとビューを表示する
* Blockchain Workbench のビューデータを Excel に読み込む

## <a name="connect-to-the-blockchain-workbench-database"></a>Blockchain Workbench データベースに接続する

Blockchain Workbench データベースに接続する手順は次の通りです｡

1. Microsoft Excel を開きます。
2. **データ** タブで **Get Data** を選択します｡
3. **From Azure** を選択して､**From Azure SQL Database** を選択します｡

   ![Azure SQL Database に接続する](media/blockchain-workbench-data-excel/connect-sql-db.png)

4. **SQL Server database** ダイアログ ボックスで以下の操作を行います｡

    * **Server** の場合は､Blockchain Workbench サーバー名を入力します｡
    * **Database (オプション)** の場合は､データベース名を入力します｡

   ![データベース サーバーとデータベースを指定する](media/blockchain-workbench-data-excel/provide-server-db.png)

5. **SQL Server database** ダイアログ ボックスのナビゲーション バーで **Database** を選択します｡ **Username** と **Password** を入力して､**Connect** を選択します｡

    > [!NOTE]
    > Azure Blockchain Workbench のデプロイ プロセスで作成した資格情報を使用する場合､**User name** は `dbadmin` です｡ **Password** は､Blockchain Workbench をデプロイしたときに作成したパスワードです｡
    
   ![データベースにアクセスするための資格情報を指定する](media/blockchain-workbench-data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>データベースのテーブルとビューを表示する

データベースに接続すると､Excel Navigator ダイアログ ボックスが開きます｡ Navigator を使用して､データベースのテーブルやビューを表示することができます｡ ビューはレポート用であり､名前は **vw** で始まります｡

   ![Excel Navigator でのビューのプレビュー](media/blockchain-workbench-data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Excel のブックにビュー データを読み込む

以下は､ビューのデータを Excel のブックに読み込む例です｡

1. **Navigator** のスクロール バーで **vwContractAction** ビューを選択します｡ **vwContractAction** プレビューに､Blockchain Workbench のコントラクトに関係するすべてのアクションが表示されます｡ 
2. **Load** を選択してビュー内のすべてーのデータを読み出し､Excel のブックに読み込みます｡

   ![ビューから読み込まれたデータ](media/blockchain-workbench-data-excel/view-data.png)

データが読み込まれると､Excel の機能と Azure Blockchain Workbench データベースのメタデータとトランザクション データを使って独自のレポートを作成することができます｡

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のデータベース ビュー](blockchain-workbench-database-views.md)