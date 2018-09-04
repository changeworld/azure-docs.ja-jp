---
title: Text Analytics と Power BI - Azure Cognitive Services | Microsoft Docs
description: Text Analytics を使用して Power BI に格納されているテキストからキー フレーズを抽出する方法について説明します。
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 3/07/2018
ms.author: luisca
ms.openlocfilehash: 2cdb93d44218627efdcb0360d8cf4a4eeeca177a
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "42889287"
---
# <a name="text-analytics-with-power-bi"></a>Text Analytics と Power BI

Microsoft Power BI を使用すると、組織のデータをきれいなレポートにまとめ、組織全体に配布して、より迅速で深い分析情報を得ることができます。 Text Analytics サービスは、Microsoft Azure の Cognitive Services の一部であり、Key Phrases API を使用してテキストから重要度の高いフレーズを抽出できます。 これらのツールを組み合わせることで、ユーザーが話している内容とそれに対する感情をすばやく確認できます。

このチュートリアルでは、Power BI Desktop と Key Phrases API を統合してカスタム Power Query 機能を使用してユーザーのフィードバックから重要度の高いフレーズを抽出する方法を確認できます。 また、これらのフレーズからワード クラウドを作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、次のものが必要です。

> [!div class="checklist"]
> * Microsoft Power BI Desktop。 [無料でダウンロードできます](https://powerbi.microsoft.com/get-started/)。
> * Microsoft Azure アカウント。 [無料試用版を開始する](https://azure.microsoft.com/free/)か、[サインイン](https://portal.azure.com/)します。
> * Text Analytics のアクセス キー。 [サインアップ](../../cognitive-services-apis-create-account.md)し、[キーを取得します](../how-tos/text-analytics-how-to-access-key.md)。
> * ユーザーのコメント。 [データ例を取得](https://aka.ms/cogsvc/ta)するか、独自のデータを使用します。

## <a name="loading-customer-data"></a>ユーザー データの読み込み

まず Power BI Desktop を開き、コンマ区切りファイル (CSV) の **FabrikamComments.csv** を読み込みます。 このファイルは、架空の小規模な会社のサポート フォーラムの 1 日分の仮想的なアクティビティを表しています。

> [!NOTE]
> Power BI では、Facebook や SQL Database など、さまざまなソースのデータを使用できます。 [Facebook と Power BI の統合](https://powerbi.microsoft.com/integrations/facebook/)と [SQL Server と Power BI の統合](https://powerbi.microsoft.com/integrations/sql-server/)に関するページを参照してください。

Power BI Desktop ウィンドウで、[ホーム] リボンの [外部データ] グループを探します。 このグループの **[データの取得]** ドロップダウン メニューで **[テキスト/CSV]** を選択します。

![[[データの取得] ボタン]](../media/tutorials/power-bi/get-data-button.png)

[開く] ダイアログが表示されます。 ダウンロード フォルダーまたはサンプル データ ファイルを含む任意のフォルダーに移動します。 `FabrikamComments.csv` をクリックし、**[開く]** ボタンをクリックします。 CSV のインポート ダイアログが表示されます。

![[[CSV のインポート] ダイアログ]](../media/tutorials/power-bi/csv-import.png)

[CSV インポート] ダイアログでは、Power BI Desktop で文字セット、区切り文字、ヘッダー行、および列の種類が正しく検出されたことを確認できます。 この情報はすべて正しいので、**[読み込み]** をクリックします。

読み込まれたデータを表示するには、Power BI ワークスペースの左端にある [データ ビュー] ボタンを使用してデータ ビューに切り替えます。 Microsoft Excel などで、データを含むが入ったテーブルが開きます。

![[インポートされたデータの最初のビュー]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="preparing-the-data"></a>データを準備する

必要に応じて Power BI Desktop でデータを変換してから、Key Phrases サービスで処理する準備が整えます。

たとえば、データには `subject` フィールドと `comment` フィールドの両方が含まれています。 キー フレーズを抽出するときに、`comment` だけでなく、これら両方のフィールドのテキストを考慮する必要があります。 Power BI Desktop の列のマージ機能を使用すると、この作業が簡単になります。

[ホーム] リボンで [外部データ] グループの **[クエリの編集]** をクリックして、メイン Power BI Desktop ウィンドウからクエリ エディターを開きます。 

![[[ホーム] リボンの [外部データ] グループ]](../media/tutorials/power-bi/edit-queries.png)

ウィンドウの左側にある [クエリ] リストで [FabrikamComments] がまだ選択されていない場合は選択します。

次に、テーブル内の `subject` 列と `comment` 列の両方を選択します。 これらの列を表示するには、必要に応じて横にスクロールします。 まず `subject` 列ヘッダーをクリックし、Ctrl キーを押しながら `comment` 列ヘッダーをクリックします。

![[マージするフィールドを選択する]](../media/tutorials/power-bi/select-columns.png)

[変換] リボンの [Text Columns]\(テキスト列\) グループで、**[列のマージ]** をクリックします。 [列のマージ] ダイアログが表示されます。

![[[列のマージ] ダイアログを使用してフィールドをマージする]](../media/tutorials/power-bi/merge-columns.png)

[列のマージ] ダイアログで区切りとしてタブを選択してから **[OK]** をクリックします。 これで完了です。

[空の削除] フィルターを使用して空白のメッセージを除外したり、[Clean transformation]\(変換のクリア\) を使用して印刷できない文字を削除したりすることもできます。 サンプル ファイルの `spamscore` 列のような列がデータに含まれている場合は、[数値フィルター] を使用して "スパム" コメントをスキップできます。

## <a name="understanding-the-api"></a>API の概要

Key Phrases API は、HTTP 要求ごとに最大 1,000 個のテキスト ドキュメントを処理できます。 Power BI では一度に 1 レコードずつ処理する方が好まれるため、この API の呼び出しには 1 つのドキュメントしか含まれていません。 この [API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) では、処理されるドキュメントごとに次のフィールドが必要です。

| | |
| - | - |
| `id`  | 要求内のこのドキュメントの一意の識別子。 応答にもこのフィールドが含まれます。 そのため、複数のドキュメントを処理する場合、抽出されたキー フレーズを元のドキュメントと簡単に関連付けることができます。 要求ごとに 1 つのドキュメントを処理しているので、応答に関連付けられているドキュメントは既にわかっており、要求ごとに `id` を同じにすることができます。|
| `text`  | 処理されるテキスト。 作成した `Merged` 列から取得します。この列には、結合されたサブジェクト行とコメント テキストが含まれています。 Key Phrases API では、このデータの文字数の上限は約 5,000 文字です。|
| `language` | ドキュメントの記述に使用されている言語を表すコード。 この例では、すべてのメッセージは英語なので、クエリには言語 `en` をハードコードすることができます。|

Key Phrases API に送信するために、これらのフィールドを JSON (JavaScript Object Notation) ドキュメントに結合する必要があります。 この要求からの応答も JSON ドキュメントであり、それを解析してキーフレーズを取り出す必要があります。

## <a name="creating-a-custom-function"></a>カスタム関数を作成する

Power BI と Text Analytics を統合するカスタム関数を作成する準備が整いました。 Power BI Desktop は、テーブルの各行の関数を呼び出し、結果と共に新しい列を作成します。

この関数は、処理されるテキストをパラメーターとして受け取ります。 また、必要な JavaScript Object Notation (JSON) との間でデータを変換し、Key Phrases API エンドポイントへの HTTP 要求を行います。 応答を解析した後、関数からは、抽出されたキー フレーズのコンマ区切りの一覧を含む文字列が返されます。

> [!NOTE]
> Power BI Desktop のカスタム関数は、[Power Query M 式言語](https://msdn.microsoft.com/library/mt211003.aspx)、または省略形の "M" と書かれます。 M は、[F#](https://docs.microsoft.com/dotnet/fsharp/) に基づく関数型プログラミング言語です。 ただし、このチュートリアルを完了するためにプログラマーである必要はありません。必要なコードは以下に含まれています。

[クエリ エディター] ウィンドウは開いたままにしておきます。 [ホーム] リボンの **[新しいソース]** ([新しいクエリ] グループ内) をクリックし、ドロップダウン メニューから **[空のクエリ]** を選択します。 

最初に Query1 という名前の新しいクエリが [クエリ] リストに表示されます。 このエントリをダブルクリックし、`KeyPhrases` と名前を付けます。

[ホーム] リボンの [クエリ] グループで **[詳細エディター]** をクリックして、[詳細エディター] ウィンドウを開きます。 そのウィンドウに既にあるコードを削除し、次のコードに貼り付けます。 

> [!NOTE]
> 以下の例では、エンドポイントが https://westus.api.cognitive.microsoft.com であると仮定しています。  Text Analytics は、13 か所のリージョンでのサブスクリプション作成をサポートしています。 別のリージョンのサービスにサインアップした場合は、選択したリージョンのエンドポイントを必ず使用してください。 Text Analytics サブスクリプションを選択すると、Azure portal の [概要] ページに表示されます。

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

また、Microsoft Azure ダッシュボードから取得した Text Analytics API キーを、`apikey` で始まる行に貼り付けます (キーの前後には必ず引用符を入れてください)。次に、**[完了]** をクリックします。

## <a name="using-the-function"></a>関数を使用する

カスタム関数を使用して、ユーザーの各コメントに含まれるキー フレーズを取得し、テーブルの新しい列に格納できるようになりました。 

開いているクエリ エディターで、FabrikamComments クエリに戻り、[列の追加] リボンに変更し、[全般] グループの **[カスタム関数の呼び出し]** ボタンをクリックします。

![[[カスタム関数の呼び出し] ボタン]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

[カスタム関数の呼び出し] ダイアログで、新しい列の名前として「`keyphrases`」と入力します。 [関数クエリ] としてカスタム関数の `KeyPhrases` を選択します。 

`text` パラメーターとして関数に渡すフィールドを確認する新しいフィールドがダイアログに表示されます。 ドロップダウン メニューから `Merged` (サブジェクト フィールドとメッセージ フィールドをマージして前に作成した列) を選択します。

![[カスタム関数を呼び出す]](../media/tutorials/power-bi/invoke-custom-function.png)

最後に **[OK]** をクリックします。

すべての準備が完了すると、Power BI からテーブルの各行に対して関数が 1 回呼び出され、Key Phrases クエリが実行され、新しい列がテーブルに追加されます。 ただし、その処理が実行される前に、必要に応じて認証とプライバシーの設定を指定します。

## <a name="authentication-and-privacy"></a>認証とプライバシー

[カスタム関数の呼び出し] ダイアログを閉じると、Key Phrases エンドポイントに接続する方法を指定するように求めるバナーが表示されることがあります。

![[資格情報のバナー]](../media/tutorials/power-bi/credentials-banner.png)

**[資格情報の編集]** ダイアログで [匿名] が選択されていることを確認して **[接続]** をクリックします。 

> [!NOTE]
> 匿名の理由は、 Text Analytics サービスは API キーを使用してユーザーを認証するので、Power BI は HTTP 要求自体の資格情報を指定する必要はないためです。

![[認証を匿名に設定する]](../media/tutorials/power-bi/access-web-content.png)

匿名アクセスを選択した後も [資格情報の編集] バナーが表示される場合は、API キーを貼り付けるのを忘れている可能性があります。 詳細エディターの `KeyPhrases` カスタム関数で存在することを確認してください。

次に、データ ソースのプライバシーに関する情報の入力を求めるバナーが表示されることがあります。 

![[プライバシー バナー]](../media/tutorials/power-bi/privacy-banner.png)

**[続行]** をクリックし、ダイアログ内の各データ ソースに対して [公開] を選択します。 **[保存]** をクリックします。

![[データ ソースのプライバシーを設定する]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="creating-the-word-cloud"></a>ワード クラウドを作成する

表示されるバナーを処理したら、[ホーム] リボンの **[Close & Apply]\(閉じて適用\)** をクリックしてクエリ エディターを閉じます。

Power BI Desktop で必要な HTTP 要求の実行が完了するまで少し待ちます。 テーブルの各行の新しい `keyphrases` 列には、Key Phrases API によって検出されたテキストのキー フレーズが含まれています。 

この列を使用してワード クラウドを生成しましょう。 まず、ワークスペースの左側にある Power BI Desktop のメイン ウィンドウの [レポート] ボタンをクリックします。

> [!NOTE]
> すべてのコメントの全文ではなく、抽出されたキー フレーズを使用してワード クラウドを生成する理由は、 キー フレーズで、ユーザーのコメントから*最も一般的な*単語だけでなく*重要な*単語が提供されるためです。 また、比較的少数のコメントで 1 つの単語が頻繁に使用されることで、結果として得られるクラウドの単語のサイズ指定が偏ることはありません。

Word Cloud カスタム ビジュアルをまだインストールしていない場合は、インストールします。 ワークスペースの右側にある [視覚化] パネルで、3 つのドット (**[...]**) をクリックし、**[ストアからインポート]** を選択します。 次に、「クラウド」を検索して、Word Cloud ビジュアルの横にある **[追加]** ボタンをクリックします。 Power BI に Word Cloud ビジュアルがインストールされると、正常にインストールされたことが通知されます。

![[カスタム ビジュアルの追加]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

それではワード クラウドを作成しましょう。

![[[視覚化] パネルの Word Cloud アイコン]](../media/tutorials/power-bi/visualizations-panel.png)

まず [視覚化] パネルの Word Cloud アイコンをクリックします。 ワークスペースに新しいレポートが表示されます。 [フィールド] パネルの [`keyphrases`] フィールドを [視覚化] パネルの [カテゴリ] フィールドにドラッグします。 レポート内にワード クラウドが表示されます。

次に [視覚化] パネルの [書式設定] ページに切り替えます。 クラウドから短い一般的な単語 ("of" など) を除外するには、[ストップ ワード] カテゴリで **[既定のストップ ワード]** を有効にします。 

![[既定のストップ ワードを有効にする]](../media/tutorials/power-bi/default-stop-words.png)

このパネルをもう少し下にスクロールし、**[テキストの回転]** と **[タイトル]** をオフにします。

![[アクティブなフォーカス モード]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

レポートのフォーカス モード ツールをクリックすると、ワード クラウドの詳細が表示されます。 ツールでワード クラウドが展開され、図のようにワークスペース全体に表示されます。

![[ワード クラウド]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>その他の Text Analytics サービス

Microsoft Azure で提供されている Cognitive Services の 1 つである Text Analytics サービスには、感情分析機能と言語検出機能もあります。 言語検出機能は、ユーザーのフィードバックがすべて英語ではない場合に特に役立ちます。

これらの他の API は両方とも Key Phrases API とよく似ています。 そのため、ほぼ同じカスタム関数を使用すると、Power BI Desktop と統合することができます。 空のクエリを作成して、先ほどと同じように以下の適切なコードを詳細エディターに貼り付けます  (アクセス キーを忘れないでください)。前回と同様に、関数を使用してテーブルに新しい列を追加します。

以下の感情分析機能では、テキストに示されている感情がどの程度肯定的かを示すスコアが返されます。

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

言語検出機能には 2 つのバージョンがあります。 1 つ目では ISO 言語コードが返されます (例: 英語の場合は `en`)。一方、2 つ目では "わかりやすい" 名前 (たとえば `English`) が返されます。 ご覧のように、2 つのバージョンは本文の最後の行のみが異なります。

```fsharp
// Returns the two-letter language code (e.g. en for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (e.g. English) of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

最後に、前述の Key Phrases 関数を、コンマで区切られたフレーズの単一の文字列ではなく、フレーズをリスト オブジェクトとして返すように変更した関数を次に示します。 

> [!NOTE]
> 単一の文字列を返すことで、ワード クラウドの例が簡単になりました。 一方、リストは、Power BI で返されたフレーズを処理する場合により柔軟な形式です。 クエリ エディターの [変換] リボンの [構造化列] グループを使用して、Power BI Desktop のリスト オブジェクトを操作できます。

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>次の手順

Text Analytics サービス、Power Query M 式言語、または Power BI の詳細について学習します。

> [!div class="nextstepaction"]
> [Text Analytics API リファレンス](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Power Query M リファレンス](//msdn.microsoft.com/library/mt211003.aspx)

> [!div class="nextstepaction"]
> [Power BI のドキュメント](//powerbi.microsoft.com/documentation/powerbi-landing-page/)
