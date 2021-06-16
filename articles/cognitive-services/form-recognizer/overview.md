---
title: Azure Form Recognizer とは?
titleSuffix: Azure Applied AI Services
description: Azure Form Recognizer サービスを使用すると、フォーム ドキュメントからキーと値のペアとテーブル データを識別して抽出することや、領収書や名刺から主要な情報を抽出することができます。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: 自動データ処理、ドキュメント処理、自動データ入力、フォーム処理
ms.openlocfilehash: 1688cc686b11fd6a6edb129a7ac01a048ae88f4a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111957497"
---
# <a name="what-is-azure-form-recognizer"></a>Azure Form Recognizer とは?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer は、機械学習テクノロジを利用して自動データ処理ソフトウェアを構築ことを可能にする [Azure Applied AI Services](../../applied-ai-services/index.yml) の一部です。 ドキュメントからテキスト、キーと値のペア、選択マーク、テーブル、構造を特定して抽出します。このサービスによって、元のファイル、境界ボックス、信頼度などのリレーションシップを含む構造化データが出力されます。 手作業による操作やデータ サイエンスに関する専門知識があまりなくても、特定のコンテンツに合わせた正確な結果がすばやく得られます。 Form Recognizer を使用して、アプリケーションのデータ入力を自動化し、ドキュメントの検索機能を強化します。

Form Recognizer は、カスタム ドキュメント処理モデルと、請求書、レシート、ID、名刺の事前構築済みモデル、およびレイアウト モデルで構成されています。 REST API またはクライアント ライブラリ SDK を使用して Form Recognizer モデルを呼び出すことにより、複雑さを軽減し、自分のワークフローやアプリケーションに統合することができます。

このドキュメントには、次の種類の記事が含まれています。

* [**概念**](concept-layout.md)では、サービスの機能と特徴について詳しく説明します。
* [**クイックスタート**](quickstarts/client-library.md)は、サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](build-training-data-set.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* [**チュートリアル**](tutorial-ai-builder.md)はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。

## <a name="form-recognizer-features"></a>Form Recognizer の特徴

Form Recognizer を使用すると、以下の機能を使用して、ドキュメント データを簡単に抽出および分析できます。

### <a name="layout"></a>[レイアウト](concept-layout.md)

テキスト、選択マーク、およびテーブルの構造を、対応する境界ボックスの座標と共にドキュメントから抽出します。

Form Recognizer では、高精細の光学式文字認識 (OCR) とドキュメントからの高度なディープ ラーニング モデルを使用して、テキスト、選択マーク、およびテーブル構造 (テキストに関連付けられた行および列番号) を抽出できます。

:::image type="content" source="./media/tables-example.jpg" alt-text="テーブルの例" lightbox="./media/tables-example.jpg":::

### <a name="custom-models"></a>[カスタム モデル](concept-custom.md)

フォームからテキスト、キーと値のペア、選択マーク、およびテーブル データを抽出します。 これらのモデルは自分が用意した独自のデータでトレーニングされるため、実際のフォームに合わせて調整されます。

Form Recognizer のカスタム モデルでは、独自のデータでトレーニングを行います。また、5 つのサンプル入力フォームだけで開始できます。 トレーニング済みのドキュメント処理モデルは、元の form ドキュメント内の関係を含む構造化データを出力できます。 モデルをトレーニングした後、モデルをテストおよび再トレーニングでき、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

カスタム モデルをトレーニングする際は、ラベル付けされたデータを使用したトレーニングとラベル付けされたデータを使用しないトレーニングのどちらかを選択できます。

#### <a name="train-without-labels"></a>ラベルを使用しないトレーニング

Form Recognizer では、教師なし学習を使用して、フォーム内でのレイアウトおよびフィールドやエントリ間の関係を把握します。 入力フォームを送信すると、アルゴリズムによって種類別にフォームが分類されて、存在するキーとテーブルが検出されます。また、値がキーに、エントリがテーブルに関連付けられます。 ラベルを使用しないトレーニングでは、手動によるデータのラベル付けや大量のコーディングとメンテナンスは必要ありません。この方法を最初に試してみることをお勧めします。

トレーニング ドキュメントを収集する方法のヒントについては、「[トレーニング データ セットの作成](./build-training-data-set.md)」を参照してください。

#### <a name="train-with-labels"></a>ラベルを使用したトレーニング

ラベル付けされたデータによるモデルのトレーニングでは、ラベル付けされた指定のフォームを使用して、教師あり学習を使って、目的とする値が抽出されます。 ラベル付きデータにより、性能の高いモデルが得られ、複雑なフォームやキーのない値を含んだフォームでも機能するモデルを作成できます。

Form Recognizer では、[Layout API](#layout) を使用して、印刷または手書きのテキストの要素について予想されるサイズや位置を学習し、テーブルを抽出します。 その後、ユーザーによって指定されたラベルを使用して、ドキュメントに含まれるキーと値の関係およびテーブルを学習します。 新しいモデルをトレーニングする際はまず、手動でラベル付けされた同じタイプ (同じ構造) のフォーム 5 つを使用し、そのうえで、モデルの精度を改善するために必要であれば、ラベル付けされたデータを追加することをお勧めします。 Form Recognizer を使用すると、教師あり学習機能を使用してキーと値のペアおよびテーブルを抽出するようにモデルをトレーニングできます。

[ラベルを使用したトレーニングを開始する](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


### <a name="prebuilt-models"></a>事前構築済みのモデル

 Form Recognizer には、領収書、名刺、請求書、および身分証明書のデータ処理を自動化するための事前構築済みモデルも含まれています。

### <a name="receipts"></a>[Receipts](concept-receipts.md)

事前構築済みのレシート モデルは、オーストラリア、カナダ、英国、インド、および米国のレストランやガソリン スタンド、小売店などで使用されている種類の英語のレシートを読み取るために使用されます。 このモデルでは、取引日時、販売店情報、税額、明細項目、合計金額などの主要な情報が抽出されます。 さらに、あらかじめ構築されたレシート モデルは、レシート内のすべてのテキストを分析して返すようにトレーニングされています。

:::image type="content" source="./media/overview-receipt.jpg" alt-text="サンプルのレシート" lightbox="./media/overview-receipt.jpg":::

### <a name="business-cards"></a>[名刺](concept-business-cards.md)

名刺モデルを使用すると、個人の名前、役職、住所、電子メール、会社、電話番号などの情報を英語の名刺から抽出できます。

:::image type="content" source="./media/overview-business-card.jpg" alt-text="サンプルの名刺" lightbox="./media/overview-business-card.jpg":::

### <a name="invoices"></a>[Invoices](concept-invoices.md)

事前構築済みの請求書モデルでは、さまざまな形式の請求書からデータが抽出されて、構造化データが返されます。 このモデルでは、請求書 ID、顧客の詳細、仕入先の詳細、出荷先、請求先、合計金額、税金、小計、品目などの主要な情報が抽出されます。 さらに、あらかじめ構築された請求書モデルは、請求書にあるすべてのテキストとテーブルを分析して返すようにトレーニングされています。

:::image type="content" source="./media/overview-invoices.jpg" alt-text="サンプル請求書" lightbox="./media/overview-invoices.jpg":::

### <a name="identity-documents"></a>[ID ドキュメント](concept-identification-cards.md)

身分証明書 (ID) を使用すると、世界中のパスポートと米国の運転免許証から重要な情報を抽出できます。 ドキュメント ID、生年月日、有効期限日、名前、国、地域、コンピューターで読み取り可能なゾーンなどのデータが抽出されます。

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="ID カードのサンプル" lightbox="./media/overview-id.jpg":::

## <a name="get-started"></a>開始

Form Recognizer ツールのサンプルを使用して、お使いのドキュメントでレイアウト、モデルの事前構築、およびカスタム モデルのトレーニングを試すことができます。 Form Recognizer サービスを試すには、Azure サブスクリプション ([**無料で作成**](https://azure.microsoft.com/free/cognitive-services)) と [**Form Recognizer リソース**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) エンドポイントおよびキーが必要です。

### <a name="v21"></a>[v2.1](#tab/v2-1)

>
> [!div class="nextstepaction"]
> [Form Recognizer を試す](https://aka.ms/fott-2.1-ga/)
>

### <a name="v20"></a>[v2.0](#tab/v2-0)

>
> [!div class="nextstepaction"]
> [Form Recognizer を試す](https://fott.azurewebsites.net/)

---

[クライアント ライブラリまたは REST API のクイックスタート](./quickstarts/client-library.md)の記事に従って、ドキュメントからのデータ抽出を開始します。 テクノロジを学習している場合は、無料のサービスを使用することをお勧めします。 無料のページは 1 か月あたり 500 ページに制限されていることに注意してください。

### <a name="v21"></a>[v2.1](#tab/v2-1)

詳しくは、[REST API のリファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)をご覧ください。 以前のバージョンの API をご利用の方は、「[新機能](./whats-new.md)」の記事で、レシートに関する最新の変更点をご確認ください。

### <a name="v20"></a>[v2.0](#tab/v2-0)

詳しくは、[REST API のリファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)をご覧ください。 以前のバージョンの API をご利用の方は、「[新機能](./whats-new.md)」の記事で、レシートに関する最新の変更点をご確認ください。

---

## <a name="input-requirements"></a>入力の要件

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="service-availability-and-redundancy"></a>サービスの可用性と冗長性

### <a name="is-form-recognizer-service-zone-resilient"></a>Form Recognizer サービスにゾーン回復性はありますか?

はい。 Form Recognizer サービスには、ゾーン回復性が既定で備わっています。

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Form Recognizer サービスにゾーン回復性を構成するにはどうすればよいですか?

ゾーン回復性を有効にするために、顧客による構成は必要ありません。 Form Recognizer リソースのゾーン回復性は、既定で使用できるようになっており、サービス自体によって管理されます。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

* Cognitive Services 全般に言えることですが、Form Recognizer サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

* 顧客がサービス インスタンスをデプロイしたリージョンの外部で、Form Recognizer によって、顧客データの格納または処理が行われることはありません。

## <a name="next-steps"></a>次のステップ

Form Recognizer サービスの詳細については、オンライン ツールとクイックスタートをお試しください。

* [**Form Recognizer Tool**](https://aka.ms/fott-2.1-ga)
* [**クライアント ライブラリと REST API のクイックスタート**](quickstarts/client-library.md)