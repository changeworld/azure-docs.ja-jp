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
ms.openlocfilehash: b656001c8a7d1bed21c208bc643018c5f751e09c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38733833"
---
Azure Portal でデータ エクスプローラー ツールを使用してグラフ データベースを作成できるようになりました。 

1. **[データ エクスプローラー]** > **[New Graph]\(新しいグラフ\)** をクリックします。

    **[グラフの追加]** 領域が右端に表示されます。表示するには、右にスクロールする必要がある場合があります。

    ![Azure Portal データ エクスプローラーの [グラフの追加] ページ](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. **[グラフの追加]** ページで、新しいグラフの設定を入力します。

    Setting|推奨値|説明
    ---|---|---
    データベース ID|sample-database|新しいデータベースの名前として「*sample-database*」と入力します。 データベース名は、1 - 255 文字である必要があります。また、`/ \ # ?` は使えず、末尾にスペースを入れることもできません。
    グラフ ID|sample-graph|新しいコレクションの名前として「*sample-graph*」と入力します。 グラフ名の文字要件はデータベース ID と同じです。
    ストレージの容量|固定 (10 GB)|既定値の**固定 (10 GB)** のままにします。 この値は、データベースの記憶域容量です。
    Throughput|400 RU|スループットを 400 要求ユニット (RU/秒) に変更します。 待ち時間を短縮する場合、後でスループットをスケールアップできます。

3. フォームに入力したら、**[OK]** をクリックします。
