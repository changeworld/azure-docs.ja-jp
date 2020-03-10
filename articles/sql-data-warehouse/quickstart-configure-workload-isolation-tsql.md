---
title: クイック スタート:ワークロードの分離を構成する - T-SQL
description: T-SQL を使用して、ワークロードの分離を構成します。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: bd3ad98116b18a77a77e8f6f327689d0ebb7dd21
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200517"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>クイック スタート:T-SQL を使用してワークロードの分離を構成する

このクイックスタートでは、データの読み込み用にリソースを予約する場合にワークロード グループとワークロード分類子をすばやく作成します。 ワークロード グループによって、データの読み込みにシステム リソースの 20% が割り当てられます。  ワークロード分類子によって、データ読み込みワークロード グループに要求が割り当てられます。  データの読み込みに対して 20% が分離されることで、SLA を達成するためのリソースが保証されます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

> [!NOTE]
> Azure Synapse Analytics の SQL Analytics インスタンスを作成すると、新しい課金対象サービスを使用することになる場合があります。  詳細については、「[Azure Synapse Analytics の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」を参照してください。
>
>

## <a name="prerequisites"></a>前提条件
 
このクイックスタートは、Azure Synapse の SQL Analytics インスタンスが既に用意されていて、CONTROL データベース権限を持っていることを前提としています。 作成する必要がある場合は、[ポータルでの作成と接続](create-data-warehouse-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前のデータ ウェアハウスを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-login-for-dataloads"></a>DataLoads のログインを作成する

"ELTLogin" に対して [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) を使用して、`master` データベースで SQL Server 認証ログインを作成します。

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>ユーザーの作成

mySampleDataWarehouse に "ELTLogin" という[ユーザーを作成](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest)します

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>ワークロード グループを作成する
DataLoads の[ワークロード グループ](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)を 20% の分離で作成します。
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>ワークロード分類子を作成する

ELTLogin を DataLoads ワークロード グループにマップする[ワークロード分類子](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)を作成します。

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>既存のワークロード グループ、ワークロード分類子、およびランタイム値を表示する

```sql
--Workload groups
SELECT * FROM 
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM 
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM 
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

データ ウェアハウス ユニットと、データ ウェアハウスに格納されているデータに対して課金されます。 これらのコンピューティングとストレージのリソースは別々に請求されます。

- データをストレージに保持しておく場合は、SQL プールを使わない間、コンピューティング リソースを一時停止できます。 コンピューティングを一時停止すると、データ ストレージに対してのみ課金されます。 データを使用する準備ができたら、コンピューティングを再開します。
- それ以上課金されないようにする場合は、データ ウェアハウスを削除できます。

以下の手順に従ってリソースをクリーンアップします。

1. [Azure portal](https://portal.azure.com) にサインインし、データ ウェアハウスを選択します。

    ![リソースをクリーンアップする](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. コンピューティング リソースを一時停止するには、 **[一時停止]** ボタンを選択します。 データ ウェアハウスが一時停止すると、ボタンの表示が **[開始]** になります。  コンピューティング リソースを再開するには、 **[開始]** を選択します。

3. コンピューティング リソースやストレージに課金されないようにデータ ウェアハウスを削除するには、 **[削除]** を選択します。

4. 作成した SQL Server を削除するには、前の画像の **mynewserver-20180430.database.windows.net** を選択して、 **[削除]** を選択します。  サーバーを削除すると、サーバーに割り当てられているすべてのデータベースが削除されるので、削除には注意してください。

5. リソース グループを削除するには、**myResourceGroup** を選択して、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- これで、ワークロード グループが作成されました。 いくつかのクエリを ELTLogin として実行し、その動作を確認します。 クエリと、割り当てられたワークロード グループを確認するには、[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) を参照してください。
- SQL Analytics ワークロード管理の詳細については、[ワークロード管理](sql-data-warehouse-workload-management.md)と[ワークロードの分離](sql-data-warehouse-workload-isolation.md)に関する記事を参照してください。
