---
title: pattern.any エンティティを使用して LUIS の予測を向上させる方法のチュートリアル - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: このチュートリアルでは、pattern.any エンティティを使用して、LUIS による意図とエンティティの予測を向上させます。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 1587debecd82072c29d4caffc2b81629b1f52b0e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527366"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>チュートリアル: pattern.any エンティティを使用してアプリを改善する

このチュートリアルでは、pattern.any エンティティを使用して意図とエンティティの予測を向上させます。  

> [!div class="checklist"]
* pattern.any を使用するタイミングと方法について説明します
* pattern.any を使用するパターンの作成
* 予測の改善を検証する方法

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>開始する前に
[パターン ロール](luis-tutorial-pattern-roles.md) チュートリアルからの人事アプリを保持していない場合は、JSON を [LUIS](luis-reference-regions.md#luis-website) Web サイトの新しいアプリに[インポート](luis-how-to-start-new-app.md#import-new-app)します。 インポートするアプリは、[LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json) GitHub リポジトリにあります。

元の Human Resources アプリを保持する場合は、[[Settings]\(設定\)](luis-how-to-manage-versions.md#clone-a-version) ページでバージョンを複製し、`patt-any` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 

## <a name="the-purpose-of-patternany"></a>pattern.any の目的
pattern.any エンティティは、エンティティの表現が原因で発話の残りの部分からエンティティの終わりを判別するのが難しい自由形式データを見つけるために使用できます。 

この人事アプリは、従業員が会社のフォームを見つける際に役立ちます。 フォームは[正規表現チュートリアル](luis-quickstart-intents-regex-entity.md)で追加されました。 そのチュートリアルのフォーム名では、正規表現を使用して、次の発話表で太字になっているフォーム名など、正しい形式のフォーム名を抽出しました。

|発話|
|--|
|**HRF-123456** はどこにありますか?|
|誰が **HRF-123234** を作成しましたか?|
|**HRF-456098** はフランス語で発行されますか?|

ただし、各フォームには、前の表で使用されているフォーマット済みの名前と、`Request relocation from employee new to the company 2018 version 5` のようなわかりやすい名前の両方があります。 

わかりやすいフォーム名を持つ発話は次のようになります。

|発話|
|--|
|**会社の新しい従業員からの配置換えリクエスト 2018 バージョン 5** はどこにありますか?|
|誰が **"会社の新しい従業員からの配置換えリクエスト 2018 バージョン 5"** を作成しましたか?|
|**会社の新しい従業員からの配置換えリクエスト 2018 バージョン 5** はフランス語で発行されますか?|

さまざまな長さには、エンティティの末尾がどこであるかについて LUIS を混乱させる可能性のあるフレーズが含まれています。 パターンで Pattern.any エンティティを使用すると、フォーム名の先頭と末尾を指定できるため、LUIS はフォーム名を正しく抽出できます。

**パターンを使用すると提供される発話の例を少なくすることができますが、エンティティが検出されない場合、パターンは一致しません。**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>既存の意図 FindForm に発話例を追加する 
FormName エンティティを作成してラベルを付けることが難しい場合、事前構築済みの keyPhrase エンティティを削除します。 

1. 上部のナビゲーションから **[ビルド]** を選択し、左側のナビゲーションから **[Intents]\(意図\)** を選択します。

2. 意図の一覧から **FindForm** を選択します。

3. 発話の例をいくつか追加します。

    |発話の例|
    |--|
    |フォーム **ラボで火災が発生したときにするべきこと** はどこにあり、私がそれを読んだ後に誰が署名する必要がありますか?|
    |**会社の新しい従業員からの配置換えリクエスト** はサーバー上のどこにありますか?|
    |"**メイン キャンパスでの健康とウェルネスのリクエスト**" を作成したのは誰で、最新バージョンはいくつですか?|
    |私は "**物理資産を含むオフィス移転リクエスト**" という名前のフォームを探しています。 |

    Pattern.any エンティティがない場合、フォーム名の多くのバリエーションのため、フォームのタイトルの末尾はどこかを LUIS が理解することは難しくなります。

## <a name="create-a-patternany-entity"></a>Pattern.any エンティティの作成
Pattern.any エンティティは、さまざまな長さのエンティティを抽出します。 パターンによってエンティティの先頭と末尾がマークされるため、これはそのパターン内でのみ機能します。 Pattern.any が含まれているパターンでエンティティが正しく抽出されない場合は、[明示的なリスト](luis-concept-patterns.md#explicit-lists)を使用してこの問題を修正します。 

1. 左側のナビゲーションの **[Entities]\(エンティティ\)** を選択します。

2. **[Create new entity]\(新しいエンティティの作成\)** を選択し、名前「`FormName`」を入力し、タイプとして **[Pattern.any]** を選択します。 **[完了]** を選択します。 

    Pattern.any はパターンでのみ有効なため、意図でエンティティにラベルを付けることはできません。 

    抽出されたたデータに number や datetimeV2 などの他のエンティティを含める場合、number と datetimeV2 に加えて Pattern.any を含む複合エンティティを作成する必要があります。

## <a name="add-a-pattern-that-uses-the-patternany"></a>Pattern.any を使用するパターンを追加する

1. 左側のナビゲーションの **[Patterns]\(パターン\)** を選択します。

2. 意図 **[FindForm]** を選択します。

3. 新しいエンティティを使用する次のテンプレート発話を入力します。

    |テンプレート発話|
    |--|
    |フォーム ["]{FormName}["] はどこにあり、私がそれを読んだ後に誰が署名する必要がありますか[?]|
    |["]{FormName}["] はサーバー上のどこにありますか[?]|
    |["]{FormName}["] を作成したのは誰で、最新バージョンはいくつですか[?]|
    |私は ["]{FormName}["] という名前のフォームを探しています[。]|

    二重引用符の代わりに一重引用符、疑問符の代わりにピリオド、のようなフォームのバリエーションを考慮したい場合、各バリエーション用の新しいパターンを作成します。

4. keyPhrase を削除した場合、もう一度アプリに追加します。 

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>新しいパターンで自由形式のデータが抽出されるかどうかをテストする
1. 上部のバーの **[Test]\(テスト\)** を選択して、テスト パネルを開きます。 

2. 次の発話を入力します。 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. 結果の下の **[検査]** を選択して、エンティティと意図のテスト結果を確認します。

    まずエンティティ `FormName` が検出され、次にパターンが検出されて意図が決定されます。 テスト結果でエンティティが検出されず、その結果としてパターンが検出されない場合は、(パターンではなく) その意図の発話の例をさらに追加する必要があります。

4. 上部のナビゲーションの **[Test]\(テスト\)** を選択してテスト パネルを閉じます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [パターンと共にロールを使用する方法について](luis-tutorial-pattern-roles.md)