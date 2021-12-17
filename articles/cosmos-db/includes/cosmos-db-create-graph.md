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
ms.openlocfilehash: 4f11826c5f4a39a6df8c1b9823c54620cb66f3ee
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "113223503"
---
Azure Portal でデータ エクスプローラー ツールを使用してグラフ データベースを作成できるようになりました。 

1. **[データ エクスプローラー]** 、 **[New Graph]\(新しいグラフ\)** の順に選択します。

    **[グラフの追加]** 領域が右端に表示されます。表示するには、右にスクロールする必要がある場合があります。

    ![Azure Portal データ エクスプローラーの [グラフの追加] ページ](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. **[グラフの追加]** ページで、新しいグラフの設定を入力します。

    設定|推奨値|説明
    ---|---|---
    データベース ID|sample-database|新しいデータベースの名前として「*sample-database*」と入力します。 データベース名は、1 - 255 文字である必要があります。また、`/ \ # ?` は使えず、末尾にスペースを入れることもできません。
    スループット|400 RU|スループットを 400 要求ユニット (RU/秒) に変更します。 待ち時間を短縮する場合、後でスループットをスケールアップできます。
    グラフ ID|sample-graph|新しいコレクションの名前として「*sample-graph*」と入力します。 グラフ名の文字要件はデータベース ID と同じです。
    Partition Key| /pk |すべての Cosmos DB アカウントには、水平方向にスケーリングするために、パーティション キーが必要です。 適切なパーティション キーを選択する方法については、[グラフ データのパーティション分割に関する記事](../graph-partitioning.md)を確認してください。

3. フォームに入力したら、**[OK]** を選択します。