---
title: Azure Blockchain Workbench データベースに関する詳細の取得
description: Azure Blockchain Workbench データベースおよびデータベース サーバーに関する情報の取得方法を説明しています。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 63b718bcb8722c5fd501891d162eadfae9fb8ec2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Azure Blockchain Workbench データベースに関する情報の取得

Azure Blockchain Workbench データベースに関する情報の取得方法を説明しています。

## <a name="overview"></a>概要

アプリケーション､ワークフロー、およびスマート コントラクトの実行に関する情報は、Blockchain Workbench SQL DB のデータベース ビューを使って提供されます。 開発者は、Microsoft Excel や Power BI、Visual Studio､ SQL Server Management Studio などのツールの利用でこうした情報を利用できます。

データベースに接続するには､以下を行う必要があります｡

* データベースのファイアウォールで外部クライアントからのアクセスを許可する｡ データベースのファイアウォールの設定に関するこの記事では､アクセスを許可する方法を説明しています｡
* データベース サーバー名とデータベース名

## <a name="connect-to-the-blockchain-workbench-database"></a>Blockchain Workbench データベースに接続する

データベースに接続する手順は以下の通りです｡

1. Azure Blockchain Workbench リソースに対する **Owner** アクセス許可を受けているアカウントを使って Azure ポータルにサインインします｡
2. 左側のナビゲーション ペインで、**[リソース グループ]** を選択します。
3. Blockchain Workbench のデプロイにリソース グループの名前を選択します。
4. **Type** を選択してリソース一覧を並べ替え､**SQL server** を選択します｡ 次図は並べ替えられた一覧の画面キャプチャーで､｢master｣と､ **Resource プレフィックス** が｢lhgn｣の 2 つの SQL データベースがあります｡

   ![Sorted Blockchain Workbench のリソース一覧](media/blockchain-workbench-getdb-details/sorted-workbench-resource-list.png)

5. Blockchain Workbench データベースに関する詳細情報を見るには､Blockchain Workbench のデプロイで指定した **Resource プレフィックス** を持つデータベースのリンクを選択します｡

   ![データベースの詳細](media/blockchain-workbench-getdb-details/workbench-db-details.png)

開発ツールやレポート ツールでは､データベース サーバー名とデータベース名を使って Blockchain Workbench データベースに接続することができます｡

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のデータベース ビュー](blockchain-workbench-database-views.md)