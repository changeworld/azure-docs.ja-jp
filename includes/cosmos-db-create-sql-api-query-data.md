---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: e6e70da1f939547cdb589ae7bcb6ed1f6148f22e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38733771"
---
これで、データ エクスプローラーでクエリを使用して、データを取得しフィルター処理できるようになりました。

1. 既定では、クエリは `SELECT * FROM c` と設定されていることを確認してください。 この既定のクエリでは、コレクション内のすべてのドキュメントを取得し、表示します。 

    ![データ エクスプローラーの既定のクエリは `SELECT * FROM c`](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)

2. 引き続き **[ドキュメント]** タブで **[フィルターの編集]** ボタンをクリックし、クエリの述語のボックスに `ORDER BY c._ts DESC` を追加してから、**[フィルターの適用]** をクリックすることでクエリを変更します。

    ![ORDER BY c._ts DESC を追加し [フィルタの適用] をクリックすることで既定のクエリを変更](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

この変更したクエリでは、ドキュメントがそのタイムスタンプの降順に一覧表示されるので、ここでは 2 番目のドキュメントが最初に表示されます。 SQL 構文に慣れている場合は、サポートされている任意の [SQL クエリ](../articles/cosmos-db/sql-api-sql-query.md)をこのボックスに入力できます。 

以上でデータ エクスプローラーでの作業は完了です。 コードにとりかかる前に、データ エクスプローラーを使用すると、ストアド プロシージャ、UDF、トリガーを作成し、サーバー側ビジネス ロジックを実行できるほか、スループットのスケールもできることに注目してください。 データ エクスプローラーでは、API で使用可能な、組み込みのプログラムによるデータ アクセスがすべて公開されていますが、Azure Portal でデータに簡単にアクセスできます。