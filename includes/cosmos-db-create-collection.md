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
ms.openlocfilehash: cf77eaa07d45222cecf0450fb33fe62e556bcd9e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38729005"
---
Azure Portal でデータ エクスプローラー ツールを使用してデータベースとコレクションを作成できるようになりました。 

1. **[データ エクスプローラー]** > **[新しいコレクション]** をクリックします。 
    
    **[コレクションの追加]** 領域が右端に表示されます。表示するには、右にスクロールする必要がある場合があります。

    ![Azure Portal の [データ エクスプローラー] の [コレクションの追加] ブレード](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. **[コレクションの追加]** ページで、新しいコレクションの設定を入力します。

    Setting|推奨値|説明
    ---|---|---
    データベース ID|タスク|新しいデータベースの名前として*タスク*を入力します。 データベース名は 1 文字以上 255 文字以内にする必要があります。/、\\、#、? は使えず、末尾にスペースを入れることもできません。
    コレクション ID|項目|新しいコレクションの名前として*項目*を入力します。 コレクション ID には、データベース名と同じ文字要件があります。
    ストレージの容量| 固定 (10 GB)|既定値の **[固定 (10 GB)]** を使用します。 この値は、データベースの記憶域容量です。
    Throughput|400 RU|スループットを 400 要求ユニット (RU/秒) に変更します。 スループットを 400 RU/秒に設定するには、記憶域容量を**固定 (10 GB)** に設定する必要があります。 待ち時間を短縮する場合、後でスループットをスケールアップできます。 
    
    上記の設定に加え、必要に応じて、このコレクション用に**一意なキー**を追加できます。 この例では、このフィールドを空のままにしましょう。 一意なキーを使用すると、開発者はデータベースにデータ整合性のレイヤーを追加できます。 コレクションの作成中に一意キー ポリシーを作成すると、パーティション キーごとに 1 つ以上の値の一意性が保証されます。 詳細については、記事「[Azure Cosmos DB における一意なキー](../articles/cosmos-db/unique-keys.md)」を参照してください。
    
    Click **OK**.

    新しいデータベースとコレクションがデータ エクスプローラーに表示されます。

    ![新しいデータベースとコレクションを示す Azure Portal のデータ エクスプローラー](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection.png)