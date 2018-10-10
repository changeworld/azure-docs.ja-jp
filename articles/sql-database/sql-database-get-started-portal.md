---
title: 'Azure Portal: SQL データベースの作成 | Microsoft Docs'
description: Azure Portal で SQL Database の論理サーバー、サーバーレベルのファイアウォール規則、およびデータベースを作成し、クエリを実行します。
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
ms.date: 09/07/2018
ms.openlocfilehash: 0e7ea33fa775bfba934d68d7cbcdd754880c3e55
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165009"
---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Azure Portal で Azure SQL データベースを作成する

このクイック スタートでは、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)を使用して、Azure で SQL データベースを作成する方法について説明します。 Azure SQL Database は、"サービスとしてのデータベース" を提供するサービスで、これを使用すると、クラウド内で可用性の高い SQL Server データベースの実行とスケーリングが可能になります。 このクイック スタートでは、Azure Portal を使用して SQL データベースを作成し、クエリを実行することによって作業を開始する方法を示します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

  >[!NOTE]
  >このチュートリアルでは、DTU ベースの購入モデルを使用しますが、[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)も利用できます。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-sql-database"></a>SQL Database の作成

Azure SQL データベースは、定義済みの一連の[コンピューティング リソースとストレージ リソース](sql-database-service-tiers-dtu.md)を使って作成されます。 データベースは、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)内と [Azure SQL Database 論理サーバー](sql-database-features.md)内に作成されます。

以下の手順に従い、Adventure Works LT サンプル データを含む SQL データベースを作成します。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。

2. **[新規]** ページから **[データベース]** を選択し、**[新規]** ページの **[SQL Database]** で **[作成]** を選択します。

   ![データベースの作成 -1](./media/sql-database-get-started-portal/create-database-1.png)

3. 前の画像で示されているように、[SQL Database] のフォームに次の情報を入力します。   

   | Setting       | 推奨値 | 説明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **[データベース名]** | mySampleDatabase | 有効なデータベース名については、「[Database Identifiers (データベース識別子)](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
   | **サブスクリプション** | 該当するサブスクリプション  | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   | **[リソース グループ]**  | myResourceGroup | 有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
   | **ソースの選択** | Sample (AdventureWorksLT) | AdventureWorksLT のスキーマとデータを新しいデータベースに読み込みます。 |

   > [!IMPORTANT]
   > このクイック スタートの残りの部分で使用するため、このフォームでサンプル データベースを選択する必要があります。
   >

4. 次の画像に示されているように、**[サーバー]** の **[Configure required settings]\(必要な設定の構成\)** をクリックし、SQL Server (論理サーバー) フォームに次の情報を入力します。   

   | Setting       | 推奨値 | 説明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **サーバー名** | グローバルに一意の名前 | 有効なサーバー名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
   | **サーバー管理者ログイン** | 有効な名前 | 有効なログイン名については、「[Database Identifiers (データベース識別子)](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
   | **パスワード** | 有効なパスワード | パスワードには 8 文字以上が使用され、大文字、小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。 |
   | **サブスクリプション** | 該当するサブスクリプション | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   | **[リソース グループ]** | myResourceGroup | 有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
   | **場所** | 有効な場所 | リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。 |

   > [!IMPORTANT]
   > ここで指定するサーバー管理者のログイン名とパスワードは、このクイックスタートの後半でサーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。
   >  

   ![データベース サーバーの作成](./media/sql-database-get-started-portal/create-database-server.png)

5. フォームの入力が完了したら、**[選択]** をクリックします。

6. **[価格レベル]** をクリックして、サービス レベル、DTU 数、ストレージの容量を指定します。 DTU の量とストレージの容量に関して、サービス レベルごとに利用できるオプションを調べます。

   > [!IMPORTANT]
   > 現在、英国北部、米国中西部、英国南部 2、中国東部、US DoD 中部、ドイツ中部、US DoD 東部、US Gov 南西部、US Gov 中南部、ドイツ北東部、中国北部、US Gov 東部を除くすべてのリージョンでは、Premium レベルで 1 TB を超えるストレージを使用できます。 それ以外のリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。 [P11 ～ P15 の現時点での制限]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)に関するページを参照してください。  

7. このクイック スタートでは、**Standard** サービス レベルを選択したうえで、スライダーを使用して **10 DTU (S0)** と **1** GB のストレージを選択します。

   ![データベースの作成 -s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. プレビューの使用条件に同意して、**[Add-on Storage]\(アドオン ストレージ\)** オプションを使用します。

   > [!IMPORTANT]
   > 現在、米国中西部、中国東部、US DoD 中部、USGov アイオワ、ドイツ中部、US DoD 東部、US Gov 南西部、ドイツ北東部、中国北部を除くすべてのリージョンでは、Premium レベルで 1 TB を超えるストレージを使用できます。 それ以外のリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。 [P11 ～ P15 の現時点での制限]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)に関するページを参照してください。  

9. サーバーのレベル、DTU 数、ストレージの容量を選択したら、**[適用]** をクリックします。  

10. これで SQL Database フォームの入力が完了したので、**[作成]** をクリックして、データベースをプロビジョニングします。 プロビジョニングには数分かかります。

11. ツール バーの **[通知]** をクリックして、デプロイ プロセスを監視します。

     ![通知](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>SQL データベースに対するクエリ

Azure でサンプル データベースを作成できたので、Azure Portal の組み込みのクエリ ツールを使用して、データベースへの接続とデータへのクエリを実行できることを確認してみましょう。

1. データベースの SQL Database ページで、左側のメニューにある **[クエリ エディター (プレビュー)]** をクリックし、**[ログイン]** をクリックします。

   ![login](./media/sql-database-get-started-portal/query-editor-login.png)

2. SQL Server 認証を選択し、必要なログイン情報を入力して、**[OK]** をクリックしてログインします。

3. **ServerAdmin** として認証されたら、[クエリ エディター] ウィンドウに次のクエリを入力します。

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. **[実行]** をクリックし、**[結果]** ウィンドウでクエリの結果を確認します。

   ![クエリ エディターの結果](./media/sql-database-get-started-portal/query-editor-results.png)

5. **[クエリ エディター]** ページを閉じ、**[OK]** をクリックして未保存の編集を破棄します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

データベースに接続してクエリを実行するためのさまざまな方法を紹介した「[次のステップ](#next-steps)」に進む場合は、これらのリソースを保存しておいてください。 一方、このクイック スタートで作成したリソースを削除する場合は、次の手順に従います。


1. Azure Portal の左側のメニューから、**[リソース グループ]**、**[myResourceGroup]** の順にクリックします。
2. リソース グループのページで **[削除]** をクリックし、テキスト ボックスに「**myResourceGroup**」と入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

- これでデータベースが用意できたので、オンプレミスのツールからそれに接続するためのサーバーレベルのファイアウォール規則を作成する必要があります。 「[サーバーレベルのファイアウォール規則を作成する](sql-database-get-started-portal-firewall.md)」を参照してください。
- サーバーレベルのファイアウォール規則を作成している場合は、次のような、お気に入りのツールまたは言語のいずれかを使用して[接続およびクエリ実行](sql-database-connect-query.md)できます。
  - [SQL Server Management Studio を使用して接続およびクエリを実行する](sql-database-connect-query-ssms.md)
  - [Azure Data Studio を使用して接続およびクエリを実行する](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI を使用してデータベースを作成するには、「[Azure CLI のサンプル](sql-database-cli-samples.md)」を参照してください。
- Azure PowerShell を使用してデータベースを作成するには、「[Azure PowerShell サンプル](sql-database-powershell-samples.md)」を参照してください。