---
title: 最初の Azure SQL Database の設計 - C# | Microsoft Docs
description: ADO.NET を利用し、C# プログラムで最初の Azure SQL データベースを設計し、それに接続する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 12/10/2018
ms.openlocfilehash: cf180f6e2970ac4435602f1cceeb98a4dd9e8724
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727167"
---
# <a name="tutorial-design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>チュートリアル:C&#x23; と ADO.NET で Azure SQL データベースを設計し、接続する

Azure SQL Database は、Microsoft Cloud (Azure) のリレーショナルなサービスとしてのデータベース (DBaaS) です。 このチュートリアルでは、Azure Portal、ADO.NET、および Visual Studio を使用して以下の手順を実行する方法を説明します。

> [!div class="checklist"]
> * Azure Portal でデータベースを作成する
> * Azure Portal でサーバーレベルのファイアウォール規則を設定する
> * ADO.NET と Visual Studio を使用してデータベースに接続する
> * ADO.NET を使用してテーブルを作成する
> * ADO.NET を使用してデータを挿入、更新、削除する
> * ADO.NET を使用してデータのクエリを実行する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

[Visual Studio 2017](https://www.visualstudio.com/downloads/) のインストール

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]

<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、データベースとテーブルの作成、データベースへの接続、データの読み込み、クエリの実行など、基本的なデータベース タスクについて学習しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * データベースを作成する
> * ファイアウォール規則の設定
> * [Visual Studio と C#](sql-database-connect-query-dotnet-visual-studio.md) を使用して SQL Database に接続する
> * テーブルの作成
> * データの挿入、更新、削除、照会

次のチュートリアルに進み、データ移行について確認してください。

> [!div class="nextstepaction"]
> [DMS を使用して SQL Server を Azure SQL Database にオフラインで移行する](../dms/tutorial-sql-server-to-azure-sql.md)
