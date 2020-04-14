---
title: Azure Cosmos DB によってトリガーされる関数を作成する
description: Azure Functions を使用して、データが Azure Cosmos DB のデータベースに追加されるときに呼び出されるサーバーレスの関数を作成します。
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 10/02/2018
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 6045c61dc9837667bfaf01c685f687fcf5816e4c
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754198"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Azure Cosmos DB によってトリガーされる関数を作成する

Azure Cosmos DB にデータが追加される、または変更されるときにトリガーされる関数の作成方法を説明します。 Azure Cosmos DB の詳細については、「[Azure Cosmos DB: Azure Functions を使用したサーバーなしのデータベースのコンピューティング](../cosmos-db/serverless-computing-database.md)」をご覧ください。

![ログ内のメッセージを表示します。](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

+ Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

トリガーを作成する前に、SQL API を使用する Azure Cosmos DB アカウントが必要です。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

次に、新しい Function App で関数を作成します。

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Azure Cosmos DB のトリガーの作成

1. Function App を展開し、 **[関数]+ の横にある** [ **]** ボタンをクリックします。 これが関数アプリの初めての関数の場合は、 **[ポータル内]** 、 **[続行]** の順に選択します。 それ以外の場合は、手順 3 に進みます。

   ![Azure Portal での関数のクイック スタート ページ](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. **[その他のテンプレート]** 、 **[Finish and view templates]\(終了してテンプレートを表示\)** の順に選択します。

    ![Functions のクイック スタート: [その他のテンプレート] を選択する](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. 検索フィールドに「`cosmos`」と入力し、**Azure Cosmos DB トリガー** テンプレートを選択します。

1. プロンプトが表示されたら、 **[インストール]** を選択して Azure Cosmos DB 拡張機能を関数アプリにインストールします。 インストールが正常に完了したら、 **[続行]** を選択します。

    ![バインディング拡張機能をインストールする](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. 新しいトリガーは、次の画像の下の表に示したように設定します。

    ![Azure Cosmos DB でトリガーされる関数の作成](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | 設定      | 推奨値  | 説明                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **名前** | Default | テンプレートに示されている既定の関数名を使用します。|
    | **Azure Cosmos DB アカウント接続** | 新しい設定 | **[新規]** を選択し、使用する**サブスクリプション**、前の手順で作成した**データベース アカウント**、 **[選択]** の順に選択します。 これにより、アカウント接続のアプリケーション設定が作成されます。 この設定は、データベースへの接続へのバインディングによって使用されます。 |
    | **コンテナー名** | アイテム | 監視するコンテナーの名前。 |
    | **Create lease container if it doesn't exist (リース コンテナーが存在しない場合は作成する)** | オン | コンテナーはまだ存在していないため、作成します。 |
    | **データベース名** | 処理手順 | 監視するコンテナーのデータベース名。 |

1. **[作成]** をクリックして、Azure Cosmos DB でトリガーされる関数を作成します。 関数が作成されると、テンプレート ベースの関数コードが表示されます。  

    ![Cosmos DB の関数テンプレート (C#)](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    この関数テンプレートは、ドキュメント数と最初のドキュメント ID をログに書き込みます。

次に、Azure Cosmos DB アカウントに接続し、`Items` データベースに `Tasks` コンテナーを作成します。

## <a name="create-the-items-container"></a>Items コンテナーの作成

1. ブラウザーの新しいタブで、[Azure Portal](https://portal.azure.com) の 2 つ目のインスタンスを開きます。

1. ポータルの左側のアイコン バーを展開し、検索フィールドに「`cosmos`」と入力して、 **[Azure Cosmos DB]** を選択します。

    ![Azure Cosmos DB サービスの検索](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Azure Cosmos DB アカウントを選択してから、 **[データ エクスプローラー]** を選択します。 

1. **[SQL API]** で **Tasks** データベースを選択し、 **[新しいコンテナー]** を選択します。

    ![コンテナーを作成する](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. **[コンテナーの追加]** で、次の画像の下の表に示したように設定します。 

    ![Tasks コンテナーを定義する](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | 設定|推奨値|説明 |
    | ---|---|--- |
    | **[データベース ID]** | 処理手順 |新しいデータベースの名前。 これは、関数バインドで定義された名前と一致する必要があります。 |
    | **コンテナー ID** | アイテム | 新しいコンテナーの名前。 これは、関数バインドで定義された名前と一致する必要があります。  |
    | **[[パーティション キー]](../cosmos-db/partition-data.md)** | /category|各パーティションに均等にデータを分散するパーティション キー。 効率の良いコンテナーを作成するためには、正しいパーティション キーを選択することが大切です。 | 
    | **スループット** |400 RU| 既定値を使用します。 待ち時間を短縮する場合、後でスループットをスケールアップできます。 |    

1. **[OK]** をクリックして、Items コンテナーを作成します。 コンテナーが作成されるまで多少時間がかかる場合があります。

関数バインドで指定されたコンテナーが作成されたら、この新しいコンテナーに項目を追加することで関数をテストできます。

## <a name="test-the-function"></a>関数をテストする

1. データ エクスプローラーで新しい **Items** コンテナーを展開し、 **[Items]\(項目\)** を選択して、 **[New Item]\(新しい項目\)** を選択します。

    ![Items コンテナーへの項目の作成](./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png)

1. 新しい項目の内容を次の内容に置き換えて、 **[保存]** を選択します。

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. ポータルでご利用の関数が表示されている、ブラウザーの 1 つ目のタブに切り替えます。 関数ログを展開し、新しいドキュメントがその関数をトリガーしたことを確認します。 `task1` のドキュメント ID の値がログに書き込まれていることを確認してください。 

    ![ログ内のメッセージを表示します。](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (省略可能) ドキュメントに戻り、変更を加え、 **[更新]** をクリックします。 次に、関数ログに戻り、この更新によっても関数がトリガーされたことを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でドキュメントが追加または変更されたときに実行される関数を作成しました。 Azure Cosmos DBのトリガーの詳細については、「[Azure Functions の Azure Cosmos DB バインド](functions-bindings-cosmosdb.md)」をご覧ください。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
