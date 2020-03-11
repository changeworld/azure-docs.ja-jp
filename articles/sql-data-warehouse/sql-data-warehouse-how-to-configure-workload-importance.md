---
title: ワークロードの重要度の構成
description: Azure Synapse Analytics で要求レベルの重要度を設定する方法について説明します。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8b2a4333717938edf9f3039e29e8df88cece7cc1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196799"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Azure Synapse Analytics でのワークロードの重要度の構成

Azure Synapse の SQL Analytics で重要度を設定すると、クエリのスケジュール設定に影響を与えることができます。 重要度の高いクエリが、重要度の低いクエリよりも先に実行されるようスケジュールされます。 重要度をクエリに割り当てるには、ワークロード分類子を作成する必要があります。

## <a name="create-a-workload-classifier-with-importance"></a>重要度を使用したワークロード分類子を作成する

データ ウェアハウスのシナリオでは多くの場合、ユーザーはクエリをすばやく実行することを必要とします。  このユーザーは、レポートを実行する必要がある会社の経営陣や、アドホック クエリを実行するアナリストである場合があります。 重要度をクエリに割り当てるには、ワークロード分類子を作成します。  次の例では新しい [create workload classifier](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) 構文を使用して、2 つの分類子を作成しています。  Membername には単一のユーザーまたはグループを指定できます。 個々のユーザー分類はロール分類よりも優先されます。 既存のデータ ウェアハウス ユーザーを検索するには、次のコマンドを実行します。

```sql
Select name from sys.sysusers
```

重要度の高いユーザー向けのワークロード分類子を作成するには、次のコマンドを実行します。

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

重要度の低いアドホック クエリを実行するユーザー向けのワークロード分類子を作成するには、次のコマンドを実行します。  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>次の手順
- ワークロード管理の詳細については、[ワークロード分類](sql-data-warehouse-workload-classification.md)に関するページを参照してください
- 重要度の詳細については、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)に関するページを参照してください

> [!div class="nextstepaction"]
> [ワークロードの重要度の管理と監視に移動](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
