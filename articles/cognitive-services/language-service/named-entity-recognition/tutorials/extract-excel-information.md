---
title: Power Automate を使用して Excel に情報を抽出する
titleSuffix: Azure Cognitive Services
description: 名前付きエンティティの認識と Power Automate を使用して、コードを書き込みせずに Excel テキストを抽出する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: a53ff3e69842bcd2f42e8463f3f84638f512450f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091275"
---
# <a name="extract-information-in-excel-using-named-entity-recognitionner-and-power-automate"></a>固有表現認識 (NER) と Power Automate を使用して、情報を抽出します。 

このチュートリアルでは、コードを記述せずに Excel スプレッドシート内のテキストを抽出する Power Automate フローを作成します。 

このフローでは、あるアパートについて報告された問題のスプレッドシートを取得して、それらを 2 つのカテゴリ (配管工事とその他) に分類します。 また、送信元のテナントの名前と電話番号も抽出します。 フローでは最後に、この情報を Excel シートに追加します。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Power Automate を使用してフローを作成する
> * OneDrive for Business から Excel データをアップロードする
> * Excel からテキストを抽出し、固有表現認識(NER)に送信する 
> * API の情報を使用して、Excel シートを更新する

## <a name="prerequisites"></a>前提条件

- Microsoft Azure アカウント。 [無料アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)するか、[サインイン](https://portal.azure.com/)してください。
- 言語リソース。 お持ちでない場合は、[Azure portal で作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)し、Free レベルを使用してこのチュートリアルを完了することができます。
- サインアップ時に生成された[キーとエンドポイント](../../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
- テナントの問題を含むスプレッドシート。 サンプル データは GitHub で提供されています。
- Microsoft 365 (OneDrive for Business)。

## <a name="add-the-excel-file-to-onedrive-for-business"></a>OneDrive for Business に Excel ファイルを追加する

[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx)からサンプルの Excel ファイルをダウンロードします。 このファイルは、ご自分の OneDrive for Business アカウントで保存する必要があります。

:::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel ファイルの例" lightbox="../media/tutorials/excel/example-data.png":::

問題は、未加工のテキストで報告されます。 ユーザー名と電話番号を抽出するには、固有表現認識フィーチャーを使用します。 次に、フローで、説明の中の "plumbing" (配管工事) という語を検索して、問題を分類します。 

## <a name="create-a-new-power-automate-workflow"></a>新しい Power Automate ワークフローを作成する

[Power Automate サイト](https://preview.flow.microsoft.com/)にアクセスし、ログインします。 次に、 **[作成]** 、 **[予定フロー]** の順にクリックします。

:::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="ワークフロー作成画面" lightbox="../media/tutorials/excel/flow-creation.png":::

**[予定クラウド フローを作成]** ページで、次のフィールドを使用してフローを初期化します。

|フィールド |値  |
|---------|---------|
|**フロー名**     | **スケジュールされたレビュー** または別の名前。         |
|**開始中**     |  現在の日付と時刻を入力します。       |
|**繰り返し間隔**     | **1 時間**        |

## <a name="add-variables-to-the-flow"></a>フローに変数を追加する

Excel ファイルに追加する情報を表す変数を作成します。 **[新しいステップ]** をクリックし、**変数を初期化する** を検索します。 4 つの変数を作成するには、この操作を 4 回行います。

:::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="変数を初期化する手順" lightbox="../media/tutorials/excel/initialize-variables.png":::


作成した変数に次の情報を追加します。 これらは、Excel ファイルの列を表します。 折りたたまれている変数がある場合は、それらをクリックして展開できます。

| アクション |名前   | 型 | 値 |
|---------|---------|---|---|
| 変数を初期化する | var_person | String | Person |
| 変数を初期化する 2 | var_phone | String | 電話番号 |
| 変数を初期化する 3 | var_plumbing | String | plumbing |
| 変数を初期化する 4 | var_other | String | その他 | 

:::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="フロー変数に含まれる情報" lightbox="../media/tutorials/excel/flow-variables.png":::

## <a name="read-the-excel-file"></a>Excel ファイルを読み取る

**[新しいステップ]** をクリックし、「**Excel**」と入力します。次に、アクションのリストから **[表内に存在する行を一覧表示]** を選択します。

:::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="フローに Excel 行を追加します" lightbox="../media/tutorials/excel/list-excel-rows.png":::

このアクションのフィールドに入力して、Excel ファイルをフローに追加します。 このチュートリアルでは、ファイルが OneDrive for Business にアップロードされている必要があります。

:::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="フロー内の Excel 行に入力します" lightbox="../media/tutorials/excel/list-excel-rows-options.png":::

**[新しいステップ]** をクリックし、**Apply to each** アクションを追加します。

:::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="Apply to each アクションを追加します" lightbox="../media/tutorials/excel/add-apply-action.png":::

**[以前の手順から出力を選択]** をクリックします。 表示される動的コンテンツのボックスで、 **[値]** を選択します。

:::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Excel ファイルの出力を選択します" lightbox="../media/tutorials/excel/select-output.png":::

## <a name="send-a-request-for-entity-recognition"></a>エンティティ表彰の要求を送信する

まだ行っていない場合は、Azure portal で [言語リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) を作成する必要があります。

### <a name="create-a-language-service-connection"></a>言語サービス接続を作成する

**[Apply to each]** で、 **[アクションの追加]** をクリックします。 Azure portal の言語リソースの **[キーとエンドポイント]** ページに移動し、言語リソースのキーとエンドポイントを取得します。

フローで、次の情報を入力して新しい言語接続を作成します。

> [!NOTE]
> 言語接続を既に作成していて、接続の詳細を変更する場合は、右上にある省略記号をクリックし、 **[+ 新しい接続の追加]** をクリックします。

| フィールド           | 値                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| 接続名 | 言語リソースへの接続の名前。 たとえば、「 `TAforPowerAutomate` 」のように入力します。 |
| アカウント キー     | 言語リソースのキー。                                                                                   |
| サイトの URL        | 言語リソースのエンドポイント。                                                       |

:::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="言語リソースの資格情報をフローに追加する" lightbox="../media/tutorials/excel/add-credentials.png":::


## <a name="extract-the-excel-content"></a>Excel コンテンツを抽出する 

接続が作成されたら、**Text Analytics** を検索し、 **[固有表現認識]** を選択します。 これにより、問題の説明列から情報が抽出されます。

:::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Excel シートからエンティティを抽出します" lightbox="../media/tutorials/excel/extract-info.png":::

**[テキスト]** フィールドをクリックし、表示される動的コンテンツ ウィンドウから **[Description]** を選択します。 言語に `en` と入力し、ドキュメント ID として一意の名前を入力します ( **[詳細オプションの表示]** をクリックする必要がある場合があります)。

:::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Excel シートから説明列のテキストを取得します" lightbox="../media/tutorials/excel/description-from-dynamic-content.png":::


**[Apply to each]** 内で、 **[アクションの追加]** をクリックし、別の **Apply to each** アクションを作成します。 テキスト ボックス内をクリックし、表示される動的コンテンツ ウィンドウで **[ドキュメント]** を選択します。

:::image type="content" source="../media/tutorials/excel/apply-to-each-documents.png" alt-text="別の Apply to each アクションを作成します。" lightbox="../media/tutorials/excel/apply-to-each-documents.png":::


## <a name="extract-the-person-name"></a>個人名を抽出する

次に、NER 出力で 個人のエンティティ型を確認します。 **[Apply to each 2]** 内で、 **[アクションの追加]** をクリックし、別の **Apply to each** アクションを作成します。 テキスト ボックス内をクリックし、表示される動的コンテンツ ウィンドウで **[エンティティ]** を選択します。

:::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="NER 出力で個人のエンティティを検索する" lightbox="../media/tutorials/excel/add-apply-action-2.png":::


新しく作成した **[Apply to each 3]** アクション内で、 **[アクションの追加]** をクリックして、**条件** コントロールを追加します。

:::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Apply to each 3 アクションに、条件コントロールを追加します" lightbox="../media/tutorials/excel/create-condition.png":::


[条件] ウィンドウで、最初のテキスト ボックスをクリックします。 動的コンテンツ ウィンドウで、 **[カテゴリ]** を見つけて選択します。

:::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="コントロール条件にカテゴリを追加する" lightbox="../media/tutorials/excel/choose-entities-value.png":::


2 番目のボックスが **[次の値と等しい]** に設定されていることを確認します。 次に、3 番目のボックスを選択し、動的コンテンツ ウィンドウで `var_person` を検索します。 

:::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="人物の変数を追加します" lightbox="../media/tutorials/excel/choose-variable-value.png":::


**[はいの場合]** 条件で、「Excel」 を入力し、 **[行の更新]** を選択します。

:::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="[はいの場合] 条件を更新します" lightbox="../media/tutorials/excel/yes-column-action.png":::

Excel の情報を入力し、 **[キー列]** 、 **[キー値]** 、 **[人名]** フィールドを更新します。 これにより、API によって検出された名前が Excel シートに追加されます。 

:::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Excel 情報を追加します" lightbox="../media/tutorials/excel/yes-column-action-options.png":::

## <a name="get-the-phone-number"></a>電話番号を取得する

名前をクリックして、**Apply to each 3** アクションを最小化します。 次に、前の手順と同様、別の **Apply to each** アクションを **[Apply to each 2]** に追加します。 これには **Apply to each 4** という名前が付けられます。 テキスト ボックスを選択し、このアクションの出力として **エンティティ** を追加します。 

:::image type="content" source="../media/tutorials/excel/add-apply-action-phone.png" alt-text="NER 出力から別のアクションにエンティティを追加して、各アクションに適用します。" lightbox="../media/tutorials/excel/add-apply-action-phone.png":::

**[Apply to each 4]** 内で、**条件** コントロールを追加します。 **条件 2** という名前になります。 最初のテキスト ボックスで、動的コンテンツ ウィンドウから **カテゴリ** を探して追加します。 中央のボックスが **[次の値と等しい]** に設定されていることを確認します。 次に、右側のテキスト ボックスに「`var_phone`」と入力します。 

:::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="2 つ目の条件コントロールを追加します" lightbox="../media/tutorials/excel/condition-2-options.png":::

**[はいの場合]** 条件で、**行の更新** アクションを追加します。 次に、Excel シートの電話番号の列に、上記で行ったように情報を入力します。 これにより、API によって検出された電話番号が Excel シートに追加されます。 

:::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="2 番目の [はいの場合] 条件に Excel 情報を追加します" lightbox="../media/tutorials/excel/condition-2-yes-column.png":::

## <a name="get-the-plumbing-issues"></a>配管工事の問題を取得する

名前をクリックして、**Apply to each 4** を最小化します。 次に、親アクションで別の **Apply to each** を作成します。 テキスト ボックスを選択し、動的コンテンツ ウィンドウからこのアクションの出力として **エンティティ** を追加します。 

:::image type="content" source="../media/tutorials/excel/add-apply-action-plumbing.png" alt-text="別の Apply to each アクションを作成します" lightbox="../media/tutorials/excel/add-apply-action-plumbing.png":::

次に、Excel テーブルの行の問題の説明に "plumbing" (配管工事) という語が含まれているかどうかがフローによって確認されます。 含まれている場合は、"plumbing" が [IssueType] 列に追加されます。 そうでない場合は、「other」(その他) と入力します。

**Apply to each 4** 内に、**条件** コントロールを追加します。 **条件 3** という名前になります。 動的コンテンツ ウィンドウを使用して、最初のテキスト ボックスで、Excel ファイルから **[Description]** を検索して追加します。 中央のボックスが **[を含む]** になっていることを確認します。 次に、右側のテキストボックスで、`var_plumbing` を見つけて選択します。 

:::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="新しい条件コントロールを作成します" lightbox="../media/tutorials/excel/condition-3-options.png":::

**[はいの場合]** 条件で、 **[アクションを追加]** をクリックし、 **[行の更新]** を選択します。 次に、前と同じように情報を入力します。 [IssueType] 列で、[`var_plumbing`] を選択します。 これにより、行に "plumbing" ラベルが適用されます。

**[いいえの場合]** 条件で、 **[アクションを追加]** をクリックし、 **[行の更新]** を選択します。 次に、前と同じように情報を入力します。 [IssueType] 列で、[`var_other`] を選択します。 これにより、行に "other" ラベルが適用されます。

:::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="両方の条件に情報を追加します" lightbox="../media/tutorials/excel/plumbing-issue-condition.png":::

## <a name="test-the-workflow"></a>ワークフローをテストする

画面の右上隅にある **[保存]** をクリックし、次に **[テスト]** をクリックします。 **テスト フロー** で、 **[手動]** を選択します。 次に、 **[テスト]** をクリックし、 **[フローの実行]** をクリックします。

Excel ファイルは、ご自分の OneDrive アカウントで更新されます。 次のようになります。

:::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="ワークフローをテストして出力を表示します" lightbox="../media/tutorials/excel/updated-excel-sheet.png":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [固有表現認識を呼び出すために、REST APIまたはクライアントライブラリを使用します。](../quickstart.md)
