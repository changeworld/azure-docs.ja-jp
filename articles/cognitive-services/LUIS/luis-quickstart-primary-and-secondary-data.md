---
title: データを抽出するための LUIS アプリ作成のチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、意図とシンプルなエンティティを使用して単純な LUIS アプリを作成し、機械学習されたデータを抽出する方法を学習します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 87d97b078927800e4e90c39a70e2acc7163a4c84
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493049"
---
# <a name="tutorial-7-add-simple-entity-and-phrase-list"></a>チュートリアル: 7.  シンプルなエンティティとフレーズ リストを追加する
このチュートリアルでは、**Simple** エンティティを使用して発話から機械学習されたデータを抽出する方法を示すアプリを作成します。

<!-- green checkmark -->
> [!div class="checklist"]
> * シンプルなエンティティとは 
> * 人事 (HR) ドメイン用の新しい LUIS アプリを作成する 
> * シンプル エンティティを追加してアプリからジョブを抽出する
> * アプリをトレーニングして公開する
> * アプリのエンドポイントをクエリして LUIS JSON の応答を表示する
> * フレーズ リストを追加してジョブの単語のシグナルを強化する
> * トレーニング、アプリの発行、エンドポイントの再実行を行う

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始する前に
[複合エンティティ](luis-tutorial-composite-entity.md) チュートリアルからの人事アプリを保持していない場合は、JSON を [LUIS](luis-reference-regions.md#luis-website) Web サイトの新しいアプリに[インポート](luis-how-to-start-new-app.md#import-new-app)します。 インポートするアプリは、[LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-composite-HumanResources.json) GitHub リポジトリにあります。

元の人事アプリを保持したい場合は、[[設定]](luis-how-to-manage-versions.md#clone-a-version) ページ上でバージョンを複製して、`simple` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。  

## <a name="purpose-of-the-app"></a>アプリの目的
このアプリでは、発話からデータを取得する方法を示します。 チャットボットからの次の発話について考えます。

|発話|抽出できるジョブの名前|
|:--|:--|
|新しい経理の仕事への応募を希望しています。|経理|
|エンジニアリングの職に私の履歴書を提出してください。|エンジニアリング|
|ジョブ 123456 の申込書に記入してください|123456|

このチュートリアルでは、ジョブ名を抽出する新しいエンティティを追加します。 

## <a name="purpose-of-the-simple-entity"></a>シンプル エンティティの目的
この LUIS アプリのシンプル エンティティの目的は、ジョブ名がどのようなもので、発話のどこで見つけられるかを、LUIS に教えることです。 発話のジョブ部分は、単語の選択や発話の長さに基づいて、発話ごとに異なります。 LUIS には、すべての意図のすべての発話におけるジョブの例が必要です。  

ジョブ名は名詞、動詞、または複数の単語で構成されるフレーズであるため、判断するのが困難です。 例: 

|[ジョブ]|
|--|
|エンジニア|
|ソフトウェア エンジニア|
|シニア ソフトウェア エンジニア|
|エンジニアリング チーム リーダー |
|航空管制官|
|車両運転者|
|救急車運転手|
|監督|
|押出|
|機械設置工|

この LUIS アプリには、複数の意図にジョブ名があります。 すべての意図の発話で、これらの単語にラベルを付けることで、ジョブ自体と、そのジョブが発話のどこに出現するかを LUIS に認識させることができます。

## <a name="create-job-simple-entity"></a>ジョブのシンプル エンティティを作成する

1. 人事アプリは必ず、LUIS の**ビルド** セクションに配置してください。 右上のメニュー バーの **[Build]\(ビルド\)** を選択すると、このセクションに変更できます。 

2. **[Intents]\(意図\)** ページで、**[ApplyForJob]** 意図を選択します。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "\"ApplyForJob\" 意図が強調表示されている LUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. 発話 `I want to apply for the new accounting job` で、`accounting` を選択し、ポップアップ メニューの一番上のフィールドに「`Job`」と入力して、**[新しいエンティティの作成]** を選択します。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "エンティティの作成手順が強調表示されている \"ApplyForJob\" 意図を含む LUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. ポップアップ ウィンドウでエンティティ名と型を確認し、**[完了]** を選択します。

    ![名前に "ジョブ"、型に "シンプル" が指定された、シンプル エンティティの作成ポップアップ モデル ダイアログ](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. 発話 `Submit resume for engineering position` の中の `engineering` という単語にジョブのエンティティとしてラベルを付けます。 `engineering` という単語を選択し、ポップアップ メニューから **[ジョブ]** を選択します。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "ジョブ エンティティのラベル付けが強調表示されている LUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    すべての発話にラベルを付けますが、5 つの発話では、ジョブ関連の単語やフレーズについて LUIS に認識させるには不十分です。 数値が使用されているジョブには、例は不要です。正規表現エンティティが使用されているためです。 単語または語句のジョブには少なくともさらに 15 例が必要です。 

6. 発話を追加し、ジョブの単語またはフレーズを**ジョブ** エンティティとしてマークします。 ジョブの種類は、雇用サービスの雇用全体にわたって一般的なものです。 特定の業界に関連するジョブが必要な場合は、それをジョブの単語に反映させる必要があります。 

    |発話|ジョブ エンティティ|
    |:--|:--|
    |研究開発のプログラム マネージャー デスクに応募します|プログラム マネージャー|
    |料理人補佐への応募書類です。|料理人補佐|
    |キャンプ指導員に申し込むための履歴書を添付しました。|キャンプ指導員|
    |職務経歴書です。 管理アシスタントに応募します。|管理アシスタント|
    |販売管理の仕事への応募を希望しています。|管理、販売|
    |新しい経理の仕事に対する履歴書です。|経理|
    |バーテンダー アシスタントへの応募書類を添付しました。|バーテンダー アシスタント|
    |屋根職人、フレーマーへの応募書類を送ります。|屋根職人、フレーマー|
    |こちらの履歴書をご確認ください。 バス運転手に応募します。|バス運転手|
    |正看護師です。 履歴書をご確認ください。|正看護師|
    |新聞広告で拝見した教職への応募に必要な書類を提出いたします。|教職|
    |職務経歴書です。 果物/野菜の在庫管理のポストへの応募を希望します。|在庫管理|
    |タイル職人に応募します。|タイル|
    |造園技師に応募するための履歴書を添付します。|造園技師|
    |生物学教授への応募に必要な履歴書を同封します。|生物学教授|
    |写真撮影の職に応募します。|写真撮影|git 

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>GetJobInformation 意図の発話の例のエンティティにラベルを付ける
1. 左側のメニューから **[Intents]\(意図\)** を選びます。

2. 意図の一覧から **[GetJobInformation]** を選択します。 

3. 発話の例のジョブにラベルを付けます。

    |発話|ジョブ エンティティ|
    |:--|:--|
    |データベースの仕事はありますか。|データベース|
    |経理関連の新しい職を探しています|経理|
    |シニア エンジニアではどのようなポストがありますか。|シニア エンジニア|

    発話の例は他にもありますが、ジョブの単語が含まれていません。

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>異なる発話でエンドポイントにクエリを実行する

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Here is my c.v. for the programmer job`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、`ApplyForJob` 発話が返される必要があります。

```JSON
{
  "query": "Here is my c.v. for the programmer job",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.9826467
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.9826467
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0218927357
    },
    {
      "intent": "MoveEmployee",
      "score": 0.007849265
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00349470088
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00348804821
    },
    {
      "intent": "None",
      "score": 0.00319909188
    },
    {
      "intent": "FindForm",
      "score": 0.00222647213
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00211193133
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00172086991
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00138010911
    }
  ],
  "entities": [
    {
      "entity": "programmer",
      "type": "Job",
      "startIndex": 24,
      "endIndex": 33,
      "score": 0.5230502
    }
  ]
}
```

## <a name="names-are-tricky"></a>名前には注意が必要
LUIS アプリによって、確実性の高い適切な意図が検出され、ジョブ名が抽出されました。ただし、名前には注意が必要です。 発話 `This is the lead welder paperwork` を見てみましょう。  

次の JSON では、LUIS は正しい意図 `ApplyForJob` で応答していますが、`lead welder` はジョブ名として抽出されませんでした。 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

名前は何でもかまわないため、シグナルを強化する単語のフレーズ リストが LUIS にある場合、LUIS はエンティティをより正確に予測します。

## <a name="to-boost-signal-add-jobs-phrase-list"></a>シグナルを強化するためにジョブ フレーズ リストを追加する
LUIS-Samples の GitHub リポジトリから [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) ファイルを開きます。 リストに含まれるジョブの単語およびフレーズは 1,000 を超えます。 自分にとって意味のあるジョブの単語がリストにあるか確認します。 単語またはフレーズがリストにない場合は、自分で追加します。

1. LUIS アプリの **[ビルド]** セクションで、**[アプリのパフォーマンス向上]** メニューの **[フレーズ リスト]** を選択します。

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "左側の [フレーズ リスト] ナビゲーション ボタンが強調表示されているスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. **[Create new phrase list]\(新しいフレーズ リストの作成\)** を選択します。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "[Create new phrase list]\(新しいフレーズ リストの作成\) ボタンが強調表示されているスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. 新しいフレーズ リスト `Jobs` に名前を付けて、そのリストを jobs-phrase-list.csv から **[値]** テキスト ボックスにコピーします。 [Enter] を選択します。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "新しいフレーズ リストの作成ダイアログ ポップアップのスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    フレーズ リストに単語をさらに追加したい場合は、**[Related Values]\(関連する値\)** を確認し、関連のある単語を追加します。 

4. **[保存]** を選択して、フレーズ リストをアクティブにします。

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "フレーズ リストの値ボックスに単語が入力されている、新しいフレーズ リストの作成ダイアログ ポップアップのスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. フレーズ リストが使用されるように、アプリを再度[トレーニング](#train-the-luis-app)して[発行](#publish-the-app-to-get-the-endpoint-URL)します。

6. 同じ発話 `This is the lead welder paperwork.` を含むエンドポイントにクエリを再実行します。

    JSON 応答には、抽出された次のエンティティが含まれています。

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="phrase-lists"></a>フレーズ リスト
フレーズ リストを追加したことで、リスト内の単語のシグナルが強化されましたが、まだ完全一致としては使用されてい**ません**。 フレーズ リストには `lead` という単語で始まるジョブが複数存在します。また、ジョブ `welder` もありますが、`lead welder` はありません。 ジョブのこのフレーズ リストは不完全である可能性があります。 定期的に[エンドポイントの発話を確認](luis-how-to-review-endoint-utt.md)し、ジョブの単語を他に見つけて、その単語をご自身のフレーズ リストに追加してください。 その後、再度トレーニングして、再発行します。

## <a name="what-has-this-luis-app-accomplished"></a>この LUIS アプリの処理内容
シンプル エンティティと単語のフレーズ リストを含むこのアプリは、自然言語クエリの意図を識別し、ジョブ データを返しました。 

お使いのチャットボットには、現在、ジョブ応募の基本アクションと、そのアクションのパラメーター、つまり、どのジョブを参照するかを判断するための十分な情報があります。 

## <a name="where-is-this-luis-data-used"></a>この LUIS データの使用場所 
LUIS はこの要求の処理を完了しています。 チャットボットなどの呼び出し元アプリは、エンティティから topScoringIntent の結果とデータを取得し、サード パーティの API を使って、ジョブ情報を人事担当者に送信できます。 ボットまたは呼び出し元アプリケーションに他のプログラム オプションがある場合でも、LUIS はその処理を行いません。 LUIS はユーザーの意図を判断するだけです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [事前構築済みの KeyPhrase エンティティを追加する](luis-quickstart-intent-and-key-phrase.md)