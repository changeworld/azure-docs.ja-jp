---
title: Azure Storage インベントリを使用して BLOB の数とサイズを計算する
description: コンテナーごとの BLOB の数と合計サイズを計算する方法について説明します。
services: storage
author: normesta
ms.author: normesta
ms.date: 08/16/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: subject-rbac-steps
ms.openlocfilehash: f4a03a73a85fa265517b421c2179a077d73a75be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600344"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Azure Storage インベントリを使用してコンテナーあたりの BLOB の数と合計サイズを計算する

この記事では、Azure Blob Storage インベントリ機能と Azure Synapse を使用して、コンテナーあたりの BLOB の数と合計サイズを計算します。 これらの値は、コンテナーごとに BLOB の使用を最適化するときに役立ちます。

この方法には、BLOB メタデータは含まれていません。 Azure Blob Storage インベントリ機能では、[List Blobs](/rest/api/storageservices/list-blobs) REST API と既定のパラメーターが使用されます。 そのため、この例はスナップショットや '$' コンテナーなどに対応していません。

## <a name="enable-inventory-reports"></a>インベントリ レポートを有効にする

この方法の最初の手順は、ストレージ アカウントで[インベントリ レポートを有効にする](blob-inventory.md#enabling-inventory-reports)ことです。 インベントリ レポートを有効にしてから、最初のレポートを生成するには、最大で 24 時間待機することが必要な場合があります。

分析するインベントリ レポートがあるときは、自分に **ストレージ BLOB データ閲覧者** ロールを付与してレポート CSV ファイルが置かれているコンテナーに対する読み取りアクセス権を自分自身に付与します。 必ずレポートを実行するために使用しているアカウントの電子メール アドレスを使用してください。 Azure ロールベースのアクセス制御 (Azure RBAC) を持つユーザーに Azure ロールを割り当てる方法については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」の手順に従ってください。

## <a name="create-an-azure-synapse-workspace"></a>Azure Synapse ワークスペースを作成する

次に SQL クエリを実行してインベントリ結果を報告するための [Azure Synapse ワークスペースを作成](../../synapse-analytics/get-started-create-workspace.md)します。

## <a name="create-the-sql-query"></a>SQL クエリを作成する

Azure Synapse ワークスペースを作成したら、次の手順を行います。

1. [https://web.azuresynapse.net](https://web.azuresynapse.net) に移動します。
1. 左端にある **[開発]** タブを選択します。
1. 大きなプラス記号 (+) を選択して項目を追加します。
1. **SQL スクリプト** をインポートします。

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="SQL スクリプトを選択して新しいクエリを作成する ":::

## <a name="run-the-sql-query"></a>SQL クエリを実行する

1. 次の SQL クエリを Azure Synapse ワークスペースに追加して[インベントリ CSV ファイルを読み取ります](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file)。

    `bulk` パラメーターには、分析するインベントリ レポート CSV ファイルの URL を使用します。

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container,
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. 右側の [プロパティ] ウィンドウで、SQL クエリに名前を付けます。

1. Ctrl キーを押しながら S キーを押すか、 **[すべて発行]** ボタンを選択して、SQL クエリを発行します。

1. **[実行]** ボタンを選択して SQL クエリを実行します。 コンテナーごとの BLOB の数と合計サイズが **[結果]** ウィンドウに報告されます。

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="BLOB の数と合計サイズを計算するスクリプトを実行して得られた出力。":::

## <a name="next-steps"></a>次のステップ

- [Azure Storage BLOB インベントリを使用して BLOB データを管理する](blob-inventory.md)
- [Blob コンテナーの合計課金サイズを計算する](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)
