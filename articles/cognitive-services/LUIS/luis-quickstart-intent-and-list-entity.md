---
title: 完全なテキスト一致
titleSuffix: Azure Cognitive Services
description: 事前に定義された項目の一覧に一致するデータを取得します。 一覧の各項目には、同様に完全に一致するシノニムを含めることができます。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 5706e0b124bb9ceaf1abf7228faf088dc4e510ce
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53096691"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>チュートリアル 4:完全なテキスト一致を抽出する
このチュートリアルでは、事前に定義された項目の一覧に一致するデータを取得する方法について説明します。 一覧の各項目には、シノニムの一覧を含めることができます。 人事アプリの場合、従業員は、名前、電子メール、電話番号、米国連邦税 ID などのいくつかの重要な情報で識別できます。 

人事アプリは、あるビルから別のビルにどの従業員が移動するかを特定する必要があります。 従業員の移動に関する発話の場合、LUIS はその意図を特定し、従業員を移動するための標準的な指示をクライアント アプリケーションが作成できるように従業員を抽出します。

このアプリは、リスト エンティティを使用して従業員を抽出します。 従業員は、名前、会社の内線番号、携帯電話番号、電子メール、または米国連邦政府の社会保障番号を使用して参照できます。 

リスト エンティティがこのデータの種類に適しているのは次の場合です。

* データ値が既知のセットである。
* セットがこのエンティティ型の最大 LUIS [境界](luis-boundaries.md)を超えていない。
* 発話内のテキストがシノニムまたは正規名に完全に一致している。 

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * 既存のチュートリアル アプリを使用する
> * MoveEmployee 意図を追加する
> * リスト エンティティを追加する 
> * トレーニング 
> * [発行]
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>既存のアプリを使用する
最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

以前のチュートリアルの HumanResources アプリがない場合は、次の手順を使用します。

1.  [アプリの JSON ファイル](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `list` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。 


## <a name="moveemployee-intent"></a>MoveEmployee 意図

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

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

    番号と datetimeV2 が前のチュートリアルで追加され、いずれかの発話の例に見つかると自動的にラベルが付けられることに注意してください。

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>従業員のリスト エンティティ
これで **MoveEmployee** 意図に発話の例が含まれたので、LUIS は従業員が何かを理解する必要があります。 

各項目のプライマリ (_正規_) 名は従業員番号です。 このドメインの場合、各正規名のシノニムの例は次のとおりです。 

|シノニムの目的|シノニムの値|
|--|--|
|Name|John W. Smith|
|電子メール アドレス|john.w.smith@mycompany.com|
|電話の内線番号|x12345|
|個人の携帯電話番号|425-555-1212|
|米国連邦政府の社会保障番号|123-45-6789|


1. 左のパネルで **[エンティティ]** を選びます。

2. **[Create new entity]\(新しいエンティティの作成\)** を選択します。

3. エンティティ ポップアップ ダイアログで、エンティティ名として「`Employee`」を入力し、エンティティ型として **[リスト]** を指定します。 **[完了]** を選択します。  

    [![新しいエンティティ作成のポップアップ ダイアログのスクリーンショット](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "新しいエンティティ作成のポップアップ ダイアログのスクリーンショット")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. 従業員エンティティ ページで、新しい値として「`Employee-24612`」と入力します。

    [![値入力のスクリーンショット](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "値入力のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. シノニムについては、次の値を追加します。

    |シノニムの目的|シノニムの値|
    |--|--|
    |Name|John W. Smith|
    |電子メール アドレス|john.w.smith@mycompany.com|
    |電話の内線番号|x12345|
    |個人の携帯電話番号|425-555-1212|
    |米国連邦政府の社会保障番号|123-45-6789|

    [![シノニム入力のスクリーンショット](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "シノニム入力のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. 新しいシノニムとして「`Employee-45612`」と入力します。

7. シノニムについては、次の値を追加します。

    |シノニムの目的|シノニムの値|
    |--|--|
    |Name|Jill Jones|
    |電子メール アドレス|jill-jones@mycompany.com|
    |電話の内線番号|x23456|
    |個人の携帯電話番号|425-555-0000|
    |米国連邦政府の社会保障番号|234-56-7891|

## <a name="train"></a>トレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>[発行]

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. アドレスの URL の末尾に移動し、「`shift 123-45-6789 from Z-1242 to T-54672`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 ラベル付けされた発話に、この発話と一致するものはありません。したがって、テストは適切で、`MoveEmployee` 意図が返され `Employee` が抽出されるはずです。

  ```json
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

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順
このチュートリアルでは、新しい意図を作成し、発話の例を追加した後、発話から完全なテキスト一致を抽出するためのリスト エンティティを作成しました。 トレーニングおよびアプリの発行後、エンドポイントのクエリによって意図を識別し、抽出されたデータを取得しました。

> [!div class="nextstepaction"]
> [階層エンティティをアプリに追加する](luis-quickstart-intent-and-hier-entity.md)

