---
title: "Azure Functions と Cosmos DB を使用して非構造化データを格納する"
description: "Azure Functions と Cosmos DB を使用して非構造化データを格納する"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, Cosmos DB, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: ms-hero
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/08/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 46022530c69b292878a4500c0e325bda878e6188
ms.contentlocale: ja-jp
ms.lasthandoff: 05/12/2017


---
# <a name="store-unstructured-data-using-azure-functions-and-cosmos-db"></a>Azure Functions と Cosmos DB を使用して非構造化データを格納する

Azure Cosmos DB は、非構造化 JSON データを格納するうえで最適な手段です。 Cosmos DB は、Azure Functions と連携させると、リレーショナル データベースにデータを格納する場合よりもはるかに少ないコードですばやく簡単にデータを格納することができます。

このチュートリアルでは、Cosmos DB ドキュメントに非構造化データを格納する Azure 関数を Azure Portal を使用して作成する方法について説明しています。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-a-function"></a>関数を作成する

`MyTaskList` という名前の新しい C# ジェネリック webhook を作成します。

1. 既存の関数一覧を展開し、+ 記号をクリックして新しい関数を作成します。
1. [GenericWebHook-CSharp] を選択し、`MyTaskList` という名前を付けます。

![新しい C# ジェネリック webhook Function App の追加](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-new-functionapp.png)

## <a name="add-an-output-binding"></a>出力バインディングを追加する

Azure 関数には、1 つのトリガーと任意の数の入力バインディングまたは出力バインディングを割り当てることができます。 この例では、HTTP 要求トリガーと Cosmos DB ドキュメントを出力バインディングとして使用します。

1. 関数のトリガーとバインディングを表示または編集するには、関数の *[統合]* タブをクリックします。
1. ページの右上にある *[新規出力]* リンクを選択します。

注: HTTP 要求トリガーは既に構成されていますが、Cosmos DB ドキュメント バインディングは自分で追加する必要があります。

![新しい Cosmos DB 出力バインディングの追加](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

1. バインディングを作成するために必要な情報を入力します。 下の表を参考にして値を決めてください。

![Cosmos DB の出力バインディングの構成](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

|  フィールド | 値  |
|---|---|
| ドキュメント パラメーター名 | コード内で Cosmos DB オブジェクトを指す名前 |
| データベース名 | ドキュメントを保存するデータベースの名前 |
| コレクション名 | Cosmos DB データベースのグループの名前 |
| Would you like the Cosmos DB and collection created for you (Cosmos DB とコレクションが作成されるようにしますか?) | はい/いいえ |
| Cosmos DB account connection (Cosmos DB アカウント接続) | Cosmos DB データベースを指す接続文字列 |

さらに、Cosmos DB データベースへの接続を構成する必要もあります。

1. [Cosmos DB document connection]\(Cosmos DB ドキュメント接続\) ラベルの横の [新規] リンクをクリックします。
1. 各フィールドに必要事項を入力し、Cosmos DB ドキュメントを作成するために必要な適切なオプションを選択します。

![Cosmos DB 接続の構成](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

|  フィールド | 値  |
|---|---|
| ID | Cosmos DB データベースの一意の ID  |
| NoSQL API | Cosmos DB または MongoDB  |
| [サブスクリプション] | MSDN サブスクリプション  |
| リソース グループ  | 新しいグループを作成するか、または既存のグループを選択します。  |
| 場所  | 西ヨーロッパ  |

1. *[OK]* ボタンをクリックします。 Azure によってリソースが作成されるまでに数分かかる場合があります。
1. *[保存]* ボタンをクリックします。

## <a name="update-the-function-code"></a>関数コードを更新する

関数のテンプレート コードを以下の内容に置き換えます。

このサンプルのコードは C# のみです。

```csharp
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, out object taskDocument, TraceWriter log)
{
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    string task = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "task", true) == 0)
        .Value;

    string duedate = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "duedate", true) == 0)
        .Value;

    taskDocument = new {
        name = name,
        duedate = duedate.ToString(),
        task = task
    };

    if (name != "" && task != "") {
        return req.CreateResponse(HttpStatusCode.OK);
    }
    else {
        return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}

```

このサンプル コードは、HTTP 要求のクエリ文字列を読み取り、それらを `taskDocument` オブジェクトのメンバーとして代入します。 `taskDocument` オブジェクトは、そのデータを自動的に Cosmos DB データベースに保存し、さらに初回使用時にはデータベースを作成します。

## <a name="test-the-function-and-database"></a>関数とデータベースをテストする

1. [関数] タブで、ポータルの右側にある *[テスト]* リンクをクリックし、次の HTTP クエリ文字列を入力します。

| クエリ文字列 | 値 |
|---|---|
| name | Chris P. Bacon |
| タスク | Make a BLT sandwich |
| duedate | 05/12/2017 |

1. *[実行]* リンクをクリックします。
1. 関数から *HTTP 200 OK* という応答コードが返されたことを確認します。

![Cosmos DB の出力バインディングの構成](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

Cosmos DB データベースにエントリが作成されていることを確認します。

1. Azure Portal で目的のデータベースを探して選択します。
1. *[データ エクスプローラー]* オプションを選択します。
1. ドキュメントのエントリに到達するまでノードを展開します。
1. データベース エントリを確認します。 データベースには、先ほど入力したデータの他にメタデータが存在します。

![Cosmos DB エントリの確認](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

データがドキュメントに存在すれば、非構造化データを Cosmos DB データベースに保存する Azure 関数は正しく作成されています。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

Azure Functions の詳細については、次のトピックを参照してください。

[!INCLUDE [Getting help note](../../includes/functions-get-help.md)]

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]
