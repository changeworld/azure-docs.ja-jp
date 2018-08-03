---
title: LUIS アプリのバッチ テスト - Azure | Microsoft Docs
description: バッチ テストを使用して、アプリケーションの改善とその言語解釈の向上に継続的に取り組みます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/06/2018
ms.author: diberry
ms.openlocfilehash: bba3f2ff942fbe5dffc9b694990964e4e3078dbe
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222655"
---
# <a name="batch-testing-in-luis"></a>LUIS でのバッチ テスト

バッチ テストによって、[アクティブ](luis-concept-version.md#active-version)なトレーニング済みモデルを検証し、その予測精度を測定します。 バッチ テストは、現在のトレーニング済みモデルに含まれるそれぞれの意図とエンティティの精度をグラフで表示する助けになります。 バッチ テストの結果を確認して、精度を向上させるために適切なアクションを実行します。アプリが頻繁に失敗する場合は意図に発話の例をより多く追加し、正しい意図を明らかにする、といったアクションです。

## <a name="group-data-for-batch-test"></a>バッチ テスト用のグループ データ
バッチ テストに使用される発話が、LUIS にとって初めてであることが重要です。 発話のデータセットがある場合は、発話を 3 つのセットに分割します。意図に追加される発話、公開されているエンドポイントから受信した発話、およびトレーニング後に LUIS をバッチ テストするために使用される発話です。 

## <a name="a-dataset-of-utterances"></a>発話のデータセット
バッチ テストのために、*データセット*と呼ばれる発話のバッチ ファイルを送信します。 データセットは JSON 形式のファイルで、ラベル付きの**重複していない**発話が最大 1,000 件含まれます。 アプリでは、最大 10 個のデータセットをテストできます。 さらにテストする必要がある場合は、データセットを削除し、新しいものを追加します。

|**規則**|
|--|
|*重複する発話がない|
|階層構造エンティティの子がない|
|発話が 1000 以下|

*重複は、最初にトークン化される一致項目ではなく、正確な文字列の一致だと見なされています。 

## <a name="entities-allowed-in-batch-tests"></a>バッチ テストで許可されるエンティティ
エンティティには、シンプル、階層構造の親、および複合が含まれます。 バッチ ファイル内に対応するエンティティが存在しない場合でも、これらの型のすべてのエンティティがバッチ テストのエンティティのフィルターに表示されます。


<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>バッチ ファイルの形式
バッチ ファイルは、発話で構成されます。 各発話には、検出されると予期されている[機械学習によるエンティティ](luis-concept-entity-types.md#types-of-entities)すべてと共に、予期される意図の予測が必要です。 

正しい構文を持つバッチ ファイルの例を次に示します。

```JSON
[
  {
    "text": "Are there any janitorial jobs currently open?",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 14,
            "endPos": 23
        }
    ]
  },
  {
    "text": "I would like a fullstack typescript programming with azure job",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 15,
            "endPos": 46
        }
    ]
  },
  {
    "text": "Is there a database position open in Los Colinas?",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 11,
            "endPos": 18
        }
    ]
  },
  {
    "text": "Please find database jobs open today in Seattle",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 12,
            "endPos": 19
        }
    ]
  }
]
```

## <a name="batch-syntax-template"></a>バッチ構文のテンプレート

次のテンプレートを使用してバッチ ファイルを開始します。

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

バッチ ファイルは、**startPos** および **endPos** プロパティを使用して、エンティティの開始と終了をメモします。 これらの値は 0 から始まり、スペースで開始または終了してはいけません。 

これは、startIndex および endIndex プロパティを使用するクエリ ログとは異なります。 


## <a name="common-errors-importing-a-batch"></a>バッチ インポートでの一般的なエラー
一般的なエラーの理由は、次のとおりです。 

> * 発話数が 1,000 を超えている
> * エンティティ プロパティがない発話 JSON オブジェクト
> * 複数のエンティティにラベルが付いている単語

## <a name="batch-test-state"></a>バッチ テストの状態
LUIS は、各データセットの最後のテストの状態を追跡します。 これには、サイズ (バッチ内の発話の数)、最終実行日、最後の結果 (正常に予測された発話の数) が含まれます。

<a name="sections-of-the-results-chart"></a>
## <a name="batch-test-results"></a>バッチ テストの結果
バッチ テストの結果は、エラー マトリックスと呼ばれる散布図です。 このグラフは、ファイル内の発話と、現在のモデルの予測された意図およびエンティティを 4 つの方法で比較したものです。 

**False Positive** セクション上と **False Negative** セクション上のデータ ポイントは、調査が必要なエラーを示しています。 すべてのデータ ポイントが **True Positive** セクション上と **True Negative** セクション上にある場合、アプリの精度はこのデータセットに関しては理想的です。

![グラフの 4 つのセクション](./media/luis-concept-batch-test/chart-sections.png)

このグラフは、現在のトレーニングに基づいて LUIS が誤った予測をしている発話を見つけるのに役立ちます。 結果はグラフの領域ごとに表示されます。 発話情報を確認したり、そのリージョンでの発話の結果を確認したりするには、グラフ上の個々のポイントを選択します。

![バッチ テスト](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>結果内のエラー
バッチ テストのエラーは、バッチ ファイルに記載されているようには予測されていない意図を示しています。 エラーは、グラフの 2 つの赤い色のセクションに示されます。 

False Positive セクションは、そうであってはいけないときに、発話が意図またはエンティティと一致したことを示しています。 False Negative セクションは、そうであるべきときに、発話が意図またはエンティティと一致しなかったことを示しています。 

## <a name="fixing-batch-errors"></a>バッチのエラーの修正
バッチ テストでエラーがある場合は、より多くの発話を意図に追加したり、LUIS が意図を区別する助けとなるエンティティを使用して、より多くの発話にラベル付けしたりできます。 発話を追加し、それらにラベル付けしてもバッチ テストで予測エラーが発生する場合は、LUIS がより迅速に学習する助けになるように、ドメイン固有のボキャブラリを含む[語句一覧](luis-concept-feature.md)機能を追加することを検討してください。 

## <a name="next-steps"></a>次の手順

* [バッチをテストする](luis-how-to-batch-test.md)方法を学習します。
