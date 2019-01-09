---
title: ポータルを使用した Azure SQL データベースの作成 | Microsoft Docs
description: Azure portal 上で Azure SQL Database の論理サーバーとデータベースを作成し、クエリを実行します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 12/21/2018
ms.openlocfilehash: b8ff482f2aec406ef4c1c545db7844a861317518
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994421"
---
# <a name="quickstart-create-an-azure-sql-database-in-the-azure-portal"></a>クイック スタート:Azure Portal で Azure SQL データベースを作成する

Azure SQL Database は、クラウド内で可用性の高い SQL Server データベースの実行とスケーリングを可能にする "*サービスとしてのデータベース*" です。 このクイック スタートでは、Azure portal を使用して Azure SQL データベースを作成し、クエリを実行することによって作業を開始する方法を示します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

このクイック スタートのすべての手順では、[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-sql-database"></a>SQL Database の作成

Azure SQL データベースには、定義済みの一連の[コンピューティング リソースとストレージ リソース](sql-database-service-tiers-dtu.md)があります。 データベースは、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)内の [Azure SQL Database 論理サーバー](sql-database-features.md)に作成します。

Adventure Works LT サンプル データを含む SQL データベースを作成するには:

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。
   
1. **[データベース]** を選択してから、**[SQL Database]** を選択します。
   
1. **[SQL Database]** フォームで、次の値を入力または選択します。 
   
   - **データベース名**:「*mySampleDatabase*」と入力します。
   - **サブスクリプション**:表示されていない場合は、ドロップ ダウンして適切なサブスクリプションを選択します。  
   - **[リソース グループ]**: **[新規作成]** を選択し、「*myResourceGroup*」と入力して、**[OK]** を選択します。 
   - **ソースの選択**:ドロップ ダウンして **[Sample (AdventureWorksLT) (サンプル (AdventureWorksLT))]** を選択します。 
   
   >[!IMPORTANT]
   >必ず **[Sample (AdventureWorksLT) (サンプル (AdventureWorksLT))]** を選択して、このクイック スタートとこのデータを使用する他の Azure SQL Database クイック スタートに従えるようにします。 
   
   ![Azure SQL データベースの作成](./media/sql-database-get-started-portal/create-database-1.png)
   
1. **[サーバー]** を選択してから、**[新しいサーバーの作成]** を選択します。 
   
1. **[新しいサーバー]** フォームで、次の値を入力または選択します。 
   
   - **サーバー名**: 「*mySQLServer*」と入力します。
   - **サーバー管理者ログイン**:「*azureuser*」と入力します。 
   - **[パスワード]**: 「*Azure1234567*」と入力します。 
   - **パスワードの確認**:パスワードを再入力します。
   - **場所**: ドロップ ダウンして任意の有効な場所を選択します。  
   
   >[!IMPORTANT]
   >サーバー管理者のログインとパスワードを記憶または記録して、このクイック スタートと他のクイック スタートのためにサーバーとデータベースにログインできるようにします。 ログインまたはパスワードを忘れた場合は、**[SQL サーバー]** ページでログイン名を取得するかパスワードをリセットします。 **[SQL サーバー]** ページを開くには、データベースの作成後にデータベースの **[概要]** ページでサーバー名を選択します。
   
1. **[選択]** を選択します。
   
   ![Create server](./media/sql-database-get-started-portal/create-database-server.png)
   
1. **[SQL Database]** フォームで、**[価格レベル]** を選択します。 サービス レベルごとに利用できる DTU の量とストレージの容量を調べます。
   
   >[!NOTE]
   >このクイック スタートでは、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)を使用しますが、[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)も利用できます。
   
   >[!NOTE]
   >現在、1 TB を超える Premium レベルのストレージは、英国北部、米国中西部、英国南部 2、中国東部、US DoD 中部、ドイツ中部、US DoD 東部、US Gov 南西部、US Gov 中南部、ドイツ北東部、中国北部、US Gov 東部を除くすべてのリージョンで使用できます。 これらのリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。 詳しくは、[P11-P15 の現在の制限事項](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)に関するページをご覧ください。  
   
1. このクイック スタートでは、**Standard** サービス レベルを選択したうえで、スライダーを使用して **10 DTU (S0)** と **1** GB のストレージを選択します。
   
1. **[適用]** を選択します。  
   
   ![価格の選択](./media/sql-database-get-started-portal/create-database-s1.png)
   
1. **[SQL Database]** フォームで **[作成]** を選択して、リソース グループ、サーバー、データベースをデプロイし、プロビジョニングします。 
   
   デプロイには、数分かかります。 ツール バーの **[通知]** を選択して、デプロイの進行状況を監視できます。

   ![通知](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>SQL データベースに対するクエリ

Azure SQL データベースを作成したので、Azure portal に組み込まれているクエリ ツールを使用して、データベースへの接続とデータへのクエリを実行します。

1. データベースの **[SQL Database]** ページで、左側のメニューの **[クエリ エディター (プレビュー)]** を選択します。 
   
   ![クエリ エディターへのログイン](./media/sql-database-get-started-portal/query-editor-login.png)
   
1. ログイン情報を入力し、**[OK]** を選択します。
   
1. **[クエリ エディター]** ウィンドウに次のクエリを入力します。
   
   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```
   
1. **[実行]** を選択し、**[結果]** ウィンドウでクエリの結果を確認します。

   ![クエリ エディターの結果](./media/sql-database-get-started-portal/query-editor-results.png)
   
1. **[クエリ エディター]** ページを閉じ、未保存の編集を破棄するかどうかを確認するプロンプトが表示されたら **[OK]** をクリックします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

データベースに接続してクエリを実行するためのさまざまな方法を紹介した「[次のステップ](#next-steps)」に進む場合は、このリソース グループ、SQL サーバー、SQL データベースを保存しておきます。 

これらのリソースの使用が終了したら、次のように削除できます。

1. Azure portal の左側のメニューで、**[リソース グループ]**、**[myResourceGroup]** の順に選択します。
1. リソース グループ ページで **[リソース グループの削除]** を選択します。 
1. フィールドに「*myResourceGroup*」と入力してから、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

- オンプレミスまたはリモート ツールから Azure SQL データベースに接続するにはサーバーレベルのファイアウォール規則を作成する必要があります。 詳細については、「[サーバーレベルのファイアウォール規則を作成する](sql-database-get-started-portal-firewall.md)」を参照してください。
- サーバーレベルのファイアウォール規則を作成した後に、いくつかの異なるツールと言語を使用して、データベースに[接続し、クエリを実行](sql-database-connect-query.md)できます。 
  - [SQL Server Management Studio を使用して接続およびクエリを実行する](sql-database-connect-query-ssms.md)
  - [Azure Data Studio を使用して接続およびクエリを実行する](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI を使用して Azure SQL データベースを作成するには、「[Azure CLI のサンプル](sql-database-cli-samples.md)」をご覧ください。
- Azure PowerShell を使用して Azure SQL データベースを作成するには、「[Azure PowerShell サンプル](sql-database-powershell-samples.md)」をご覧ください。
