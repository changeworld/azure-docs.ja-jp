---
title: Form Recognizer の新機能
titleSuffix: Azure Cognitive Services
description: Form Recognizer API の最新の変更点を把握します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531084"
---
# <a name="whats-new-in-form-recognizer"></a>Form Recognizer の新機能

Form Recognizer サービスは、継続的に更新されます。 この記事で、機能の強化、修正、ドキュメントの更新についての最新情報を入手してください。

> [!NOTE]
> Form Recognizer のクイックスタートとガイドでは、特に指定がない限り、常に最新バージョンの API が使用されます。

## <a name="march-2020"></a>2020 年 3 月 

### <a name="new-features"></a>新機能

* **ラベル付けの値の種類** Form Recognizer サンプル ラベル付けツールを使用して、ラベル付けする値の種類を指定できるようになりました。 現在、次の値の型とバリエーションがサポートされています。
  * `string`
    * 既定値、`no-whitespaces`、`alphanumeric`
  * `number`
    * 既定値、`currency`
  * `date` 
    * 既定値、`dmy`、`mdy`、`ymd`
  * `time`
  * `integer`

  この機能の使用方法については、[サンプル ラベル付けツール](./quickstarts/label-tool.md#specify-tag-value-types) ガイドを参照してください。


* **テーブルの視覚化** サンプル ラベル付けツールで、ドキュメントで認識されたテーブルが表示されるようになりました。 これにより、ラベル付けおよび分析を行う前に、ドキュメントから認識されて抽出されたテーブルを表示できます。 この機能は、レイヤー オプションを使用してオンとオフを切り替えることができます。

  テーブルが認識されて抽出される方法の例を次に示します。

  > [!div class="mx-imgBorder"]
  > ![サンプル ラベル付けツールを使用したテーブルの視覚化](./media/whats-new/formre-table-viz.png)

    抽出されたテーブルは、`"pageResults"` の JSON 出力で使用できます。

  > [!IMPORTANT]
  > テーブルのラベル付けはサポートされていません。 テーブルの認識と抽出が自動的に行われない場合は、キーと値のペアとしてのみラベルを付けることができます。 キーと値のペアとしてテーブルのラベル付けを行うときは、各セルに固有の値としてラベルを付けます。

### <a name="extraction-enhancements"></a>抽出の機能強化

このリリースでは、抽出の機能強化と精度の向上が行われています。具体的には、テキストの同じ行に複数のキーと値のペアが含まれる場合の、ラベル付けと抽出の機能です。 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>サンプル ラベル付けツールがオープンソースになりました

Form Recognizer のサンプル ラベル付けツールが、オープンソース プロジェクトとして使用できるようになりました。 ソリューションにそれを統合し、ニーズに合わせて顧客固有の変更を行うことができます。

Form Recognizer のサンプル ラベル付けツールの詳細については、[GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md) で入手できるドキュメントを参照してください。

### <a name="tls-12-enforcement"></a>TLS 1.2 の適用

TLS 1.2 は現在、このサービスへのすべての HTTP 要求に適用されるようになりました。 詳細については、[Azure Cognitive Services のセキュリティ](../cognitive-services-security.md)に関するページを参照してください。

## <a name="january-2020"></a>2020 年 1 月

このリリースでは、Form Recognizer 2.0 (プレビュー) が導入されています。 以下のセクションでは、新機能、機能強化、および変更の詳細について説明します。 

### <a name="new-features"></a>新機能

* **カスタム モデル**
  * **ラベルを使用したトレーニング** 手動でラベル付けされたデータを使用して、カスタム モデルをトレーニングできるようになりました。 これにより、性能の高いモデルが得られ、複雑なフォームやキーのない値を含んだフォームでも機能するモデルを作成することが可能です。
  * **非同期 API** 非同期 API 呼び出しを使用して、大規模なデータ セットおよびファイルをトレーニングして分析できます。
  * **TIFF ファイルのサポート** TIFF ドキュメントからのデータをトレーニングして、抽出できるようになりました。
  * **抽出精度の向上**

* **構築済みのレシート モデル**
  * **チップの金額** チップの金額とその他の手書きの値を抽出できるようになりました。
  * **品目の抽出** レシートから品目の値を抽出できます。
  * **信頼値** 抽出された各値のモデルの信頼度を表示できます。
  * **抽出精度の向上**

* **レイアウトの抽出** レイアウト API を使用して、フォームからテキスト データとテーブル データを抽出できるようになりました。

### <a name="custom-model-api-changes"></a>カスタム モデル API の変更点

カスタム モデルのトレーニングと使用のための API 名がすべて変更され、一部の同期メソッドは非同期になりました。 主な変更点は次のとおりです。

* モデルのトレーニング プロセスが非同期になりました。 **/custom/models** API 呼び出し経由で、トレーニングを開始します。 この呼び出しでは、操作 ID が返されます。この ID を **custom/models/{modelID}** に渡すと、トレーニング結果を返すことができます。
* キー/値の抽出は、 **/custom/models/{modelID}/analyze** API 呼び出しによって開始されるようになりました。 この呼び出しでは操作 ID が返され、それを **custom/models/{modelID}/analyzeResults/{resultID}** に渡すと、抽出結果を返すことができます。
* トレーニング操作の操作 ID は、**Operation-Location** ヘッダーではなく、HTTP 応答の **Location** ヘッダー内に配置されるようになりました。

### <a name="receipt-api-changes"></a>レシート API の変更点

販売レシートを読み取る API の名前が変更されました。

* レシート データの抽出は、 **/prebuilt/receipt/analyze** API 呼び出しによって開始されるようになりました。 この呼び出しでは操作 ID が返され、それを **/prebuilt/receipt/analyzeResults/{resultID}** に渡すと、抽出結果を返すことができます。

### <a name="output-format-changes"></a>出力形式の変更点

すべての API 呼び出しへの JSON 応答には、新しい形式が用意されています。 一部のキーと値は、追加、削除、または名前の変更が行われています。 現在の JSON 形式の例については、クイックスタートを参照してください。

## <a name="next-steps"></a>次のステップ

[クイック スタート](quickstarts/curl-train-extract.md)をすべて終え、[Form Recognizer API シリーズ](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)の使用を開始します。