---
author: stevestein
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 08/09/2019
ms.author: sstein
ms.openlocfilehash: f7b385f359311217595352be31ba0687ca87bf40
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873695"
---
## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure portal を使用した Azure SQL リソースの作成および管理

Azure portal には、SQL 仮想マシンを含む[すべての Azure SQL リソース](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql)を管理できる単一ページが用意されています。

**[Azure SQL resources]\(Azure SQL リソース\)** ページにアクセスするには、Azure portal の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択してから、検索ボックスに「*Azure SQL*」と入力します。

> [!NOTE]
> **Azure SQL** では、すべての SQL データベース、エラスティック プール、データベース サーバー、SQL マネージド インスタンス、および SQL 仮想マシンにすばやく簡単にアクセスできます。 Azure SQL はサービスでもリソースでもありません。 

既存のリソースを管理するには、一覧から目的の項目を選択します。 新しいAzure SQL リソースを作成するには、 **[+ 追加]** を選択します。 

![ポータルの Azure SQL のページ](../media/sql-database-technical-overview/azure-sql.png)

**[+ 追加]** を選択した後、任意のタイルで **[詳細の表示]** を選択して、さまざまなオプションに関する追加情報を表示します。

![データベース タイルの詳細](../media/sql-database-technical-overview/create-single-database.png)

詳細については、次のリンクを参照してください。

- [単一データベースを作成する](../sql-database-single-database-get-started.md)
- [エラスティック プールの作成](../sql-database-elastic-pool.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [マネージド インスタンスを作成する](../sql-database-managed-instance-get-started.md)
- [SQL 仮想マシンを作成する](../../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)