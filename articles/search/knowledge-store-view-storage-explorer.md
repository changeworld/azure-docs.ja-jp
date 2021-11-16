---
title: ナレッジ ストアを表示する
titleSuffix: Azure Cognitive Search
description: Azure portal のストレージ ブラウザーを使用して、ナレッジ ストアを表示します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/03/2021
ms.openlocfilehash: d3a66bacfe746b73d269cd8a36fedf0c6b821cdd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563665"
---
# <a name="view-a-knowledge-store-with-storage-browser"></a>ストレージ ブラウザーでナレッジ ストアを表示する

[ナレッジ ストア](knowledge-store-concept-intro.md)は、Azure Cognitive Search スキルセットによって作成され、Azure Storage に保存されるコンテンツです。 この記事では、Azure portal のストレージ ブラウザーを使用してナレッジ ストアの内容を表示する方法を説明します。

[Azure portal](knowledge-store-create-portal.md) 内または [REST API](knowledge-store-create-rest.md) を使用して作成された既存のナレッジ ストアから始めます。 ポータルと REST のどちらのチュートリアルでも、Azure Table Storage にナレッジ ストアが作成されます。

## <a name="start-storage-browser"></a>ストレージ ブラウザーを起動する

1. Azure portal で、ナレッジ ストアの作成に使用した [Storage アカウントを開きます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. ストレージ アカウントの左側のナビゲーション ウィンドウで、 **[ストレージ ブラウザー]** を選択します。

## <a name="view-and-edit-tables"></a>テーブルを表示および編集する

1. **テーブル** を展開して、ナレッジ ストアのテーブル プロジェクションを見つけます。 クイックスタートまたは REST の記事を使用してナレッジ ストアを作成した場合、テーブルにはヨーロッパのホテルの顧客レビューに関連するコンテンツが含まれます。

   :::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="ストレージ ブラウザーのスクリーンショット" border="true":::

1. 一覧からテーブルを選択すると、その内容が表示されます。

1. 列の順序を変更したり、列を削除したりするには、ページ上部の **[列の編集]** を選択します。

ストレージ ブラウザーでは、[サポートされているクエリ構文](/rest/api/storageservices/Querying-Tables-and-Entities)を使用して、一度に 1 つのテーブルのクエリのみを実行できます。 テーブル全体でクエリを実行するには、代わりに Power BI の使用を検討してください。

## <a name="next-steps"></a>次のステップ

このナレッジ ストアを Power BI に接続して、複数のテーブルを含む視覚化を作成します。

> [!div class="nextstepaction"]
> [Power BI を使用した接続](knowledge-store-connect-power-bi.md)
