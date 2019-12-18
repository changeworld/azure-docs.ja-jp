---
title: エンティティの追加 - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) アプリでユーザーの発話から重要なデータを抽出するエンティティを作成します。 抽出されたエンティティ データは、顧客の要求に応える目的で、クライアント アプリケーションによって使用されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383681"
---
# <a name="add-entities-to-extract-data"></a>データを抽出するエンティティを追加する 

Language Understanding (LUIS) アプリでユーザーの発話から重要なデータを抽出するエンティティを作成します。 抽出されたエンティティ データは、顧客の要求に応える目的で、お使いのクライアント アプリケーションによって使用されます。

エンティティは、抽出しようとしている発話内の単語またはフレーズを表します。 エンティティは、意図に関連する情報を説明し、アプリがタスクを実行するうえで不可欠なこともあります。 エンティティは意図にサンプル発話を追加するときに作成できます。あるいは、サンプル発話を意図に追加するとき以外で (追加の前後で) 作成できます。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>エンティティを計画し、作成してラベルを付ける

機械学習エンティティはサンプル発話から作成するか、 **[エンティティ]** ページから作成できます。 

一般的に、ベスト プラクティスは、ポータルで機械学習エンティティを作成する前に、時間をかけてエンティティを計画することです。 その後、サブコンポーネント、記述子、制約についてその時点で把握しうる限りの詳細を含め、サンプル発話から機械学習エンティティを作成します。 [分解可能エンティティ チュートリアル](tutorial-machine-learned-entity.md)では、このメソッドの使用方法を実演しています。 

エンティティ計画の一環として、テキストに一致するエンティティ (事前構築済みエンティティ、正規表現エンティティ、リスト エンティティなど) の必要性が判明していることがあります。 そのようなエンティティは、サンプル発話でラベルが付けられる前に、 **[エンティティ]** ページから作成できます。 

ラベルを付けるときは、個々のエンティティにラベルを付け、機械学習された親エンティティまで構築できます。 あるいは、機械学習された親エンティティから始め、子エンティティに分解できます。 

> [!TIP] 
>クライアント アプリケーションで抽出されるとき、単語が使用されない場合でも、エンティティを示すすべての単語にラベルを付けます。 

## <a name="creating-an-entity-before-or-with-labeling"></a>ラベル付けの前かラベル付けと同時にエンティティを作成する

次の表からは、どのエンティティをどのような状況で作成するか、あるいはどのような状況で各エンティティをアプリに追加するかわかります。 

|エンティティの種類|LUIS ポータルでエンティティを作成する状況|
|--|--|
|機械学習エンティティ|エンティティまたは意図の詳細|
|リスト エンティティ|エンティティまたは意図の詳細|
|正規表現エンティティ|エンティティ|
|Pattern.any エンティティ|エンティティ|
|事前構築済みのエンティティ|エンティティ|
|事前構築済みのドメイン エンティティ|エンティティ|

エンティティはすべて、 **[エンティティ]** ページから作成できます。あるいは、 **[Intent detail]\(意図の詳細\)** ページでサンプル発話のエンティティにラベルを付けるとき、いくつかのエンティティを作成できます。 **[Intent detail]\(意図の詳細\)** ページでは、サンプル発話のエンティティにのみ_ラベルを付ける_ことができます。 

## <a name="create-a-machine-learned-entity"></a>機械学習エンティティを作成する

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>テキストに一致するエンティティを作成する

テキストに一致するエンティティを使用し、いくつかの方法でデータを抽出します。

|テキストに一致するエンティティ|目的|
|--|--|
|[リスト エンティティ](#add-list-entities-for-exact-matches)|正規名と代替形式としてのシノニムの一覧|
|正規表現エンティティ|正規表現エンティティを利用してテキストを照合する|
|[事前構築済みのエンティティ](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|数値、メール、日付など、一般的なデータ型を照合する|
|事前構築済みのドメイン エンティティ|選択したサブジェクト ドメインを使用して照合する|
|[Pattern.any](#add-a-patternany-entity)| 周囲のテキストと混同しがちなエンティティを照合する|  

事前構築済みのエンティティは、カスタム トレーニング データを用意しなくても動作します。 他のエンティティの場合、カスタム トレーニング データ (リスト エンティティのアイテムなど) か表現 (正規表現や pattern.any など) を用意する必要があります。

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>新しいカスタム エンティティを作成する方法

1. LUIS ポータルで、 **[管理]** セクションに移動し、 **[エンティティ]** ページに移動します。 
1. **[+ 作成]** を選択し、エンティティの種類を選択します。 
1. エンティティの設定を続け、完了したら **[作成]** を選択します。 

### <a name="add-list-entities-for-exact-matches"></a>完全一致のリスト エンティティを追加する

リスト エンティティとは、関連単語の集まりであり、固定かつ限定的です。 作成者はリストを変更できますが、LUIS によってリストが増減することはありません。 [list entity .json format]\(reference-entity-list.md#example-json-to-import-into-list-entity) を利用して既存のリスト エンティティにインポートすることもできます。 

次のリストは、正規名とシノニムの例になっています。 

|色 - リスト アイテム名|色 - シノニム|
|--|--|
|赤|深紅、血、リンゴ、消防車|
|青|空、青空、コバルト|
|緑|濃緑、ライム|

リスト エンティティを作成する手順を使用します。 リスト エンティティが作成されたら、意図でサンプル発話にラベルを付ける必要はありません。 リスト アイテムとシノニムは正確なテキストを使用して照合されます。 

1. **[ビルド]** セクションの左側にあるパネルで **[エンティティ]** を選択し、 **[+ 作成]** を選択します。

1. **[Create an entity type]\(エンティティの種類を作成する\)** ダイアログ ボックスにエンティティの名前を入力し (例: `Colors`)、 **[リスト]** を選択します。
1. **[Create a list entity]\(リスト エンティティを作成する\)** ダイアログ ボックスの **[Add new sublist....]\(新しいサブリストを追加する\)** にリスト アイテム名を入力し (例: `Green`)、シノニムを追加します。

    > [!div class="mx-imgBorder"]
    > ![[Entity detail]\(エンティティの詳細\) ページでリスト エンティティとして色の一覧を作成します。](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. リスト アイテムとシノニムの追加を終えたら、 **[作成]** を選択します。

    アプリに対する一連の変更が完了したら、忘れずにアプリを**トレーニング**してください。 1 回変更してアプリケーションをトレーニングすることは避けてください。 

    > [!NOTE]
    > この手順では、 **[Intent detail]\(意図の詳細\)** ページのサンプル発話からリスト エンティティが作成され、ラベルが付けられます。 **[エンティティ]** ページから同じエンティティを作成することもできます。

## <a name="add-a-role-for-an-entity"></a>エンティティのロールを追加する

ロールは、文脈に左右される、エンティティの名前付きサブタイプです。 

### <a name="add-a-role-to-distinguish-different-contexts"></a>異なるコンテキストを区別するためにロールを追加する

次の発話には場所が 2 つあり、いずれも、`to` や `from` など、それを囲む単語で意味的に指定されています。 

`Pick up the package from Seattle and deliver to New York City.`

この手順では、`origin` および `destination` ロールを事前構築済み geographyV2 エンティティに追加します。

1. **[ビルド]** セクションから、左パネル内の **[エンティティ]** を選択します。

1. **[+ Add prebuilt entity]\(+ 事前構築済みエンティティの追加\)** を選択します。 **[geographyV2]** を選択し、 **[完了]** を選択します。 事前構築済みエンティティがアプリに追加されます。
    
    Pattern.any が含まれているパターンでエンティティが正しく抽出されない場合は、[明示的なリスト](reference-pattern-syntax.md#explicit-lists)を使用してこの問題を修正します。 

1. エンティティの **[エンティティ]** ページ リストから、新しく追加された事前構築済み geographyV2 エンティティを選択します。 
1. 新しいロールを追加するには、 **[No roles added]\(ロールは追加されていません\)** の横にある **+** を選択します。
1. **[Type role...]\(ロールの入力...\)** テキストボックスにロール `Origin` の名前を入力し、Enter を押します。 `Destination` の 2 つ目のロール名を追加し、Enter キーを押します。 

    > [!div class="mx-imgBorder"]
    > ![Location エンティティに Origin ロールを追加している画面のスクリーンショット](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    ロールは構築済みのエンティティに追加されますが、そのエンティティを使用して発話に追加されることはありません。 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>サンプル発話のロールでテキストにラベルを付ける

1. [Intent detail]\(意図の詳細\) ページに進みます。このページにはロールを使用するサンプル発話があります。 
1. ロールでラベルを付けるには、サンプル発話でエンティティ ラベル (テキストの下の実線) を選択し、ドロップダウン リストから **[View in entity palette]\(エンティティ パレットで表示する\)** を選択します。 

    > [!div class="mx-imgBorder"]
    > ![[View in entity palette]\(エンティティ パレットで表示する\) を選択している画面のスクリーンショット](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    エンティティ パレットが右側で開きます。 

1. エンティティを選択し、パレットの下部に移動し、ロールを選択します。 

    > [!div class="mx-imgBorder"]
    > ![[View in entity palette]\(エンティティ パレットで表示する\) を選択している画面のスクリーンショット](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>pattern.any エンティティを追加する

[Pattern.any](luis-concept-entity-types.md) エンティティは[パターン](luis-how-to-model-intent-pattern.md)でのみ有効であり、意図のサンプル発話では無効です。 このエンティティ タイプは、可変長のエンティティの終わりや単語の選択を LUIS が見つけやすくします。 このエンティティがパターンで使用されることにより、発話テンプレート内でエンティティの終わりがどこにあるかを LUIS が認識します。

### <a name="steps-to-create-a-patternany-entity"></a>pattern.any エンティティの作成手順

1. **[ビルド]** セクションの左側にあるパネルで **[エンティティ]** を選択し、 **[+ 作成]** を選択します。

1. **[Create an entity type]\(エンティティの種類を作成する\)** ダイアログ ボックスの **[名前]** ボックスにエンティティ名を入力し、 **[種類]** として **[Pattern.Any]** を選択し、 **[作成]** を選択します。

    このエンティティを使用して[パターン発話を作成する](luis-how-to-model-intent-pattern.md)と、機械学習とテキスト照合アルゴリズムの組み合わせによりエンティティが抽出されます。 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>pattern.any エンティティを使用するパターン テンプレート発話を作成する

Pattern.any エンティティを使用するには、( **[Improve app performance]\(アプリのパフォーマンス改善\)** セクションの) **[パターン]** ページで、「`Where is **{HumanResourcesFormTitle}** on the server?`」のように正しい中かっこ構文を使用してパターンを追加します。

Pattern.any が含まれているパターンでエンティティが正しく抽出されない場合は、[明示的なリスト](reference-pattern-syntax.md#explicit-lists)を使用してこの問題を修正します。 

## <a name="do-not-change-entity-type"></a>エンティティの種類は変更しないでください

エンティティを作成するために追加または削除するものを LUIS は認識していないため、LUIS ではエンティティの種類を変更することは認められていません。 種類を変更するには、正しい種類の新しいエンティティを少し違う名前で作成することをお勧めします。 エンティティが作成されたら、それぞれの発話内で、古いラベルのエンティティ名を削除して新しいエンティティ名を追加します。 すべての発話でラベルが書き換えられたら、古いエンティティを削除します。 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [事前構築済みのモデルの使用](howto-add-prebuilt-models.md) 

各項目の詳細情報
* [トレーニング](luis-how-to-train.md)方法
* [テスト](luis-interactive-test.md)方法
* [発行](luis-how-to-publish-app.md)方法
* パターン:
    * [概念](luis-concept-patterns.md)
    * [構文](reference-pattern-syntax.md)
* [事前構築済みエンティティの GitHub リポジトリ](https://github.com/Microsoft/Recognizers-Text)
* [データ抽出の概念](luis-concept-data-extraction.md)


 
