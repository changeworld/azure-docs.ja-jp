---
title: 'はじめに: Azure Form Recognizer REST API v2.1'
description: Azure Form Recognizer の REST API v2.1 を使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d53287f5ed83f87d21880605e80206527264dd3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030218"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!IMPORTANT]
>
> このクイックスタートでは、cURL を使用して REST API 呼び出しを実行し、Azure Form Recognizer API バージョン **2.1** を対象としています。

| [Form Recognizer REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) | [Azure REST API リファレンス](/rest/api/azure/) |

このクイックスタートでは、次の API を使用して、フォームとドキュメントから構造化データを抽出します。

* [レイアウト](#try-it-layout-model)

* [請求書](#try-it-prebuilt-model)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)

* インストールされた [cURL](https://curl.haxx.se/windows/)。

* [PowerShell バージョン 6.0 以降](/powershell/scripting/install/installing-powershell-core-on-windows)、または同様のコマンド ライン アプリケーション。

* Cognitive Services または Form Recognizer リソース。 Azure サブスクリプションを用意できたら、Azure portal で[単一サービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)または[マルチサービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)の Form Recognizer リソースを作成し、キーとエンドポイントを取得します。 Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

  > [!TIP]
  > 1 つのエンドポイント/キーで複数の Cognitive Services にアクセスする予定の場合は、Cognitive Services リソースを作成します。 Form Recognizer アクセスのみの場合は、Form Recognizer リソースを作成します。 [Azure Active Directory 認証](/azure/active-directory/authentication/overview-authentication)を使用する場合は、単一サービス リソースが必要になることに注意してください。

* リソースがデプロイされたら、 **[リソースに移動]** を選択します。 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>コピーしてアプリケーションに貼り付けるコード サンプルを選択します。

* [**Layout**](#try-it-layout-model)

* [**事前構築済みの請求書**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、セキュリティで保護された方法を使用して資格情報を格納し、アクセスします。 詳細については、Cognitive Services の[セキュリティ](/azure/cognitive-services/cognitive-services-security.md)に関するページを参照してください。

## <a name="try-it-layout-model"></a>**試してみる**: レイアウト モデル

> [!div class="checklist"]
>
> * この例では、**URI にフォーム ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `\"{your-document-url}` をサンプル フォーム URL に置き換えます。

```http
https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf
```

#### <a name="request"></a>Request

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/v2.1/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる結果 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。

https://<span></span>cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/ **{resultId}**

次の例では、URL の一部として、`analyzeResults/` の後の文字列が結果 ID になります。

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>レイアウトの結果を取得する

**[Analyze Layout](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)** API を呼び出した後に **[Get Analyze Layout Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `{resultId}` を、前の手順の結果 ID に置き換えます。
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Request

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>結果を確認する

JSON コンテンツを含む `200 (success)` 応答が返されます。

次の請求書の画像とそれに対応する JSON 出力をご覧ください。

* `"readResults"` ノードには、あらゆるテキスト行が、ページ上の対応する境界ボックスの配置と共に表示されます。
* `selectionMarks` ノードには、すべての選択マーク (チェック ボックス、ラジオ マーク) と、その状態が "選択済み" と "未選択" のどちらであるかが示されます。
* 抽出された表は、`"pageResults"` セクションに含まれています。 それぞれの表について、テキスト、行インデックス、列インデックス、行スパン、列スパン、境界ボックスなどが抽出されます。

:::image type="content" source="../../media/contoso-invoice.png" alt-text="表を含む Contoso プロジェクト ステートメント ドキュメント。":::

#### <a name="response-body"></a>応答本文

[GitHub での完全なサンプル出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json)に関する記事を参照してください。

## <a name="try-it-prebuilt-model"></a>**試してみる**: 事前構築済みモデル

> [!div class="checklist"]
>
> * この例では、事前構築済みモデルを使用して請求書ドキュメントを分析します。 このクイックスタートでは、Microsoft の[サンプル請求書ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)を使用できます。

### <a name="choose-a-prebuilt-model"></a>事前構築済みモデルを選択する

請求書に限らず、複数の事前構築済みモデルから選択できます。各モデルには、独自のサポートされているフィールドのセットが含まれます。 分析操作に使用するモデルは、分析するドキュメントの種類によって異なります。 Azure Form Recognizer サービスで現在サポートされている事前構築済みモデルを次に示します。

* [**請求書**](../../concept-invoice.md): 請求書からテキスト、選択マーク、テーブル、フィールド、キー情報を抽出します。
* [**レシート**](../../concept-receipt.md): レシートからテキストとキー情報を抽出します。
* [**身分証明書**](../../concept-id-document.md): 運転免許証と国際パスポートからテキストとキー情報を抽出します。
* [**名刺**](../../concept-business-card.md): 名刺からテキストとキー情報を抽出します。

コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `{subscription key}` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `\"{your-document-url}` をサンプル請求書の URL に置き換えます。

    ```http
    https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf
    ```

#### <a name="request"></a>Request

```bash
curl -v -i POST https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる結果 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。

 _https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/ **{resultId}**_

次の例では、URL の一部として、`analyzeResults/` の後の文字列が結果 ID になります。

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>請求書の結果を取得する

**Analyze Invoice** API を呼び出した後に **[Get Analyze Invoice Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83)** API を呼び出して、操作の状態と抽出されたデータを取得します。 コマンドを実行する前に、次の変更を行います。

1. `{endpoint}` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `{resultId}` を、前の手順の結果 ID に置き換えます。
1. `{subscription key}` は、実際のサブスクリプション キーで置き換えてください。

#### <a name="request"></a>Request

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>結果の確認

JSON 出力で `200 (Success)` 応答を受信します。

* `"readResults"` フィールドには、請求書から抽出された各テキスト行が含まれます。
* `"pageResults"` には、請求書から抽出されたテーブルと選択マークが含まれます。
* `"documentResults"` フィールドには、請求書の最も重要な部分のキーと値の情報が含まれます。

[サンプル請求書](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)に関するドキュメントを参照してください。

#### <a name="response-body"></a>応答本文

[GitHub で完全なサンプル出力](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json)を参照してください。

## <a name="next-steps"></a>次のステップ

お疲れさまでした。 このクイックスタートでは、Azure Form Recognizer REST API を使用して、さまざまな方法でフォームを分析しました。 次に、Form Recognizer v3.0 API の詳細を把握するためにリファレンス ドキュメントを確認します。

> [!div class="nextstepaction"]
> [REST API v2.1 リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
