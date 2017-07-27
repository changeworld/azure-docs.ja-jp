---
title: "最初の Azure SQL Database の設計 - C# | Microsoft Docs"
description: "ADO.NET を利用し、C# プログラムで最初の Azure SQL データベースを設計し、それに接続する方法について説明します。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg-msft
editor: CarlRabeler
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/20/2017
ms.author: genemi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 069656b495929d66f6a47b38141f56b6bf0ac92f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017

---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>C&#x23; と ADO.NET で Azure SQL データベースを設計し、接続する

Azure SQL Database は、Microsoft Cloud ("Azure") のサービスとしてのリレーショナル データベース (DBaaS) です。 このチュートリアルでは、Azure Portal と ADO.NET を使用して次のことを行う方法を説明します。 

> [!div class="checklist"]
> * Azure Portal でデータベースを作成する
> * Azure Portal でサーバーレベルのファイアウォール規則を設定する
> * ADO.NET を使用してデータベースに接続する
> * ADO.NET を使用してテーブルを作成する
> * ADO.NET を使用してデータを挿入する 
> * ADO.NET を使用してデータのクエリを実行する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>次のステップ

次のステップを使用できます。

- [SQL クエリに関して C# プログラムの LINQ を使用する](https://msdn.microsoft.com/library/bb425822.aspx)
- [SQL Server データベースを Azure SQL Database に移行する](sql-database-migrate-your-sql-server-database.md)

