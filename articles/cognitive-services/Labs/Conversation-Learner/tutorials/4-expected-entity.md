---
title: Conversation Learner アクションの "予期されるエンティティ" プロパティを使用する方法- Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner モデルの "予期されるエンティティ" プロパティを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9e013e237a996d722d958920a1310e3aaea36c52
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170910"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>アクションの "予期されるエンティティ" プロパティを使用する方法

このチュートリアルでは、アクションの [予期されるエンティティ] フィールドについて説明します。

## <a name="video"></a>ビデオ

[![チュートリアル 4 のプレビュー](http://aka.ms/cl-tutorial-04-preview)](http://aka.ms/blis-tutorial-04)

## <a name="requirements"></a>必要条件
このチュートリアルでは、general tutorial ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
アクションの [予期されるエンティティ] フィールドを使用して、アクションに対して予期されるユーザーの応答がエンティティを表わしていることをシステムに伝達します。

具体的には、アクションの [予期されるエンティティ] フィールドが $entity に設定されている場合、次回のユーザーの発話に対して、システムは以下を実行します。

1. まず、いつものように、機械学習ベースのエンティティ抽出モデルを使用して、エンティティを見つけようとします。
2. 手順 1 でエンティティが見つからない場合は、ヒューリスティックとして、ユーザーの発話全体を $entity に割り当てます。
3. `EntityDetectionCallback` をいつものように呼び出して、アクションの選択に進みます。

## <a name="steps"></a>手順

### <a name="create-the-model"></a>モデルの作成

1. Web UI で、[新しいモデル] をクリックします。
2. [名前] に、「ExpectedEntities」と入力します。 [作成] をクリックします。

### <a name="create-an-entity"></a>エンティティの作成

1. [エンティティ] をクリックし、[新しいエンティティ] をクリックします。
2. [エンティティ名] に「name」と入力します。
3. Create をクリックしてください。

> [!NOTE]
> エンティティ型は 'custom' です。 この値は、そのエンティティをトレーニングできることを示します。  また、その動作を調整できないことを示す作成済みのエンティティも存在します。  これらのエンティティは、[作成済みのエンティティのチュートリアル](./7-built-in-entities.md)で説明されています。

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>2 つのアクションの作成

1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [応答] に、「What's your name?」と入力します。
3. [予期されるエンティティ] に、「$name」と入力します。 [保存] をクリックします。
    - この値は、この質問がされ、ユーザーの応答に何もエンティティが検出されない場合、ボットはユーザーの応答全体がこのエンティティであると見なすべきであることを示します。
2. [アクション] をクリックし、[新しいアクション] をクリックして 2 つ目のアクションを作成します。
3. [応答] に、「Hello $name」と入力します。
    - このエンティティは、自動的に不適格エンティティとして追加されます。 
4. [保存] をクリックします。

これでアクションが 2 つできました。

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>ボットのトレーニング

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[What's your name?] を選択します。
    - 応答 'Hello $name' は、エンティティ $name を定義する必要があるが、$name がボットのメモリ内にないため選択できません。
2. 「david」と入力します。 
    - この名前は、エンティティとして強調表示されます。 これは、上記のヒューリスティックを設定したために、応答がエンティティとして選択されたためです。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
    - これで、name 値がボットのメモリに入りました。
    - これで、応答として、"Hello $name" を使用できます。 
6. [Hello $name] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

機械学習エンティティ抽出モデルが名前を識別する 2 つの例があります。この場合、"予期されるエンティティ" ヒューリスティックはトリガーされません。

1. [New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「my name is david」と入力します。
    - このモデルは、前にこの単語を見たことがあるため、david を name エンティティとして識別します。
2. [Score Actions]\(アクションのスコア付け\) をクリックします。
3. [Hello $name] を選択します。
4. 「my name is susan」と入力します。
    - このモデルは、既にこのパターンを見たことがあるため、susan を name として識別します。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
2. [Hello susan] を選択します。
3. [Done Teaching]\(学習の完了\) をクリックします。

次の例では、"予期されるエンティティ" ヒューリスティックがトリガーされますが、正しくありません。 そのため、これらの例では修正を行う方法が示されます。

1. 「call me jose」と入力します。
    - このモデルは、この名前をエンティティとして認識しません。
2. [Jose] をクリックし、[name] を選択します。
3. [Score Action]\(アクションのスコア付け\) をクリックします。
4. [Hello $name] を選択します。
5. [Done Teaching]\(学習の完了\) をクリックします。
1. [New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [what's your name] の応答として、「I am called frank」と入力します。
    - フレーズ全体が強調表示されます。 これは、統計モデルで name が見つからなかったため、ヒューリスティックがトリガーされ、回答全体が name エンティティとして選択されたためです。
2. これを修正するには、強調表示されているフレーズをクリックし、赤い [x] をクリックします。 
3. [frank] を選択し、[name] をクリックします。
2. [Score Actions]\(アクションのスコア付け\) をクリックします。
3. [Hello $name] を選択します。
4. [Done Teaching]\(学習の完了\) をクリックします。

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [否定可能エンティティ](./5-negatable-entities.md)
