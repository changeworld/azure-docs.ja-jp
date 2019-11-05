---
title: 意図の追加 - LUIS
titleSuffix: Azure Cognitive Services
description: 意図を LUIS アプリに追加して、同じ意図を持つ質問またはコマンドのグループを特定します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 1f2f001489552203f0157dd24356341eb3184c81
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467549"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>ユーザーの発話意図を判断する意図を追加する

[意図](luis-concept-intent.md)を LUIS アプリに追加して、同じ意図を持つ質問またはコマンドのグループを特定します。 

インテントを管理するには、上部のナビゲーション バーの **[ビルド]** セクションに移動し、左側のパネルの **[Intents]\(意図\)** を選択します。 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-intent"></a>意図を追加する

1. **[Intents]\(意図\)** ページで、 **[Create new intent]\(意図の新規作成\)** を選びます。

1. **[Create new intent]\(意図の新規作成\)** ダイアログ ボックスで、意図の名前 (`GetEmployeeInformation`) を入力し、 **[完了]** をクリックします。

    ![意図の追加](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>発話の例を追加する

発話の例とは、ユーザーの質問や命令のサンプル テキストです。 Language Understanding (LUIS) に学習させるには、意図に発話の例を追加する必要があります。

1. **[GetEmployeeInformation]** 意図詳細ページで、意図の名前の下にあるテキスト ボックスに、該当するユーザーから想定される関連する発話 (`Does John Smith work in Seattle?` など) を入力します。
 
    ![意図の詳細ページのスクリーンショット、発話が強調表示されています](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS では、すべての発話が小文字に変換され、ハイフンなどのトークンの前後にはスペースが追加されます。

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>意図予測のエラー 

意図に含まれている発話の例では、発話の例が現在含まれている意図とトレーニング中に決定された予測意図の間に意図予測のエラーが存在することがあります。 

発話予測のエラーを見つけて修正するには、 **[フィルター]** オプションの [Incorrect] (正しくない) および [Unclear] (不明) の **[評価]** オプションを **[詳細ビュー]** の **[表示]** オプションと組み合わせて使用します。 

![発話予測のエラーを見つけて修正するには、[フィルター] オプションを使用します。](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

フィルターとビューが適用され、エラーを含む発話の例が存在する場合は、発話の例のリストにそれらの発話と問題が表示されます。

![![フィルターとビューが適用され、エラーを含む発話の例が存在する場合は、発話の例のリストにそれらの発話と問題が表示されます。](./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

各行には、発話の例の現在のトレーニングの予測スコア、最も近いライバルのスコア、これらの 2 つのスコアの差が表示されます。 

### <a name="fixing-intents"></a>意図の修正

意図予測のエラーを修正する方法を学習するには、[概要ダッシュボード](luis-how-to-use-dashboard.md)を使用します。 概要ダッシュボードには、アクティブなバージョンの最後のトレーニングに関する分析が表示され、モデルを修正するための最上位の提案が示されます。  

## <a name="add-a-custom-entity"></a>カスタム エンティティの追加

意図に発話を追加したら、発話内からテキストを選択してカスタム エンティティを作成できます。 カスタム エンティティは、抽出するテキスト (および正しい意図) をタグ付けするためのものです。 

詳細については、[発話に対するエンティティの追加](luis-how-to-add-example-utterances.md)に関するページを参照してください。

## <a name="entity-prediction-discrepancy-errors"></a>エンティティ予測の不一致エラー 

エンティティに赤い下線が引かれ、[エンティティ予測不一致](luis-how-to-add-example-utterances.md#entity-status-predictions)があることが示されます。 これがエンティティの最初のオカレンスであるため、このテキストが正しいエンティティでタグ付けされていることを LUIS が自信を持って判断するための十分なサンプルがないことを意味しています。 この不一致は、アプリのトレーニングを実行すると解消されます。 

![意図の詳細ページのスクリーンショット (カスタム エンティティ名が青で強調表示されている様子)](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

テキストが青で強調表示され、それがエンティティであることが示されます。  

## <a name="add-a-prebuilt-entity"></a>作成済みエンティティの追加

詳しくは、「[事前構築済みのエンティティ](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app)」をご覧ください。

## <a name="using-the-contextual-toolbar"></a>コンテキスト ツールバーの使用

一覧で 1 つ以上のサンプル発話が選択されているときに発話の左側のチェック ボックスをオンにすると、発話一覧の上部にあるツール バーを使用して次のアクションを実行できます。

* 意図の再割り当て: 発話を別の意図に移動します
* 発話の削除
* エンティティ フィルター: フィルター処理されたエンティティを含んでいる発話だけを表示します
* すべて表示/エラーのみ: 予測エラーのある発話を表示するか、すべての発話を表示します
* エンティティ/トークン ビュー: エンティティ名付きのエンティティ ビューを表示するか、発話の未加工テキストを表示します
* 虫眼鏡: 特定のテキストを含んでいる発話を検索します

## <a name="working-with-an-individual-utterance"></a>個別の発話の操作

次の操作は、発話の右側にある省略記号のメニューから、個別の発話に対して実行できます。

* 編集: 発話のテキストを変更します
* 削除: 発話を意図から削除します。 発話がまだ必要な場合は、その発話を **None** の意図に移動するほうが合理的です。 
* パターンの追加: パターンを使用すると、共通の発話を使用して、置き換え可能なテキストと無視可能なテキストをマークし、意図内の発話の数を減らすことができます。 

**[Labeled intent]\(ラベル付き意図\)** 列では、発話の意図を変更できます。

## <a name="train-your-app-after-changing-model-with-intents"></a>意図を含むモデルの変更後にアプリをトレーニングする

意図を追加、編集、または削除したら、その変更がエンドポイントのクエリに反映されるように、アプリを[トレーニング](luis-how-to-train.md)して[発行](luis-how-to-publish-app.md)します。 

## <a name="next-steps"></a>次の手順

[発話の例](luis-how-to-add-example-utterances.md)にエンティティを追加する方法について学習する。 
