---
title: 共有データベース
description: Azure Synapse Analytics は、サーバーレス Apache Spark プールでデータベースを作成することでサーバーレス SQL プール エンジンと SQL プール エンジンからアクセスできるようにする共有メタデータ モデルを提供します。
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 10/05/2021
author: ma77b
ms.author: maburd
ms.reviewer: wiassaf
ms.custom: devx-track-csharp
ms.openlocfilehash: 6144afdca350e8e7ff609eec273f84a39b84d2a3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228489"
---
# <a name="azure-synapse-analytics-shared-database"></a>Azure Synapse Analytics の共有データベース

Azure Synapse Analytics では、さまざまな計算ワークスペース エンジンがデータベースとテーブルを共有できます。 現時点では、Apache Spark プールに作成されたデータベースとテーブル (Parquet または CSV ベース) は、サーバーレス SQL プール エンジンと自動的に共有されます。

Spark ジョブを使用して作成されたデータベースは、その同じ名前で、サーバーレス SQL プール エンジンも含め、ワークスペース内の現在および将来のすべての Spark プールから参照できるようになります。 サーバーレス SQL プールを使用して、この同期されたデータベースにカスタム オブジェクト (外部テーブル、ビュー、プロシージャ) を直接追加することはできません。

Spark の既定のデータベース (`default` と呼ばれる) は、`default` と呼ばれるデータベースとしてサーバーレス SQL プール コンテキストにも表示されます。 Spark でデータベースを作成してから、サーバーレス SQL プールに同じ名前の別のデータベースを作成することはできません。

データベースはサーバーレス SQL プールに非同期的に同期されるため、表示されるまでに遅延が発生します。

## <a name="manage-a-spark-created-database"></a>Spark で作成されたデータベースを管理する

Spark で作成されたデータベースを管理するには、Apache Spark プールを使用する必要があります。 たとえば、Spark プール ジョブを使用して作成したり削除します。

同期されたデータベース内のオブジェクトは、サーバーレス SQL プールから変更できません。

>[!NOTE]
>異なるプールから同じ名前の複数のデータベースを作成することはできません。 サーバーレス SQL プール データベースを作成した場合、同じ名前の Spark データベースを作成することはできません。 それぞれ、データベースを Spark で作成した場合、同じ名前のサーバーレス SQL プール データベースを作成することはできません。

## <a name="security-model"></a>セキュリティ モデル

Spark データベースおよびテーブルは、SQL エンジン内のそれらの同期された表現とともに、基になるストレージ レベルで保護されます。

データベースを作成するセキュリティ プリンシパルは、そのデータベースの所有者と見なされ、データベースとそのオブジェクトに対するすべての権限を持ちます。 また、Synapse 管理者と Synapse SQL 管理者も、既定でサーバーレス SQL プール内の同期されたオブジェクトに対するすべてのアクセス許可を持ちます。 同期された SQL データベースにカスタム オブジェクト (ユーザーを含む) を作成することはできません。 

セキュリティ プリンシパル (ユーザー、Azure AD アプリまたはセキュリティ グループなど) に、外部テーブルに使用される基になるデータへのアクセス権を付与するには、ルートまでのすべての親フォルダーに対して、ファイル (テーブルの基になるデータファイルなど) への `read (R)` アクセス許可と、ファイルが格納されるフォルダーへの `execute (X)` アクセス許可を付与する必要があります。 これらのアクセス許可の詳細については、[アクセス制御リスト (ACL)](../../storage/blobs/data-lake-storage-access-control.md) に関するページを参照してください。 

たとえば、`https://<storage-name>.dfs.core.windows.net/<fs>/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/` では、セキュリティ プリンシパルは `<fs>` から `myparquettable` で始まるすべてのフォルダーに対する `X` アクセス許可と、`myparquettable` フォルダーとそのフォルダー内のファイルに対する `R` アクセス許可を持つ必要があります。これにより、(同期された、または元の) データベース テーブルを読み取ることができるようになります。

セキュリティ プリンシパルに、データベース内にオブジェクトを作成したり、オブジェクトを削除したりする権限が必要な場合は、`warehouse` フォルダー内のフォルダーとファイルに対する追加の `W` 権限が必要になります。 サーバーレス SQL プールからは、データベース内のオブジェクトを変更することはできません。Spark からのみ変更できます。

## <a name="examples"></a>例

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>サーバーレス SQL プールを使用して Spark データベースを作成して接続する

まず、ワークスペースに既に作成済みの Spark クラスターを使用して、`mytestdb` という名前の新しい Spark データベースを作成します。 たとえば、次の .NET for Spark ステートメントで Spark C# ノートブックを使用して、これを実現できます。

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

少し時間が経過すると、サーバーレス SQL プールからデータベースを確認できるようになります。 たとえば、サーバーレス SQL プールから次のステートメントを実行します。

```sql
SELECT * FROM sys.databases;
```

結果に `mytestdb` が含まれていることを確認します。

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics の共有メタデータについての詳細情報](overview.md)
- [Azure Synapse Analytics の共有メタデータ テーブルについての詳細情報](table.md)