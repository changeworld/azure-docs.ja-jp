---
title: 完全なテキスト一致リスト データを取得するための LUIS アプリを作成するチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、意図とリスト エンティティを使用して簡単な LUIS アプリを作成し、このクイック スタートのデータを抽出する方法を学習します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: afad3fe725fddd0748cc206517a7274815cf1653
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495266"
---
# <a name="tutorial-4-add-list-entity"></a>チュートリアル: 4. リスト エンティティを追加する
このチュートリアルでは、定義済みのリストに一致するデータを取得する方法を示すアプリを作成します。 

<!-- green checkmark -->
> [!div class="checklist"]
> * リスト エンティティを理解する 
> * MoveEmployee 意図を使用して人事 (HR) ドメイン用の新しい LUIS アプリを作成する
> * 発話から従業員項目を抽出するリスト エンティティを追加する
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始する前に
[RegEx エンティティ](luis-quickstart-intents-regex-entity.md) チュートリアルの人事アプリがない場合は、JSON を [LUIS](luis-reference-regions.md#luis-website) Web サイトの新しいアプリに[インポート](luis-how-to-start-new-app.md#import-new-app)します。 インポートするアプリは、[LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json) GitHub リポジトリにあります。

元の人事アプリを保持したい場合は、[[設定]](luis-how-to-manage-versions.md#clone-a-version) ページ上でバージョンを複製して、`list` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 

## <a name="purpose-of-the-list-entity"></a>リスト エンティティの目的
このアプリは、ある建物から別の建物への従業員の移動に関する発言を予測します。 このアプリはリスト エンティティを使用して、従業員を抽出します。 従業員の確認には、名前、電話番号、メール、または米国連邦政府の社会保障番号を使用します。 

リスト エンティティは多数の項目を保持でき、各項目にシノニムがあります。 小規模から中規模の企業については、従業員情報を抽出するときにリスト エンティティが使用されます。 

各項目の正規名は従業員番号です。 このドメインのシノニムの例を次に示します。 

|シノニムの目的|シノニムの値|
|--|--|
|Name|John W. Smith|
|電子メール アドレス|john.w.smith@mycompany.com|
|電話の内線番号|x12345|
|個人の携帯電話番号|425-555-1212|
|米国連邦政府の社会保障番号|123-45-6789|

リスト エンティティがこのデータの種類に適しているのは次の場合です。

* データ値が既知のセットである。
* セットがこのエンティティ型の最大 LUIS [境界](luis-boundaries.md)を超えていない。
* 発話内のテキストがシノニムと正確に一致している。 

LUIS は、従業員を移動するための標準的な指示をクライアント アプリケーションが作成できるような方法で、従業員を抽出します。
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>MoveEmployee 意図を追加する

1. 人事アプリは必ず、LUIS の**ビルド** セクションに配置してください。 右上のメニュー バーの **[Build]\(ビルド\)** を選択すると、このセクションに変更できます。 

2. **[Create new intent]\(意図の新規作成\)** を選択します。 

3. ポップアップ ダイアログ ボックスに「`MoveEmployee`」と入力して、**[完了]** を選択します。 

    ![新しい意図の作成ダイアログのスクリーンショット](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. 発話の例を意図に追加します。

    |発話の例|
    |--|
    |John W. Smith を B-1234 から H-4452 に移動する|
    |john.w.smith@mycompany.com をオフィス b-1234 からオフィス h-4452 に移動する|
    |明日 x12345 を h-1234 にシフトする|
    |425-555-1212 を HH-2345 に配置する|
    |123-45-6789 を A-4321 から J-23456 に移動する|
    |Jill Jones を D-2345 から J-23456 に移動する|
    |jill-jones@mycompany.com を M-12345 にシフトする|
    |x23456 を M-12345 に|
    |425-555-0000 を h-4452 に|
    |234-56-7891 を hh-2345 に|

    [ ![新しい発話が強調表示されている [Intents]\(意図\) ページのスクリーンショット](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

## <a name="create-an-employee-list-entity"></a>従業員のリスト エンティティを作成する
**MoveEmployee** 意図に発話が追加されました。次は、LUIS に従業員を認識させる必要があります。 

1. 左のパネルで **[エンティティ]** を選びます。

2. **[Create new entity]\(新しいエンティティの作成\)** を選択します。

3. エンティティ ポップアップ ダイアログで、エンティティ名として「`Employee`」を入力し、エンティティ型として **[リスト]** を指定します。 **[完了]** を選択します。  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "新しいエンティティの作成ポップアップ ダイアログのスクリーンショット")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. 従業員エンティティ ページで、新しい値として「`Employee-24612`」と入力します。

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "値の入力のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. シノニムについては、次の値を追加します。

    |シノニムの目的|シノニムの値|
    |--|--|
    |Name|John W. Smith|
    |電子メール アドレス|john.w.smith@mycompany.com|
    |電話の内線番号|x12345|
    |個人の携帯電話番号|425-555-1212|
    |米国連邦政府の社会保障番号|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "シノニムの入力のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. 新しいシノニムとして「`Employee-45612`」と入力します。

7. シノニムについては、次の値を追加します。

    |シノニムの目的|シノニムの値|
    |--|--|
    |Name|Jill Jones|
    |電子メール アドレス|jill-jones@mycompany.com|
    |電話の内線番号|x23456|
    |個人の携帯電話番号|425-555-0000|
    |米国連邦政府の社会保障番号|234-56-7891|

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>異なる発話でエンドポイントにクエリを実行する

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. アドレスの URL の末尾に移動し、「`shift 123-45-6789 from Z-1242 to T-54672`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 ラベル付けされた発話に、この発話と一致するものはありません。したがって、テストは適切で、`MoveEmployee` 意図が返され `Employee` が抽出されるはずです。

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  従業員が見つかり、その従業員は `Employee` 型として返され、値 `Employee-24612` に解決されました。

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>リスト エンティティでの自然言語処理の場所 
リスト エンティティは完全テキスト一致なので、自然言語処理 (または機械学習) には依存しません。 LUIS は、自然言語処理 (または機械学習) を使って、正しいトップ スコア意図を選択します。 また、発話には、複数のエンティティや、さらには複数の種類のエンティティが含まれていてもかまいません。 各発話が、自然言語処理 (または機械学習) エンティティを含め、アプリ内のすべてのエンティティについて処理されます。

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
リスト エンティティを含むこのアプリにより、適切な従業員が抽出されました。 

お使いのチャットボットには、現在、基本アクション `MoveEmployee` および移動する従業員を判断するための十分な情報があります。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し元アプリケーションは、エンティティから topScoringIntent の結果とデータを取得して、次のステップに進むことができます。 LUIS は、ボットや呼び出し元アプリケーションのためにこのようなプログラムによる処理を実行するわけではありません。 LUIS はユーザーの意図を判断するだけです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [階層エンティティをアプリに追加する](luis-quickstart-intent-and-hier-entity.md)

