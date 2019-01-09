---
title: SQL Server Management Studio で Azure Blockchain Workbench データを使用します
description: SQL Server Management Studio 内から Azure Blockchain ワークベンチの SQL データベースに接続する方法を説明します。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 3a0b4db77e6dfb8be3a2f943052925001e5f7715
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259785"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>SQL Server Management Studio で Azure Blockchain Workbench データを使用します

Microsoft SQL Server Management Studio では、Azure Blockchain ワークベンチの SQL データベースに対してクエリを短時間で作成およびテストする機能が提供されます。 このセクションでは、SQL Server Management Studio 内から Azure Blockchain ワークベンチの SQL データベースに接続する方法を、段階ごとに説明します。

## <a name="prerequisites"></a>前提条件

* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)をダウンロードします。

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>SQL Server Management Studio を Azure Blockchain Workbench データに接続します

1. SQL Server Management Studio を開き、**接続**を選択します。
2. **データベース エンジン**を選択します。

    ![データベース エンジン](./media/data-sql-management-studio/database-engine.png)

3. **[サーバーに接続]** ダイアログで、サーバー名とお使いのデータベース資格情報を入力します。

    Azure Blockchain Workbench の展開プロセスによって作成された資格情報を使用している場合、ユーザー名は **dbadmin** となり、パスワードは展開時に指定したパスワードです。

    ![SQL 資格情報を入力します](./media/data-sql-management-studio/sql-creds.png)

 4. SQL Server Management Studio では、Azure Blockchain Workbench データベース内のデータベース、データベース ビュー、およびストアド プロシージャの一覧が表示されます。

    ![データベースの一覧](./media/data-sql-management-studio/db-list.png)

5. データベース ビューのいずれかに関連付けられているデータを表示するには、以下の手順を使用して、自動的に選択ステートメントを生成できます。
6. オブジェクト エクスプローラーのデータベースビューのいずれかを右クリック。
7. **[スクリプトビュー]** を選択します。
8. **[SELECT]** を選択します。
9. **新しいクエリ エディター ウィンドウ**を選択します。
10. **新しいクエリ**を選択して、新しいクエリを作成することができますです。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のデータベース ビュー](database-views.md)
