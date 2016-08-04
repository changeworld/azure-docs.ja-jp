<properties
   pageTitle="Azure ポータルでの SQL Data Warehouse の作成 | Microsoft Azure"
   description="Azure ポータルで Azure SQL Data Warehouse を作成する方法を説明します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/23/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# Azure SQL Data Warehouse の作成

> [AZURE.SELECTOR]
- [Azure ポータル](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

このチュートリアルでは、Azure ポータルを使用して、AdventureWorksDW サンプル データベースを含む SQL Data Warehouse を作成します。


## 前提条件

開始するには、以下のものが必要です。

- **Azure アカウント**: アカウントを作成するには、[Azure 無料試用版][]に関するページまたは [MSDN Azure クレジット][]に関するページにアクセスしてください。
- **Azure SQL サーバー**: 詳細については、[Azure ポータルでの Azure SQL Database 論理サーバーの作成][]に関するセクションを参照してください。

> [AZURE.NOTE] 新しい SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。料金の詳細については、「[SQL Data Warehouse の価格][]」を参照してください。

## SQL Data Warehouse の作成

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. **[+ 新規]**、**[データ + ストレージ]**、**[SQL Data Warehouse]** の順にクリックします。

    ![作成](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. **[SQL Data Warehouse]** ブレードで必要な情報を入力し、[作成] を押して作成します。

    ![データベースを作成する](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **[サーバー]**: まず、サーバーを選択することをお勧めします。既存のサーバーを選択するか、[新しく作成](./sql-data-warehouse-get-started-new-server.md)することができます。

	- **[データベース名]**: SQL Data Warehouse の参照に使用される名前です。サーバーに対して一意にする必要があります。
	
    - **[パフォーマンス]**: 最初は 400 [DWU][DWU] にすることをお勧めします。スライダーを左または右に移動して、データ ウェアハウスのパフォーマンスを調整したり、作成後にスケールアップまたはスケールダウンしたりすることができます。DWU の詳細については、[スケーリング](./sql-data-warehouse-manage-compute-overview.md)に関するドキュメントまたは[価格のページ][SQL Data Warehouse pricing]を参照してください。

    - **[サブスクリプション]**: この SQL Data Warehouse の課金先の[サブスクリプション]を選択します。

    - **[リソース グループ]**: [リソース グループ][Resource group]は、Azure リソースのコレクション管理のサポートを目的としたコンテナーです。[リソース グループ](../resource-group-overview.md)に関する詳細情報を参照してください。

    - **[ソースの選択]**: **[ソースの選択]**、**[サンプル]** の順にクリックします。この時点で使用できるサンプル データベースは 1 つだけなので、[サンプル] を選択すると、**[サンプルの選択]** に AdventureWorksDW が自動的に設定されます。

4. **[作成]** をクリックして SQL Data Warehouse を作成します。

5. 数分すると SQL Data Warehouse の準備が整います。完了すると、[Azure ポータル](https://portal.azure.com)に戻ります。SQL Data Warehouse は、ダッシュボード上で、SQL データベースの一覧か、作成に使用したリソース グループ内に表示されます。

    ![ポータルの表示](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL DataBase create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## 次のステップ

SQL Data Warehouse の作成は以上で完了です。いつでも[接続](./sql-data-warehouse-connect-overview.md)して、クエリを実行することができます。

SQL Data Warehouse へのデータの読み込みについては、[読み込みの概要](./sql-data-warehouse-overview-load.md)に関するページを参照してください。

既存のデータベースを SQL Data Warehouse に移行する場合、[移行の概要](./sql-data-warehouse-overview-migrate.md)に関するページを参照するか、[移行ユーティリティ](./sql-data-warehouse-migrate-migration-utility.md)を使用してください。

TRANSACT-SQL を使用して、ファイアウォール規則を構成することもできます。詳細については、[sp\_set\_firewall\_rule][] と [sp\_set\_database\_firewall\_rule][] を確認してください。

[ベスト プラクティス][]も良い参考になります。

<!--Article references-->
[Azure ポータルでの Azure SQL Database 論理サーバーの作成]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[ベスト プラクティス]: ./sql-data-warehouse-best-practices.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[サブスクリプション]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group

<!--MSDN references-->
[sp\_set\_firewall\_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp\_set\_database\_firewall\_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[SQL Data Warehouse の価格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 無料試用版]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure クレジット]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0727_2016-->