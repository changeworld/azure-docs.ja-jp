---
title: Conversation Learner モデルのエンティティ リゾルバー - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner のエンティティ リゾルバーを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cc1f9ea18d425679a4db9c7b91a1c0a3c4451d4b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796658"
---
# <a name="entity-resolvers"></a>エンティティ リゾルバー

このチュートリアルでは、Conversation Learner のエンティティ リゾルバーを使用する方法について説明します

## <a name="video"></a>ビデオ

[![エンティティ リゾルバーのチュートリアルのプレビュー](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細

- [Resolver Type]\(リゾルバーの種類\) は、カスタム エンティティの省略可能なプロパティです。
- エンティティ リゾルバーは、LUIS の事前トレーニング済みのエンティティ認識エンジンの能力を利用して、カスタム エンティティに追加の詳細とわかりやすさを提供します。

## <a name="steps"></a>手順

### <a name="create-a-new-model"></a>新しいモデルを作成する

1. Web UI で、[新しいモデル] をクリックします。
2. [名前] フィールドに「Entity Resolvers」(エンティティ リゾルバー) と入力し、Enter キーを押すか、[作成] ボタンをクリックします。

### <a name="create-a-pair-of-entities"></a>エンティティのペアを作成する

1. 左側のパネルで、[エンティティ]、[新しいエンティティ] ボタンの順にクリックします。
2. [エンティティ名] フィールドに、「departure」(出発) と入力します。
3. [Resolver Type]\(リゾルバーの種類\) ドロップダウンから、"datetimeV2" を選択します。
4. [作成] ボタンをクリックします。
5. 情報ポップアップを読んだら、[OK] をクリックします。
6. 同じ手順に従って、"return" (戻り) という名前の別のエンティティを作成します。これもリゾルバーの種類が "datetimeV2" です。

### <a name="create-a-pair-of-actions"></a>アクションのペアを作成する

1. 左側のパネルで [アクション] をクリックし、[新しいアクション] をクリックします。
2. [Bot's response...]\(ボットの応答...\) のフィールドで、「You are leaving on $departure and returning on $return」(出発日は $departure で、戻りは $return です) と入力します。
    - 重要 - 「$[entityName]」と入力した場合は、Enter キーを押すか、ドロップ ダウンでエンティティをクリックする必要があります。そうしないと、Conversation Learner ではこれがエンティティではなくテキストと見なされます。
    - [Required Entities]\(必須エンティティ\) フィールドにもこれらのエンティティが表示され、削除できないことにご注意ください。 これにより、両方の必須エンティティが提示されるまで、このアクションは使用可能になりません。
3. [作成] ボタンをクリックします。
4. [新しいアクション] ボタンをもう一度クリックして、2 番目のアクションを作成します。
5. [Bot's response...]\(ボットの応答...\) フィールドで、「When are you planning to travel?」(旅行の予定はいつですか?) と入力します。
6. [Disqualifying Entities]\(不適格エンティティ\) フィールドで「departure」(出発) と入力し、「return」(戻り) も入力します。
    - これらは、これらのエンティティのいずれかに値が含まれている場合にこのアクションを実行しないようボットに伝えます。
7. [作成] ボタンをクリックします。


### <a name="training"></a>トレーニング

1. ページの左上部分にある "Training: [Status]" (トレーニング: [状態]) を参照し、"完了" になるまで待ちます。
    - この時間がかかりすぎる場合は、[Refresh]\(更新\) リンクをクリックすることができます。
    - トレーニングの "完了" 状態は、モデルをトレーニングするときにエンティティ リゾルバーが動作するために必要です。
2. 左側のパネルで、[Train Dialogs]\(トレーニング会話\) をクリックしてから、[New Train Dialog]\(新しいトレーニング会話\) ボタンをクリックします。
3. 最初のユーザー発話「book me a flight」(フライトを予約してください) を入力します。 
4. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
5. 応答 "When are you planning to travel?" (旅行の予定はいつですか?) を選択します。
6. ユーザーとして、「leaving tomorrow and returning Sunday next week」(明日出発し、来週の日曜日に戻ります) と応答します。
    - Conversation Learner によってそのユーザーの操作中に 2 つの "事前トレーニング済み日付" エンティティがどのように検出されたかにご注意ください。
7. [Entity Detection]\(エンティティの検出\) パネルで、テキスト "tomorrow" (明日) を選択し、「departure」(出発) というラベルを付けます
8. また、テキスト "Sunday next week" (来週の日曜日) に「return」(戻り) というラベルを付けます
9. [Score Actions]\(アクションのスコア付け\) ボタンをクリックします。
    - [メモリ] ウィンドウに、departure (出発) と return (戻り) の日付がどのように表示されるかにご注意ください。
    - それぞれにカーソルを合わせて、エンティティが日付オブジェクトであり、"Sunday" (日曜日) や "tomorrow" (明日) ではなく実際のカレンダー日付が明確にキャプチャされることを確認します。
10. "You are leaving on..." (出発日は...) を選択しますボットが応答します。
11. [保存] ボタンをクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [代替入力](./10-alternative-inputs.md)
