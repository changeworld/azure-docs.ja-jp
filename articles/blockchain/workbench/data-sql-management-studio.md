---
title: SQL Server Management Studio を使用して Azure Blockchain Workbench データのクエリを実行する
description: SQL Server Management Studio 内から Azure Blockchain ワークベンチの SQL データベースに接続する方法を説明します。
ms.date: 11/20/2019
ms.topic: how-to
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: d34affa93af65acf5fd7d6558e2d8077a2efc360
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625162"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>SQL Server Management Studio で Azure Blockchain Workbench データを使用します

Microsoft SQL Server Management Studio では、Azure Blockchain ワークベンチの SQL データベースに対してクエリを短時間で作成およびテストする機能が提供されます。 このセクションでは、SQL Server Management Studio 内から Azure Blockchain Workbench の SQL データベースに接続する方法を、段階ごとに説明します。

## <a name="prerequisites"></a>前提条件

* [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)をダウンロードします。

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>SQL Server Management Studio を Azure Blockchain Workbench データに接続します

1. SQL Server Management Studio を開き、**接続** を選択します。
2. **データベース エンジン** を選択します。

    ![データベース エンジン](./media/data-sql-management-studio/database-engine.png)

3. **[サーバーに接続]** ダイアログで、サーバー名とお使いのデータベース資格情報を入力します。

    Azure Blockchain Workbench のデプロイプロセスによって作成された資格情報を使用している場合、ユーザー名は **dbadmin** となり、パスワードはデプロイ時に指定したパスワードです。

    ![SQL 資格情報を入力します](./media/data-sql-management-studio/sql-creds.png)

   1. SQL Server Management Studio では、Azure Blockchain Workbench データベース内のデータベース、データベース ビュー、およびストアド プロシージャの一覧が表示されます。

      ![データベースの一覧](./media/data-sql-management-studio/db-list.png)

5. データベース ビューのいずれかに関連付けられているデータを表示するには、以下の手順を使用して、自動的に選択ステートメントを生成できます。
6. オブジェクト エクスプローラーのデータベースビューのいずれかを右クリックします。
7. **[スクリプトビュー]** を選択します。
8. **[SELECT]** を選択します。
9. **新しいクエリ エディター ウィンドウ** を選択します。
10. **新しいクエリ** を選択して、新しいクエリを作成することができますです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のデータベース ビュー](database-views.md)
