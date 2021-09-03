---
title: Storage Explorer でナレッジ ストアを表示する
titleSuffix: Azure Cognitive Search
description: Azure portal の Storage Explorer で Azure Cognitive Search のナレッジ ストアを表示して分析します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: ee6e17e6fe52125d75f2782b1fa77215045787a3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740212"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Storage Explorer でナレッジ ストアを表示する

[ナレッジ ストア](knowledge-store-concept-intro.md)は、スキルセットによって作成されて、Azure Storage に保存されます。 この記事では、Azure portal の Storage Explorer を使用してナレッジ ストアの内容を表示する方法を説明します。

## <a name="prerequisites"></a>前提条件

+ [Azure portal](knowledge-store-create-portal.md) または [Postman と REST API](knowledge-store-create-rest.md) でナレッジ ストアを作成します。

+ ナレッジ ストアがある Azure Storage アカウントの名前とそのアクセス キー (Azure portal から入手) も必要になります。

## <a name="start-storage-explorer"></a>Storage Explorer を開始します

1. Azure portal で、ナレッジ ストアの作成に使用した [Storage アカウントを開きます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. ストレージ アカウントの左側のナビゲーション ウィンドウで、 **[Storage Explorer]** をクリックします。

## <a name="view-edit-and-query-tables"></a>テーブルの表示、編集、クエリを行う

ポータルと REST のどちらのチュートリアルでも、Table Storage にナレッジ ストアが作成されます。

1. **[テーブル]** の一覧を展開し、ナレッジ ストアを作成したときに作成された Azure テーブル プロジェクションの一覧を表示します。 テーブルには、ホテルのレビューに関連するコンテンツが含まれているはずです。

1. いずれかのテーブルを選択すると、エンリッチされたデータ (キー フレーズ、センチメント スコアなど) が表示されます。

   ![Storage Explorer でテーブルを表示する](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Storage Explorer でテーブルを表示する")

1. テーブルの値のデータ型を変更したり、テーブル内の値を個別に変更したりするには、 **[Edit]\(編集\)** をクリックします。 1 つのテーブル行について列のデータ型を変更すると、すべての行に適用されます。

   ![Storage Explorer でテーブルを編集する](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Storage Explorer でテーブルを編集する")

1. クエリを実行するには、コマンド バーの **[Query]\(クエリ\)** をクリックして条件を入力します。  

   ![Storage Explorer のクエリ テーブル](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Storage Explorer のクエリ テーブル")

## <a name="next-steps"></a>次のステップ

このナレッジ ストアを Power BI に接続して詳細な分析を行うか、または REST API と Postman を使用してコーディングを進め、別のナレッジストアを作成します。

> [!div class="nextstepaction"]
> [Power BI を使用した接続](knowledge-store-connect-power-bi.md)
