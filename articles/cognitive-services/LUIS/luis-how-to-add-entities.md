---
title: LUIS アプリでエンティティを管理する | Microsoft Docs
titleSuffix: Azure
description: Language Understanding (LUIS) アプリにエンティティ (アプリケーションのドメイン内のキー データ) を追加します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: e97f9a5391799849983bd98db5400e0a842627b7
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224128"
---
# <a name="manage-entities"></a>エンティティを管理する
アプリの[意図](luis-concept-intent.md)を識別した後、[エンティティ](luis-concept-entity-types.md)によって[発話例にラベルを付ける](luis-concept-utterance.md)必要があります。 エンティティはコマンドまたは質問の重要な断片であり、クライアント アプリがタスクを実行するのに不可欠な場合があります。 

**[エンティティ]** ページの **[エンティティ] リスト**でアプリ内のエンティティを追加、編集、または削除できます。 LUIS で提供される主要なエンティティには、[事前構築済みのエンティティ](luis-reference-prebuilt-entities.md)と、独自のカスタム エンティティの 2 種類があります。

以下のセクションは LUIS アプリ内の **[Build]** セクションからのみ利用できます。 **[ビルド]** リンクは上部のナビゲーション バーにあります。 **[ビルド]** セクションに移動したら、左側のナビゲーション メニューから **[エンティティ]** を選択します。 エンティティがアプリケーションに追加された後、エンティティが機械学習される場合、発話内で[エンティティにラベルを付ける](luis-how-to-add-example-utterances.md)ことができます。 アプリがトレーニングおよび発行された後、予測から[抽出された](luis-concept-data-extraction.md)エンティティ データを受信できます。 

## <a name="add-prebuilt-entity"></a>事前構築済みのエンティティを追加する
事前構築済みのエンティティは、[Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) プロジェクトで定義されています。 アプリケーションに追加される事前構築済みのエンティティで一般的なのは、*number* と *datetimeV2* です。 

1. アプリで、**[ビルド]** セクションから、左パネル内の **[エンティティ]** をクリックします。
 
2. **[エンティティ]** ページで、**[Manage prebuilt entities]\(事前構築済みのエンティティの管理\)** を選択します。

    ![[エンティティ] ページでの事前構築済みエンティティの追加のスクリーンショット](./media/add-entities/manage-prebuilt-entities-button.png)

3. **[Add or remove prebuilt entities]\(事前構築済みのエンティティの追加または削除\)** ダイアログ ボックスで、事前構築済みのエンティティ **number** および **datetimeV2** を選択します。 **[完了]** を選択します。

    ![事前構築済みエンティティの追加ダイアログ ボックスのスクリーンショット](./media/add-entities/list-of-prebuilt-entities.png)

    エンドポイント JSON クエリ応答から事前構築済みのエンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#prebuilt-entity-data)に関するページを参照してください。

## <a name="add-simple-entities"></a>シンプル エンティティを追加する
シンプル エンティティとは、1 つの概念について説明する汎用的エンティティです。 

1. アプリで、**[ビルド]** セクションから、左パネル内の **[エンティティ]** をクリックし、**[Create new entity]\(新しいエンティティの作成\)** を選択します。

    ![[Create new entity]\(新しいエンティティの追加\) ボタンが強調された [エンティティ] ページのスクリーンショット](./media/add-entities/create-new-entity-button.png)

2. ポップアップ ダイアログ ボックスで、**[Entity name]\(エンティティ名\)** ボックスに「`Airline`」と入力し、**[エンティティの種類]** リストから **[簡易]** を選択し、**[完了]** を選択します。

    ![Airline シンプル エンティティを作成するダイアログ ボックスのスクリーンショット](./media/add-entities/create-simple-airline-entity.png)

    エンドポイント JSON クエリ応答からシンプル エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#simple-entity-data)に関するページを参照してください。 シンプル エンティティの使用方法の詳細については、シンプル エンティティの[クイック スタート](luis-quickstart-primary-and-secondary-data.md)をお試しください。

## <a name="add-regular-expression-entities"></a>正規表現エンティティを追加する
正規表現エンティティは、指定された正規表現に基づいて発話からデータを抽出するために使用されます。 

1. アプリで、左側のナビゲーションから **[エンティティ]** を選択し、**[Create new entity]\(新しいエンティティの追加\)** を選択します。

2. ポップアップ ダイアログ ボックスで、**[Entity name]\(エンティティ名\)** ボックスに「`AirFrance Flight`」と入力し、**[エンティティの種類]** リストから **[正規表現]** を選択し、正規表現「`AFR[0-9]{3,4}`」を入力し、**[完了]** を選択します。 

    この AirFrance Flight 正規表現は、3 文字のリテラル `AFR` と、それに続く 3 桁または 4 桁の数字を期待します。 各桁は 0 ～ 9 の任意の数字です。 この正規表現は、"AFR101"、"ARF1302"、"AFR5006" などの AirFrance フライト番号に一致します。 エンドポイント JSON クエリ応答からエンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md)に関するページを参照してください。

    ![正規表現エンティティを作成するダイアログ ボックスの画像](./media/add-entities/regex-entity-create-dialog.png)

    エンドポイント JSON クエリ応答から正規表現エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#regular-expression-entity-data)に関するページを参照してください。 正規表現エンティティを使用する方法の詳細については、正規表現エンティティの[クイック スタート](luis-quickstart-intents-regex-entity.md)をお試しください。

## <a name="add-hierarchical-entities"></a>階層構造エンティティを追加する
階層構造エンティティは、文脈から学習され、概念的に関連付けられる部類のエンティティです。 次の例では、エンティティに出発地と到着地が含まれます。 

発話 `Book 2 tickets from Seattle to Cairo` では、シアトルが出発地で、カイロが到着地です。 いずれの場所も文脈的に異なり、発話の中の単語の順序と選択から学習されます。

階層構造エンティティを追加するには、次の手順を完了します。 

1. アプリで、左側のナビゲーションから **[エンティティ]** を選択し、**[Create new entity]\(新しいエンティティの追加\)** を選択します。

2. ポップアップ ダイアログ ボックスで、**[Entity name]\(エンティティ名\)** ボックスに「`Location`」と入力し、**[エンティティの種類]** リストから **[Hierarchical]\(階層構造\)** を選択します。

    ![階層構造エンティティを追加する](./media/add-entities/hier-location-entity-creation.png)

3. **[子の追加]** を選択し、**[Child #1]\(子 #1\)** ボックスに「Origin」と入力します。 

4. **[子の追加]** を選択し、**[Child #2]\(子 #2\)** ボックスに「Destination」と入力します。 **[完了]** を選択します。
5. 
    >[!NOTE]
    >子を削除するには、その横にあるごみ箱アイコンを選択します。

    >[!CAUTION]
    >子エンティティ名は、1 つのアプリのエンティティ全体で一意となる必要があります。 2 つの異なる階層構造エンティティには、同じ名前の子エンティティを含めることができません。 

    エンドポイント JSON クエリ応答から階層構造エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#hierarchical-entity-data)に関するページを参照してください。 階層構造エンティティの使用方法の詳細については、階層構造エンティティの[クイック スタート](luis-quickstart-intent-and-hier-entity.md)をお試しください。

## <a name="add-composite-entities"></a>複合エンティティを追加する
複合エンティティを作成することによって、複数の既存エンティティ間の関係を定義することができます。 次の例では、エンティティにチケットの枚数、出発地、到着地が含まれます。 

発話 `Book 2 tickets from Seattle to Cairo` では、数字 2 が事前構築済みのエンティティに一致し、シアトルが出発地で、カイロが到着地です。 複合エンティティの作成後、各エンティティはより大きな親エンティティの一部になります。

1. アプリで、事前構築済みのエンティティ **number** を追加します。 手順については、「[事前構築済みのエンティティを追加する](#add-prebuilt-entity)」を参照してください。 

2. サブタイプ `origin`、`destination` を含む階層構造エンティティ `Location` を追加します。 手順については、「[階層構造エンティティを追加する](#add-hierarchical-entities)」を参照してください。 

3. 左側のナビゲーションから **[エンティティ]** を選択し、**[Create new entity]\(新しいエンティティの追加\)** を選択します。

4. ポップアップ ダイアログ ボックスで、**[Entity name]\(エンティティ名\)** ボックスに「`TicketsOrder`」と入力し、**[エンティティの種類]** リストから **[Composite]\(複合\)** を選択します。

5. **[子の追加]** を選択して新しい子を追加します。

6. **[Child #1]\(子 #1\)** で、エンティティ **number** をリストから選択します。

7. **[Child #2]\(子 #2\)** で、エンティティ **Location::Origin** をリストから選択します。 

8. **[Child #3]\(子 #3\)** で、エンティティ **Location::Destination** をリストから選択します。 

9. **[完了]** を選択します。

    ![複合エンティティを作成するダイアログ ボックスの画像](./media/add-entities/ticketsorder-composite-entity.png)

    >[!NOTE]
    >子を削除するには、その横にあるごみ箱ボタンを選択します。

    エンドポイント JSON クエリ応答から複合エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#composite-entity-data)に関するページを参照してください。 複合エンティティの使用方法の詳細については、複合エンティティ [チュートリアル](luis-tutorial-composite-entity.md)をお試しください。


## <a name="add-patternany-entities"></a>Pattern.any エンティティを追加する
[Pattern.any](luis-concept-entity-types.md) エンティティは[パターン](luis-how-to-model-intent-pattern.md)でのみ有効です。 このエンティティは、可変長のエンティティの終わりや単語の選択を LUIS が見つけやすくします。 このエンティティがパターンで使用されることにより、発話内でエンティティの終わりがどこにあるかを LUIS が認識します。

アプリに `FindBookInfo` という意図がある場合、書籍のタイトルが意図の予測に干渉する場合があります。 どの単語が書籍のタイトルに含まれているかを明確にするために、パターン内で Pattern.any を使用します。 LUIS の予測は発話によって始まります。 まず、発話がチェックされてエンティティと照合され、エンティティが見つかった場合、パターンがチェックおよび照合されます。 

「`Who wrote the book Ask and when was it published?`」という発話で、書籍のタイトル「Ask」は、タイトルがどこで終わり、発話の残りがどこから始まるか文脈上明らかでないため、扱いが困難です。 書籍のタイトルについては、1 つの単語、句読点を含む複雑なフレーズ、無意味な単語の並びなど、あらゆる単語の順序が考えられます。 パターンを使用することで、完全で正確なエンティティを抽出できるエンティティを作成することができます。 書籍のタイトルが見つかると、`FindBookInfo` がパターンの意図であるため、この意図が予測されます。

1. アプリで、**[ビルド]** セクションから、左パネル内の **[エンティティ]** をクリックし、**[Create new entity]\(新しいエンティティの作成\)** を選択します。

2. **[Add Entity]\(エンティティの追加\)** ダイアログ ボックスで、**[Entity name]\(エンティティ名\)** ボックスに「`BookTitle`」と入力し、**[エンティティの種類]** として **[Pattern.any]** を選択します。
 
    ![Pattern.any エンティティの作成のスクリーンショット](./media/add-entities/create-pattern-any-entity.png)

    Pattern.any エンティティを使用するには、(**[Improve app performance]\(アプリのパフォーマンス改善\)** セクション下の) **[Patterns]\(パターン\)** ページで、「For **{BookTitle}** who is the author?」のように正しい中かっこ構文を使用してパターンを追加します。

    エンドポイント JSON クエリ応答から Pattern.any エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#patternany-entity-data)に関するページを参照してください。 Pattern.any エンティティの使用方法について詳しく学ぶには、「[パターン](luis-tutorial-pattern.md)」のチュートリアルをお試しください。

Pattern.any が含まれているパターンでエンティティが正しく抽出されない場合は、[明示的なリスト](luis-concept-patterns.md#explicit-lists)を使用してこの問題を修正します。 

## <a name="add-role-to-pattern-based-entity"></a>パターン ベースのエンティティにロールを追加する
ロールは、文脈に基づいたエンティティの名前付きサブタイプです。 [階層構造](#add-hierarchical-entities)エンティティと比較できますが、ロールは[パターン](luis-how-to-model-intent-pattern.md)内でのみ使用されます。 

たとえば、航空券には*出発都市*と*到着都市*がありますが、どちらも都市です。 LUIS は両方が都市であると判断し、語順または単語選択の文脈に基づいて出発都市と到着都市を判断することができます。 

ロールの構文は **{Entity name:Role name}** で、エンティティ名、コロン、ロール名の順です。 たとえば、「Book a ticket from {Location:Origin} to {Location:Destination}」のようになります。

1. アプリで、**[ビルド]** セクションから、左パネル内の **[エンティティ]** を選択します。

2. **[Create new entity]\(新しいエンティティの作成\)** を選択します。 `Location` の名前を入力します。 **[簡易]** の型を選択して **[完了]** を選択します。

3. 左側のパネルから **[エンティティ]** を選択し、手順 2 で作成した新しいエンティティ **Location** を選択します。

4. **[ロール名]** テキスト ボックスに、ロール `Origin` の名前を入力して Enter キーを押します。 `Destination` の 2 番目のロール名を追加します。 例として、飛行機での旅行には出発都市と到着都市を設定できます。 2 つのロールは "Origin" と "Destination" です。

    ![Location エンティティに Origin ロールを追加するスクリーンショット](./media/add-entities/roles-enter-role-name-text.png)

    エンドポイント JSON クエリ応答からロールを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md)に関するページを参照してください。 Pattern.any エンティティの使用方法について詳しく学ぶには、パターンのチュートリアルをお試しください。

## <a name="add-list-entities"></a>リスト エンティティを追加する
リスト エンティティとは、システムにおける関連単語の集まりであり、固定かつ限定的です。 

飲み物のリスト エンティティの場合、水とソーダ水という正規値を 2 つ与えることができます。 正規名にはそれぞれシノニム (類義語) があります。 水の場合、H2O、炭酸入り、炭酸なしがシノニムとなります。 ソーダ水の場合、果汁、コーラ、ジンジャーエールがシノニムになります。 エンティティを作成するとき、すべての値を知っている必要はありません。 実際にユーザーがシノニムで発話するのを見てから追加できます。

|正規値|シノニム|
|--|--|
|水|H2O、炭酸入り、炭酸なし|
|ソーダ水|果汁、コーラ、ジンジャーエール|

1. アプリで、**[ビルド]** セクションから、左パネル内の **[エンティティ]** をクリックし、**[Create new entity]\(新しいエンティティの作成\)** を選択します。

2. **[Add Entity]\(エンティティの追加\)** ダイアログ ボックスで、**[Entity name]\(エンティティ名\)** ボックスに「`Drinks`」と入力し、**[エンティティの種類]** として **[リスト]** を選択します。 **[完了]** を選択します。
 
    ![飲み物のリスト エンティティを作成するダイアログ ボックスの画像](./media/add-entities/menu-list-dialog.png)
  
3.  リスト エンティティのページでは、正規化された名前を追加できます。 **[Values]\(値\)** テキスト ボックスにリストの項目 (飲み物リストの「`Water`」など) を入力し、キーボードの Enter を押します。 

    ![飲み物リスト エンティティのテキスト ボックスに「water」の正規化された値が表示されたスクリーンショット](./media/add-entities/entity-list-normalized-name.png)

4. 正規化された値「**water**」の右側に、類義語「`h20`」、「`flat`」、「`gas`」を入力し、各項目の入力後にキーボードの Enter を押します。

    ![water の類義語項目が強調された飲み物リスト エンティティのスクリーンショット](./media/add-entities/menu-list-synonyms.png)

5. リストの正規化された項目がさらに必要な場合、**[Recommend]\(推奨\)** を選択して、[セマンティック辞書](luis-glossary.md#semantic-dictionary)からのオプションを表示します。

    ![推奨項目が強調された飲み物リスト エンティティのスクリーンショット](./media/add-entities/entity-list-recommended-list.png)

6. 推奨リストの項目を選択して、その項目を正規化された値として追加するか、または **[すべて追加]** を選択してすべての項目を追加します。 

    ![beer の推奨項目と [すべて追加] ボタンが強調された飲み物リスト エンティティのスクリーンショット](./media/add-entities/list-entity-add-suggestions.png)

    エンドポイント JSON クエリ応答からリスト エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#list-entity-data)に関するページを参照してください。 リスト エンティティを使用する方法の詳細については、[クイック スタート](luis-quickstart-intent-and-list-entity.md)をお試しください。

## <a name="import-list-entity-values"></a>リスト エンティティの値をインポートする
既存のリスト エンティティに値をインポートできます。

 1. リスト エンティティのページで、**[Import Lists]\(リストのインポート\)** を選択します。

 2. **[Import New Entries]\(新しいエントリのインポート\)** ダイアログ ボックスで、**[Choose File]\(ファイルの選択\)** を選択し、リストを含む JSON ファイルを選択します。

    ![リスト エンティティの値をインポートするポップアップ ダイアログ ボックスのスクリーンショット](./media/add-entities/menu-list-import-json-dialog-with-file.png)

    >[!NOTE]
    >LUIS は拡張子が ".json" のファイルのみをインポートします。

 3. サポートされている JSON のリスト構文を調べるには、**[Learn about supported list syntax]\(サポートされているリスト構文の確認\)** を選択してダイアログを展開し、使用できる構文の例を表示します。 ダイアログを折りたたんで構文を非表示にするには、リンク タイトルをもう一度選択します。

 4. **[完了]** を選択します。

    **Colors** リスト エンティティの有効な JSON の例が、次の JSON 形式のコードで示されます。

    ```
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

## <a name="edit-entity-name"></a>エンティティ名を編集する
1. **[エンティティ]** リスト ページで、リスト内のエンティティを選択します。 この操作により、**[エンティティ]** ページに移動します。

2. **[エンティティ]** ページで、エンティティ名の横にある編集アイコンを選択して、エンティティ名を編集します。 エンティティの種類は編集できません。 

## <a name="delete-entity"></a>エンティティを削除する

**[エンティティ]** ページで、**[エンティティの削除]** ボタンを選択します。 次に、確認メッセージで **[OK]** を選択して、削除を確定します。
 
![[エンティティの削除] ボタンが強調された Location エンティティ ページのスクリーンショット](./media/add-entities/entity-delete.png)

>[!NOTE]
>* 階層構造エンティティを削除すると、すべての子エンティティが削除されます。
>* 複合エンティティを削除すると、複合のみが削除され複合関係が解消されますが、複合を構成するエンティティは削除されません。

## <a name="changing-entity-type"></a>エンティティの種類の変更
エンティティを作成するために追加または削除するものを LUIS は認識していないため、LUIS ではエンティティの種類を変更することは認められていません。 種類を変更するには、正しい種類の新しいエンティティを少し違う名前で作成することをお勧めします。 エンティティが作成されたら、それぞれの発話内で、古いラベルのエンティティ名を削除して新しいエンティティ名を追加します。 すべての発話でラベルが書き換えられたら、古いエンティティを削除します。 

## <a name="create-a-pattern-from-an-utterance"></a>発話からパターンを作成する
[予測の正確さを向上するためにパターンを追加する方法](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)に関するページをご覧ください。

## <a name="search-utterances"></a>発話を検索する
ツールバーの虫眼鏡アイコンを使用して、発話の検索とフィルタリングができます。 

## <a name="train-your-app-after-changing-model-with-entities"></a>エンティティを含むモデルの変更後にアプリをトレーニングする
エンティティを追加、編集、または削除したら、その変更がエンドポイントのクエリに反映されるようにアプリを[トレーニング](luis-how-to-train.md)して[発行](luis-how-to-publish-app.md)します。 

## <a name="next-steps"></a>次の手順
意図、発話、エンティティを追加したことで、基本的な LUIS アプリが完成しました。 アプリを[トレーニング](luis-how-to-train.md)、[テスト](luis-interactive-test.md)、および[発行](luis-how-to-publish-app.md)する方法を学びます。
 
