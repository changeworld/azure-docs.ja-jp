---
title: Conversation Learner アプリケーションでエンティティ検出コールバックを使用する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner アプリケーションでエンティティ検出コールバックを使用する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e41ea5930ff0c8395d0c93aa42e224ebfc894ba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376256"
---
# <a name="how-to-use-entity-detection-callback"></a>エンティティ検出コールバックを使用する方法

このチュートリアルでは、エンティティ検出コールバックを紹介し、エンティティ解決の一般的なパターンを例示します。

## <a name="requirements"></a>必要条件
このチュートリアルでは、"tutorialEntityDetectionCallback" ボットが実行されている必要があります。

    npm run tutorial-entity-detection

## <a name="details"></a>詳細
エンティティ検出コールバックを使うと、カスタム コードからエンティティに関連したビジネス ルールを扱うことができます。 ここでは、コールバックとプログラマティック エンティティを使用して、ユーザーによって入力された都市名を正規名に解決するデモを行います。その例として、"the big apple" を "new york" に解決します。

### <a name="open-the-demo"></a>デモを開く

アプリ一覧で、[Tutorial-10-EntityDetenctionCallback] をクリックします。 

### <a name="entities"></a>エンティティ

アプリケーションには、3 つのエンティティを定義してあります。

![](../media/tutorial10_entities.PNG)

1. City はカスタム エンティティです。ユーザーがテキスト入力として渡すことになります。
2. CityUnknown はプログラマティック エンティティです。 このデータはシステムにより事前設定されます。 ユーザーによって入力された都市をシステムが認識できない場合、ユーザー入力がコピーされます。
3. CityResolved は、システムによって認識されている都市です。 これは都市の正規名になります。たとえば "the big apple" は "new york" に解決されます。

### <a name="actions"></a>アクション

アクションは 3 つ作成してあります。 

![](../media/tutorial10_actions.PNG)

1. 1 つ目のアクションは "Which city do you want?" です。
2. 2 つ目は "I don't know this city, $CityUknown. Which city do you want?" です。
3. 3 つ目は "You said $City, and I resolved that to $CityResolved." です。

### <a name="callback-code"></a>コールバック コード

それではコードを見てみましょう。 C:\<installedpath>\src\demos\tutorialSessionCallbacks.ts ファイルには EntityDetectionCallback メソッドがあります。

![](../media/tutorial10_callbackcode.PNG)

この関数は、エンティティの解決が実行された後に呼び出されます。
 
- 最初の処理は $CityUknown のクリアです。 常に最初の段階でクリアされるため、$CityUknown が保持されるのは、1 回のターンに限られます。
- 次に、既に認識されている都市のリストを取得します。 先頭の要素を取得して、その解決を試みます。
- その解決に使用される関数 (resolveCity) は、このコード内のさらに上の方で定義されています。 そこには、都市の正規名のリストが存在します。 そのリストから都市名を探し出して返します。 それ以外の場合は、"cityMap" を参照し、一致した名前を返します。 都市が見つからなかった場合は、null を返します。
- 最後に、都市名が解決された場合は、その名前を $CityKnown エンティティに格納します。 それ以外の場合は、ユーザーが発話した内容をクリアして、$CityUknown エンティティを事前設定します。

### <a name="train-dialogs"></a>トレーニング会話

1. [Train Dialogs]\(トレーニング会話\) をクリックし、[New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[Which city do you want?] を選択します。
2. 「new york」と入力します。
    - 入力した内容が city エンティティとして認識されることに注意してください。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
    - City と CityResolved が事前設定されていることに注意してください。
6. [You said $City, and I resolved that to $CityResolved] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

別の会話例を追加します。

1. [New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[Which city do you want?] を選択します。
2. 「big apple」と入力します。
    - 入力した内容が city エンティティとして認識されることに注意してください。
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
    - CityResolved に、コードの実行結果が反映されていることに注意してください。
6. [You said $City, and I resolved that to $CityResolved] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

別の会話例を追加します。

1. [New Train Dialog]\(新しいトレーニング会話\) をクリックします。
2. 「hello」と入力します。
3. [Score Actions]\(アクションのスコア付け\) をクリックし、[Which city do you want?] を選択します。
2. 「foo」と入力します。
    - これは、システムによって認識されない都市の例です。 
5. [Score Actions]\(アクションのスコア付け\) をクリックします。
6. I don't know this city, $CityUknown.  Which city do you want? を選択します。
7. 「new york」と入力します。
8. [Score Action]\(アクションのスコア付け\) をクリックします。
    - CityUknown がクリアされ、CityResolved が事前設定されていることに注意してください。
6. [You said $City, and I resolved that to $CityResolved] を選択します。
7. [Done Teaching]\(学習の完了\) をクリックします。

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [セッション コールバック](./11-session-callbacks.md)
