---
title: Conversation Learner アクションの "予期されるエンティティ" プロパティを使用する方法- Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner アプリケーションの "予期されるエンティティ" プロパティの使用方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83911eba8112cf356af8c4cd562a87f746fbabc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376304"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>アクションの "予期されるエンティティ" プロパティを使用する方法

このチュートリアルでは、アクションの [予期されるエンティティ] フィールドについて説明します。

## <a name="requirements"></a>必要条件
このチュートリアルでは、汎用チュートリアル ボットが実行されている必要があります。

    npm run tutorial-general

## <a name="details"></a>詳細
アクションの [予期されるエンティティ] フィールドを使用して、アクションに対して予期されるユーザーの応答がエンティティを表わしていることをシステムに伝達します。

具体的には、アクションの [予期されるエンティティ] フィールドが $entity に設定されている場合、次回のユーザーの発話に対して、システムは以下を実行します。

1. まず、いつものように、機械学習ベースのエンティティ抽出モデルを使用して、エンティティを見つけようとします。
2. 手順 1 でエンティティが見つからない場合は、ヒューリスティックとして、ユーザーの発話全体を $entity に割り当てます。
3. `EntityDetectionCallback` をいつものように呼び出して、アクションの選択に進みます。

## <a name="steps"></a>手順

### <a name="create-the-application"></a>アプリケーションを作成する

1. Web UI で、[新しいアプリ] をクリックします。
2. [名前] に、「ExpectedEntities」と入力します。 [作成] をクリックします。

### <a name="create-an-entity"></a>エンティティを作成する

1. [エンティティ] をクリックし、[新しいエンティティ] をクリックします。
2. [エンティティ名] に「name」と入力します。
3. [作成] をクリックします。

エンティティ型が "custom" になっていることに注意してください。これは、そのエンティティをトレーニングできることを意味します。  また、動作を調整できないことを意味する既製のエンティティもあります。既製のエンティティについては、別のチュートリアルで取り上げます。

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>2 つのアクションを作成する

1. [アクション] をクリックし、[新しいアクション] をクリックします。
2. [応答] に、「What's your name?」と入力します。
3. [予期されるエンティティ] に、「$name」と入力します。 [保存] をクリックします。
    - これは、この質問をしたときにユーザーの応答に検出されるエンティティが含まれていない場合、ボットは、ユーザーの応答全体がこのエンティティであるとみなす必要があることを意味します。
2. [アクション] をクリックし、[新しいアクション] をクリックして 2 つ目のアクションを作成します。
3. [応答] に、「Hello $name」と入力します。
    - エンティティは、自動的に不適格エンティティとして追加されます。 
4. [保存] をクリックします。

これでアクションが 2 つできました。

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>ボットをトレーニングする

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[What's your name?] を選択します。
    - 応答 "Hello $name" は選択できないことに注意してください。これは、エンティティ $name を定義する必要がありますが、$name はボットのメモリに存在しないためです。
2. 「david」と入力します。 
    - エンティティとして名前が強調表示されることに注目してください。 これは、上記のヒューリスティックを設定したために、応答がエンティティとして選択されたためです。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
    - これで、ボットのメモリ内に name 値が存在するようになりました。
    - これで、応答として、"Hello $name" を使用できます。 
6. [Hello $name] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

機械学習エンティティ抽出モデルが名前を識別する 2 つの例があります。この場合、"予期されるエンティティ" ヒューリスティックはトリガーされません。

1. [New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「my name is david」と入力します。
    - 前にこの単語を見たことがあるため、david が name エンティティとして識別されます。
2. [Score Actions]\(アクションのスコア付け\) をクリックします。
3. [Hello $name] を選択します。
4. 「my name is susan」と入力します。
    - このパターンはすでに見たことがあるため、susan が name として識別されます。
2. [Score Action]\(アクションのスコア付け\) をクリックします。
2. [Hello susan] を選択します。
3. [Done Teaching]\(学習の完了\) をクリックします。

"予期されるエンティティ" ヒューリスティックがトリガーされる例がさらに 2 つあります。これらは不正解の例であり、どのように修正できるかについて説明します。

1. 「call me jose」と入力します。
    - この名前は、エンティティとして認識されません。
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
