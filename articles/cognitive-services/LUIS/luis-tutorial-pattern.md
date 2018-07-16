---
title: パターンを使用して LUIS の予測を向上させる方法のチュートリアル - Azure | Microsoft Docs
titleSuffix: Azure
description: このチュートリアルでは、意図のパターンを使用して、LUIS による意図とエンティティの予測を向上させます。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ff5572366be548132b28e5ce03b9595e7f98128c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265318"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>チュートリアル: パターンを使用して予測を向上させる

このチュートリアルでは、パターンを使用して意図とエンティティの予測を向上させます。  

> [!div class="checklist"]
* パターンがアプリで役立つことを確認する方法
* パターンを作成する方法 
* パターンで事前構築済みエンティティとカスタム エンティティを使用する方法 
* パターン予測の改善を検証する方法
* エンティティに役割を追加して、文脈ベースのエンティティを検出する方法
* Pattern.any を追加して、自由形式のエンティティを検出する方法

この記事に従って LUIS アプリケーションを作成するには、無料の [LUIS][LUIS] アカウントが必要です。

## <a name="import-humanresources-app"></a>HumanResources アプリをインポートする
このチュートリアルでは、HumanResources アプリをインポートします。 このアプリには、None、GetEmployeeOrgChart、GetEmployeeBenefits の 3 つの意図があります。 また、number と Employee の 2 つの事前構築済みエンティティがあります。 Employee エンティティは、従業員の名前を抽出する単純なエンティティです。 

1. 新しい LUIS アプリ ファイルを作成し、`HumanResources.json` という名前を付けます。 

2. このファイルに次のアプリ定義をコピーします。

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. LUIS の **[アプリ]** ページで、**[Import new app]\(新しいアプリのインポート\)** を選択します。 

4. **[Import new app]\(新しいアプリのインポート\)** ダイアログで、手順 1. で作成した `HumanResources.json` ファイルを選択します。

5. 意図 **GetEmployeeOrgChart** を選択し、**[Entities view]\(エンティティ ビュー\)** から **[Tokens view]\(トークン ビュー\)** に変更します。 発話の例がいくつか表示されます。 各発話には名前 (Employee エンティティ) が含まれています。 名前はそれぞれ異なっており、発話ごとに表現が異なることに注意してください。 この多様性により、LUIS はさまざまな発話を学習できます。

    ![[Entities view]\(エンティティ ビュー\) の切り替えを示す意図ページのスクリーンショット](media/luis-tutorial-pattern/utterances-token-view.png)

6. 上部のナビゲーション バーの **[Train]\(トレーニング\)** を選択して、アプリをトレーニングします。 緑色の成功バーが表示されるまで待ちます。

7. 上部のパネルの **[Test]\(テスト\)** を選択します。 「`Who does Patti Owens report to?`」と入力し、Enter キーを押します。 発話の下の **[検査]** を選択して、テストの詳細を確認します。
    
    Patti Owens という従業員名は、発話の例でまだ使用されていません。 これは、この発話が意図 `GetEmployeeOrgChart` の発話であり、Employee エンティティが `Patti Owens` であることを LUIS がどの程度学習しているかを確認するテストです。 意図 `GetEmployeeOrgChart` の結果は 50% (.50) を下回っています。 意図は正しいのですが、低いスコアになっています。 また、Employee エンティティも `Patti Owens` として正しく識別されています。 パターンにより、この初期予測スコアが向上します。 

    ![テスト パネルのスクリーンショット](media/luis-tutorial-pattern/original-test.png)

8. 上部のナビゲーションの **[Test]\(テスト\)** を選択してテスト パネルを閉じます。 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>パターンによって少ない例で一般的な発話を LUIS に学習させる
人事ドメインの性質上、組織内の従業員の関係について一般的な尋ね方がいくつかあります。 例: 

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

発話の多数の例を提供せずに、それぞれの文脈の一意性を判断するには、これらの発話は類似しすぎています。 意図のパターンを追加することにより、発話の多数の例を提供しなくても、LUIS は意図の一般的な発話パターンを学習します。 

この意図のテンプレート発話の例を次に示します。

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

パターンは、正規表現の照合と機械学習の組み合わせです。 次に、LUIS がパターンを学習するためのテンプレート発話の例をいくつか提供します。 意図の発話と共に、これらの例によって、どのような発話が意図に適合し、発話内のどこにエンティティが存在しているかについて、LUIS は理解を深めることができます。 <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>テンプレート発話を追加する

1. 左側のナビゲーションの **[Improve app performance]\(アプリのパフォーマンスの改善\)** で、**[Patterns]\(パターン\)** を選択します。

2. 意図 **[GetEmployeeOrgChart]** を選択し、次のテンプレート発話を入力します。各テンプレート発話の入力後に Enter キーを押して 1 つずつ入力してください。

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    `{Employee}` 構文では、テンプレート発話内でのエンティティの位置とそれがどのエンティティであるかがマークされます。 

    ![意図のテンプレート発話の入力を示すスクリーンショット](./media/luis-tutorial-pattern/enter-pattern.png)

3. 上部のナビゲーション バーの **[Train]\(トレーニング\)** を選択します。 緑色の成功バーが表示されるまで待ちます。

4. 上部のパネルの **[Test]\(テスト\)** を選択します。 テキスト ボックスに「`Who does Patti Owens report to?`」と入力します。 Enter キーを押します。 これは、前のセクションでテストしたものと同じ発話です。 意図 `GetEmployeeOrgChart` の結果が改善されていることが期待されます。 

    今回はスコアが大幅に向上しています。 多数の例を提供しなくても、LUIS はこの意図に関連するパターンを学習しました。

    ![高いスコアの結果を示すテスト パネルのスクリーンショット](./media/luis-tutorial-pattern/high-score.png)

    まずエンティティが検出され、次にパターンが検出されて意図が示されます。 テスト結果でエンティティが検出されず、その結果としてパターンが検出されない場合は、(パターンではなく) その意図の発話の例をさらに追加する必要があります。 

5. 上部のナビゲーションの **[Test]\(テスト\)** を選択してテスト パネルを閉じます。

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>役割を持つエンティティをパターンで使用する
LUIS アプリを使用すると、従業員をある場所から別の場所に移動させる際に役立ちます。 発話の例として、`Move Bob Jones from Seattle to Los Colinas` があります。 この発話内の場所はそれぞれ異なる意味を持ちます。 Seattle は移動の出発地であり、Los Colinas は目的地です。 パターンでこれらの場所を区別するために、以下のセクションでは、出発地と目的地の 2 つの役割を持つ、場所の単純なエンティティを作成します。 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>人と資産を移動させるための新しい意図を作成する
人または資産の移動に関する発話の新しい意図を作成します。

1. 左側のナビゲーションの **[Intents]\(意図\)** を選択します。
2. **[Create new intent]\(意図の新規作成\)** を選択します。
3. 新しい意図に `MoveAssetsOrPeople` という名前を付けます。
4. 次の発話の例を追加します。

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![意図 MoveAssetsOrPeople の発話の例のスクリーンショット](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    発話の例の目的は十分な例を提供することです。 後のテストで場所エンティティが検出されず、その結果としてパターンが検出されない場合は、この手順に戻り、例をさらに追加します。 その後、トレーニングしてもう一度テストします。 

5. 発話内で名、姓の順に選択し、一覧で Employee エンティティを選択することによって、発話の例のエンティティを Employee エンティティでマークします。

    ![Employee エンティティでマークされた MoveAssetsOrPeople の発話のスクリーンショット](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. `move travis hinton from portland to orlando` という発話内の `portland` というテキストを選択します。 ポップアップ ダイアログで、新しいエンティティの名前として「`Location`」と入力し、**[Create new entity]\(新しいエンティティの作成\)** を選択します。 **Simple** エンティティ型を選択し、**[Done]\(完了\)** を選択します。

    ![新しい場所エンティティの作成を示すスクリーンショット](./media/luis-tutorial-pattern/create-new-location-entity.png)

    発話内の残りの場所名をマークします。 

    ![すべてのエンティティがマークされていることを示すスクリーンショット](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    単語の選択と順番にパターンがあることは前の画像で明らかです。 パターンを使用して**おらず**、その意図の発話に明白なパターンがある場合は、パターンを使用する方がよいことを示しています。 

    パターンではなく、さまざまな発話を必要としている場合、これらの発話の例は適していません。 その場合、用語や単語の選択、発話の長さ、エンティティの配置が異なるさまざまな発話が必要になります。 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>場所エンティティに役割を追加する 
役割はパターンにのみ使用できます。 Location エンティティに Origin と Destination の役割を追加します。 

1. 左側のナビゲーションの **[Entities]\(エンティティ\)** を選択し、エンティティの一覧で **[Location]** を選択します。

2. エンティティに `Origin` と `Destination` の役割を追加します。

    ![役割が追加された新しいエンティティのスクリーンショット](./media/luis-tutorial-pattern/location-entity.png)

    役割は意図の発話には存在しないため、MoveAssetsOrPeople の意図ページではこれらの役割はマークされません。 これらはパターンのテンプレート発話にのみ存在します。 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>場所と目的地の役割を使用するテンプレート発話を追加する
新しいエンティティを使用するテンプレート発話を追加します。

1. 左側のナビゲーションの **[Patterns]\(パターン\)** を選択します。

2. 意図 **[MoveAssetsOrPeople]** を選択します。

3. 新しいエンティティを使用する新しいテンプレート発話 `Move {Employee} from {Location:Origin} to {Location:Destination}` を入力します。 テンプレート発話内のエンティティと役割の構文は `{entity:role}` です。

    ![役割が追加された新しいエンティティのスクリーンショット](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. 新しい意図、エンティティ、パターンでアプリをトレーニングします。

### <a name="test-the-new-pattern-for-role-data-extraction"></a>新しいパターンで役割データが抽出されるかどうかをテストする
テストで新しいパターンを検証します。

1. 上部のパネルの **[Test]\(テスト\)** を選択します。 
2. 発話 `Move Tammi Carlson from Bellingham to Winthrop` を入力します。
3. 結果の下の **[検査]** を選択して、エンティティと意図のテスト結果を確認します。

    ![役割が追加された新しいエンティティのスクリーンショット](./media/luis-tutorial-pattern/test-with-roles.png)

    まずエンティティが検出され、次にパターンが検出されて意図が示されます。 テスト結果でエンティティが検出されず、その結果としてパターンが検出されない場合は、(パターンではなく) その意図の発話の例をさらに追加する必要があります。 

4. 上部のナビゲーションの **[Test]\(テスト\)** を選択してテスト パネルを閉じます。

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Pattern.any エンティティを使用してパターン内の自由形式のエンティティを検出する
この HumanResources アプリは、従業員が会社のフォームを見つける際にも役立ちます。 フォームの多くは、長さが異なるタイトルが付けられています。 さまざまな長さには、フォーム名の末尾がどこであるかについて LUIS を混乱させる可能性のあるフレーズが含まれています。 パターンで **Pattern.any** エンティティを使用すると、フォーム名の先頭と末尾を指定できるため、LUIS はフォーム名を正しく抽出できます。 

### <a name="create-a-new-intent-for-the-form"></a>フォーム用の新しい意図を作成する
フォームを探す発話の新しい意図を作成します。

1. 左側のナビゲーションの **[Intents]\(意図\)** を選択します。

2. **[Create new intent]\(意図の新規作成\)** を選択します。

3. 新しい意図に `FindForm` という名前を付けます。

4. 発話の例を追加します。

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![役割が追加された新しいエンティティのスクリーンショット](./media/luis-tutorial-pattern/intent-findform.png)

    フォームのタイトルは `What to do when a fire breaks out in the Lab` です。 この発話ではフォームの場所を尋ねており、従業員が読んだことを確認するために誰が署名する必要があるのかも尋ねています。 Pattern.any エンティティがない場合、フォームのタイトルの末尾はどこかを理解し、発話のエンティティとしてフォームのタイトルを抽出することは難しくなります。

### <a name="create-a-patternany-entity-for-the-form-title"></a>フォームのタイトルの Pattern.any エンティティを作成する
Pattern.any エンティティでは、さまざまな長さのエンティティを作成できます。 パターンによってエンティティの先頭と末尾がマークされるため、これはそのパターン内でのみ機能します。 Pattern.any が含まれているパターンでエンティティが正しく抽出されない場合は、[明示的なリスト](luis-concept-patterns.md#explicit-lists)を使用してこの問題を修正します。 

1. 左側のナビゲーションの **[Entities]\(エンティティ\)** を選択します。

2. **[Create new entity]\(新しいエンティティの作成\)** を選択します。 

3. エンティティに `FormName` という名前を付け、**Pattern.any** 型を指定します。 このチュートリアルでは、このエンティティに役割を追加する必要はありません。

    ![エンティティ名とエンティティ型を指定するダイアログ ボックスの画像](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Pattern.any を使用するパターンを追加する

1. 左側のナビゲーションの **[Patterns]\(パターン\)** を選択します。

2. 意図 **[FindForm]** を選択します。

3. 新しいエンティティを使用するテンプレート発話 `Where is the form {FormName} and who needs to sign it after I read it?` を入力します。

    ![Pattern.any エンティティを使用するテンプレート発話のスクリーンショット](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. 新しい意図、エンティティ、パターンでアプリをトレーニングします。

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>新しいパターンで自由形式のデータが抽出されるかどうかをテストする
1. 上部のバーの **[Test]\(テスト\)** を選択して、テスト パネルを開きます。 

2. 発話 `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?` を入力します。

3. 結果の下の **[検査]** を選択して、エンティティと意図のテスト結果を確認します。

    ![Pattern.any エンティティを使用するテンプレート発話のスクリーンショット](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    まずエンティティが検出され、次にパターンが検出されて意図が示されます。 テスト結果でエンティティが検出されず、その結果としてパターンが検出されない場合は、(パターンではなく) その意図の発話の例をさらに追加する必要があります。

4. 上部のナビゲーションの **[Test]\(テスト\)** を選択してテスト パネルを閉じます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS アプリを削除します。 削除するには、アプリ リストのアプリ名の右にある 3 つのドット メニュー (...) を選択し、**[Delete]\(削除\)** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** ポップアップ ダイアログで、**[OK]** をクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [フレーズ リストを追加して予測を向上させる](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions