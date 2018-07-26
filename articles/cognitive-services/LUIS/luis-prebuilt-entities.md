---
title: LUIS での作成済みエンティティ | Microsoft Docs
description: この記事では、Language Understanding Intelligent Services (LUIS) に含まれる作成済みエンティティの一覧を示します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: diberry
ms.openlocfilehash: 95ff173a843e7ad18ec396a1130b2e2b4035f656
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248239"
---
# <a name="prebuilt-entities"></a>作成済みエンティティ

LUIS には、日付、時刻、数字、測定値、通貨など、一般的な情報の種類を認識するための作成済みエンティティのセットが含まれています。 作成済みエンティティのサポートは、LUIS アプリのカルチャによって異なります。 カルチャによるサポートを含む、LUIS がサポートする作成済みエンティティの完全な一覧については、[作成済みエンティティのリファレンス](./luis-reference-prebuilt-entities.md)に関するページをご覧ください。

> [!NOTE]
> **builtin.datetime** は非推奨です。 [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md) によって置き換えられます。datetimeV2 は日付と時間の範囲を認識し、あいまいな日付と時刻の認識が強化されています。

## <a name="add-a-prebuilt-entity"></a>作成済みエンティティの追加

1. **[マイ アプリ]** ページで名前をクリックしてアプリを開き、左側で **[エンティティ]** をクリックします。 
2. **[エンティティ]** ページで **[Manage prebuilt entities]\(作成済みエンティティの管理\)** をクリックします。

    ![[エンティティ] ページ - [Manage prebuilt entities]\(作成済みエンティティの管理\)](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. **[Add prebuilt entities]\(作成済みエンティティの追加\)** ダイアログ ボックスで、追加する作成済みエンティティ (たとえば、"datetimeV2") をクリックします。 その後、 **[保存]** をクリックします。

    ![[Add prebuilt entities]\(作成済みエンティティの追加\) ダイアログ ボックス](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>作成済みの number エンティティを使用する
作成済みエンティティがアプリケーションに含まれる場合、エンティティの予測が発行されるアプリケーションに含まれます。 作成済みエンティティの動作は事前にトレーニングされており、変更することは**できません**。 作成済みエンティティの動作を確認するには、次の手順のようにします。

1. **number** エンティティをアプリに追加した後、アプリを[トレーニング](luis-interactive-test.md)して[発行](luis-how-to-publish-app.md)します。
2. **[アプリの発行]** ページでエンドポイント URL をクリックして、LUIS エンドポイントを Web ブラウザーで開きます。 
3. 数値表現が含まれている発話を URL に追加します。 たとえば、「`buy two plane ticktets`」などと入力します。LUIS が `two` を `builtin.number` エンティティとして識別し、その値を `2` と識別することを、`resolution` フィールドで確認します。 `resolution` フィールドは、数値と日付をクライアント アプリケーションが使いやすい標準形式に解決するのに役立ちます。 

    ![ブラウザーでの number エンティティを含む発話](./media/luis-use-prebuilt-entity/browser-query.png)

LUIS は、非標準形式ではない数字をインテリジェントに認識できます。 別の数値表現を発話で試し、LUIS が何を返すかを確認してしてください。

次の例では、"two dozen" (2 ダース) という発話に対する解決である 24 という値を含む LUIS からの JSON 応答を示します。

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## <a name="use-a-prebuilt-datetimev2-entity"></a>datetimeV2 作成済みエンティティを使用する
**datetimeV2** 作成済みエンティティは、日付、時刻、日付範囲、および期間を認識します。 `datetimeV2` 作成済みエンティティの動作を確認するには、次の手順のようにします。

1. **datetimeV2** エンティティをアプリに追加した後、アプリを[トレーニング](luis-interactive-test.md)して[発行](luis-how-to-publish-app.md)します。
2. **[アプリの発行]** ページでエンドポイント URL をクリックして、LUIS エンドポイントを Web ブラウザーで開きます。 
3. 日付範囲が含まれている発話を URL に追加します。 たとえば、「`book a flight tomorrow`」などと入力します。LUIS が `tomorrow` を `builtin.datetimeV2.date` エンティティとして識別し、その値を明日の日付と識別することを、`resolution` フィールドで確認します。 

次の例では、今日の日付が 2017 年 10 月 31 日である場合に、LUIS からの JSON 応答がどのようになるかを示します。

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [事前構築済みのエンティティのリファレンス](./luis-reference-prebuilt-entities.md)
