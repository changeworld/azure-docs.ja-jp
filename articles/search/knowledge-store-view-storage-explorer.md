---
title: Storage Explorer でナレッジ ストアを表示する
titleSuffix: Azure Cognitive Search
description: Azure portal の Storage Explorer で Azure Cognitive Search のナレッジ ストアを表示して分析します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: d87ec3fa05e1740b5cba7b4230894f1df822ac1f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784641"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Storage Explorer でナレッジ ストアを表示する

[ナレッジ ストア](knowledge-store-concept-intro.md)は、Azure Cognitive Search スキルセットによって作成され、Azure Storage に保存されるコンテンツです。 この記事では、Azure portal の Storage Explorer を使用してナレッジ ストアの内容を表示する方法を説明します。

## <a name="prerequisites"></a>前提条件

[Azure portal](knowledge-store-create-portal.md) 内または [REST API](knowledge-store-create-rest.md) を使用して作成された既存のナレッジ ストアから始めます。 ポータルと REST のどちらのチュートリアルでも、Azure Table Storage にナレッジ ストアが作成されます。

## <a name="start-storage-explorer"></a>Storage Explorer を開始します

1. Azure portal で、ナレッジ ストアの作成に使用した [Storage アカウントを開きます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. ストレージ アカウントの左側のナビゲーション ウィンドウで、 **[Storage Explorer]** を選択します。

## <a name="edit-and-query-tables"></a>テーブルを編集してクエリを実行する

1. **[テーブル]** の一覧を展開し、ナレッジ ストアを作成したときに作成された Azure テーブル プロジェクションの一覧を表示します。 クイックスタートまたは REST の記事を使用してナレッジ ストアを作成した場合、テーブルにはヨーロッパのホテルの顧客レビューに関連するコンテンツが含まれます。

1. 一覧からテーブルを選択します。

   ![Storage Explorer でテーブルを表示する](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Storage Explorer でテーブルを表示する")

1. テーブル内のデータ型、プロパティ名、または個々のデータ値を変更するには、 **[編集]** をクリックします。

   ![Storage Explorer でテーブルを編集する](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Storage Explorer でテーブルを編集する")

1. クエリを実行するには、コマンド バーの **[クエリ]** を選択して条件を入力します。

   ![Storage Explorer のクエリ テーブル](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Storage Explorer のクエリ テーブル")

Storage Explorer では、[サポートされているクエリ構文](/rest/api/storageservices/Querying-Tables-and-Entities)を使用して、一度に 1 つのテーブルのクエリのみを実行できます。 テーブル全体でクエリを実行するには、代わりに Power BI の使用を検討してください。

## <a name="next-steps"></a>次のステップ

このナレッジ ストアを Power BI に接続して、複数のテーブルを含む視覚化を作成します。

> [!div class="nextstepaction"]
> [Power BI を使用した接続](knowledge-store-connect-power-bi.md)
