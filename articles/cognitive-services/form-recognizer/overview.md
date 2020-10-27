---
title: Form Recognizer とは
titleSuffix: Azure Cognitive Services
description: Azure Form Recognizer サービスを使用すると、フォーム ドキュメントからキーと値のペアとテーブル データを識別して抽出することや、領収書や名刺から主要な情報を抽出することができます。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 09/21/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 自動データ処理、ドキュメント処理、自動データ入力、フォーム処理
ms.openlocfilehash: 5243c170e1f6b5f647057b8cfafbcac9b2fb4db3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91318961"
---
# <a name="what-is-form-recognizer"></a>Form Recognizer とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer は、機械学習テクノロジを利用して自動データ処理ソフトウェアを構築ことを可能にするコグニティブ サービスです。 テキスト、キーと値のペア、テーブル データを特定し、form ドキュメントから抽出します。&mdash;元のファイルにおける関係を含む構造化データがこのサービスにより出力されます。 手作業による操作やデータ サイエンスに関する専門知識があまりなくても、特定のコンテンツに合わせた正確な結果がすばやく得られます。 Form Recognizer を使用して、アプリケーションへのデータ入力を自動化します。

Form Recognizer は、カスタム ドキュメント処理モデル、事前構築済みのレシートと名刺のモデル、およびレイアウト API で構成されています。 REST API またはクライアント ライブラリ SDK を使用して Form Recognizer モデルを呼び出すことにより、複雑さを軽減し、自分のワークフローやアプリケーションに統合することができます。

Form Recognizer は、次のサービスで構成されています。
* **[カスタム モデル](#custom-models)** - フォームからキーと値のペアおよびテーブル データを抽出します。 これらのモデルは自分が用意した独自のデータでトレーニングされるため、実際のフォームに合わせて調整されます。
* **[事前構築済みモデル](#prebuilt-models)** - 事前構築済みモデルを使用して、一意のフォームの種類からデータを抽出します。 現在利用可能なのは、英語のレシートと名刺用の事前構築済みモデルです。
* **[Layout API](#layout-api)** - テキストおよびテーブルの構造を、対応する境界ボックスの座標と共にドキュメントから抽出します。

## <a name="custom-models"></a>カスタム モデル

Form Recognizer のカスタム モデルでは、独自のデータでトレーニングを行います。また、5 つのサンプル入力フォームだけで開始できます。 トレーニング済みのドキュメント処理モデルは、元の form ドキュメント内の関係を含む構造化データを出力できます。 モデルをトレーニングした後、モデルをテストおよび再トレーニングでき、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

カスタム モデルをトレーニングする際は、ラベル付けされたデータを使用したトレーニングとラベル付けされたデータを使用しないトレーニングのどちらかを選択できます。

### <a name="train-without-labels"></a>ラベルを使用しないトレーニング

Form Recognizer は、既定では教師なし学習を使用して、フォーム内のレイアウトを解釈し、フィールドとエントリ間の関係を解釈します。 入力フォームを送信すると、アルゴリズムによって種類別にフォームが分類されて、存在するキーとテーブルが検出されます。また、値がキーに、エントリがテーブルに関連付けられます。 手動によるデータのラベル付けは不要であり、大量のコーディングやメンテナンスも必要ありません。最初は、この方法を試すようお勧めします。

トレーニング ドキュメントを収集する方法のヒントについては、「[トレーニング データ セットの作成](./build-training-data-set.md)」を参照してください。

### <a name="train-with-labels"></a>ラベルを使用したトレーニング

ラベル付けされたデータによるモデルのトレーニングでは、ラベル付けされた指定のフォームを使用して教師あり学習を行うことで、目的とする値が抽出されます。 こちらの方が、複雑なフォームや、キーのない値を含んだフォームでも機能する性能のよいモデルが得られます。

Form Recognizer は、印刷されたテキストや手書きのテキストの要素について、[Layout API](#layout-api) を使用して予想されるサイズや位置を学習します。 その後、ユーザーによって指定されたラベルを使用して、ドキュメントに含まれるキーと値の関係を学習します。 新しいモデルをトレーニングする際はまず、手動でラベル付けされた同じタイプのフォーム 5 つを使用し、そのうえで、モデルの精度を改善するために必要であれば、ラベル付けされたデータを追加することをお勧めします。

## <a name="prebuilt-models"></a>事前構築済みのモデル

Form Recognizer には、固有のフォームの種類を持つ自動データ処理用の事前構築済みモデルも含まれています。

### <a name="prebuilt-receipt-model"></a>事前構築済みのレシート モデル

事前構築済みのレシート モデルは、オーストラリア、カナダ、英国、インド、および米国のレストランやガソリン スタンド、小売店などで使用されている種類の英語のレシートを読み取るために使用されます。 このモデルでは、取引日時、販売店情報、税額、明細項目、合計金額などの主要な情報が抽出されます。 さらに、あらかじめ構築されたレシート モデルは、レシート内のすべてのテキストを認識して返すようにトレーニングされています。 詳細については、[レシート](./concept-receipts.md)の概念ガイドを参照してください。

![サンプルのレシート](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>事前構築済みの名刺モデル

名刺モデルを使用すると、個人の名前、役職、住所、電子メール、会社、電話番号などの情報を英語の名刺から抽出できます。 詳細については、[名刺](./concept-business-cards.md)の概念ガイドを参照してください。

![サンプルの名刺](./media/business-card-english.jpg)

## <a name="layout-api"></a>Layout API

Form Recognizer は、高精細の光学式文字認識 (OCR) を使用して、テキストやテーブル構造 (テキストが関連付けられた行番号と列番号) を抽出することもできます。

## <a name="get-started"></a>はじめに

クイックスタートに従って、フォームからのデータの抽出を開始します。 テクノロジを学習している場合は、無料のサービスを使用することをお勧めします。 無料のページは 1 か月あたり 500 ページに制限されていることに注意してください。

* [クライアント ライブラリのクイックスタート](./quickstarts/client-library.md) (すべての言語、複数のシナリオ)
* Web UI クイックスタート
  * [ラベルを使用したトレーニング - サンプル ラベル付けツール](quickstarts/label-tool.md)
* REST クイック スタート
  * カスタム モデルをトレーニングしてフォーム データを抽出する
    * [ラベルを使用しないトレーニング - cURL](quickstarts/curl-train-extract.md)
    * [ラベルを使用しないトレーニング - Python](quickstarts/python-train-extract.md)
    * [ラベルを使用したトレーニング - Python](quickstarts/python-labeled-data.md)
  * レシートからデータを抽出する
    * [レシートのデータを抽出する - cURL](quickstarts/curl-receipts.md)
    * [レシートのデータを抽出する - Python](quickstarts/python-receipts.md)
  * 名刺からデータを抽出する
    * [名刺データを抽出する - Python](quickstarts/python-business-cards.md)
  * テキストとテーブル構造をフォームから抽出する
    * [レイアウト データを抽出する - Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>REST API を確認します

次の API を使用して、モデルのトレーニングを行い、フォームから構造化データを抽出します。

|名前 |説明 |
|---|---|
| **Train Custom Model**| 同じ種類の 5 つのフォームを使用して、フォームを分析する新しいモデルをトレーニングします。 手動でラベル付けしたデータを使ってトレーニングを行うには、 _useLabelFile_ パラメーターを `true` に設定します。 |
| **Analyze Form** |ストリームとして渡された単一のドキュメントを分析し、カスタム モデルを使用してフォームからテキスト、キーと値のペア、テーブルを抽出します。  |
| **Analyze Receipt** |単一のレシート ドキュメントを分析し、レシート内の主要な情報とその他のテキストを抽出します。|
| **名刺を分析する** |名刺を分析し、主要な情報とテキストを抽出します。|
| **Analyze Layout** |フォームのレイアウトを分析してテキストとテーブル構造を抽出します。|

詳しくは、[REST API のリファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)をご覧ください。 以前のバージョンの API をご利用の方は、「[新機能](./whats-new.md)」の記事で、レシートに関する最新の変更点をご確認ください。

## <a name="input-requirements"></a>入力の要件

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Form Recognizer サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

選択した言語で Form Recognizer を使用してフォーム処理アプリの作成を開始するには、[クライアント ライブラリ クイックスタート](quickstarts/client-library.md)を完了します。