---
title: 'クイックスタート: Form Recognizer JavaScript SDK v3.0 | プレビュー'
titleSuffix: Azure Applied AI Services
description: Form Recognizer JavaScript クライアント ライブラリ SDK v3.0 を使用したフォームとドキュメントの処理、データ抽出、分析 (プレビュー)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: d3af8169bf482acbbc7122c55094231e834b3aa9
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709825"
---
# <a name="quickstart-form-recognizer-javascript-client-library-sdks-v30--preview"></a>クイックスタート: Form Recognizer JavaScript クライアント ライブラリ SDK v3.0 | プレビュー

>[!NOTE]
> Form Recognizer v3.0 は現在、パブリック プレビュー段階です。 一部の機能がサポートされなかったり、機能が制限されたりすることがあります。

[リファレンスのドキュメント](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-form-recognizer/4.0.0-beta.1/index.html) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/formrecognizer/ai-form-recognizer/src) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [サンプル](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_4.0.0-beta.1/sdk/formrecognizer/ai-form-recognizer/README.md)

JavaScript プログラミング言語を使用して、Azure Form Recognizer の使用を開始します。 Azure Form Recognizer は、機械学習を使用してドキュメントからフォーム フィールド、テキスト、テーブルを抽出して分析するクラウドベースの Azure Applied AI Service です。 お使いのワークフローとアプリケーションに Microsoft のクライアント ライブラリ SDK を統合して、Form Recognizer モデルを簡単に呼び出すことができます。 テクノロジを学習している場合は、無料のサービスを使用することをお勧めします。 無料のページは 1 か月あたり 500 ページに制限されていることに注意してください。

Azure Form Recognizer の機能と開発オプションの詳細については、「[概要](../overview.md#form-recognizer-features-and-development-options)」ページを参照してください。

このクイックスタートでは、次の機能を使用して、フォームとドキュメントからデータと値を分析および抽出します。

* [🆕**一般的なドキュメント**](#try-it-general-document-model): テキスト、テーブル、構造、キーと値のペア、名前付きエンティティを分析および抽出します。

* [**レイアウト**](#try-it-layout-model) - モデルをトレーニングすることなく、フォーム ドキュメント内のテーブル、行、単語、およびラジオ ボタンやチェック ボックスなどの選択マークを分析および抽出します。

* [**事前構築済みの請求書**](#try-it-prebuilt-model): 事前トレーニング済みの請求書モデルを使用して、請求書から共通フィールドを分析および抽出します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)。

* [Visual Studio Code](https://code.visualstudio.com/) または好みの IDE の最新バージョン。 

* [Node.js](https://nodejs.org/about/releases/) の最新 LTS バージョン

* Cognitive Services または Form Recognizer リソース。 Azure サブスクリプションを用意できたら、Azure portal で[単一サービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)または[マルチサービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)の Form Recognizer リソースを作成し、キーとエンドポイントを取得します。 Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

    > [!TIP]
    > 1 つのエンドポイント/キーで複数の Cognitive Services にアクセスする予定の場合は、Cognitive Services リソースを作成します。 Form Recognizer アクセスのみの場合は、Form Recognizer リソースを作成します。 [Azure Active Directory 認証](../../../active-directory/authentication/overview-authentication.md)を使用する場合は、単一サービス リソースが必要になることに注意してください。

* リソースがデプロイされたら、 **[リソースに移動]** を選択します。 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

## <a name="set-up"></a>設定

1. 新しい Node.js アプリケーションを作成します。 コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。

    ```console
    mkdir form-recognizer-app && cd form-recognizer-app
    ```

1. `npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。

    ```console
    npm init
    ```

1. `ai-form-recognizer` クライアント ライブラリ npm パッケージをインストールします。

    ```console
    npm install @azure/ai-form-recognizer@4.0.0-beta.1 @azure/identity
    ```

    * アプリの `package.json` ファイルが依存関係によって更新されます。

1. `index.js` という名前のファイルを作成して開き、次のライブラリを追加します。

    ```javascript
   const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");
    ```

1. 自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。

    ```javascript
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    ```

この時点で、JavaScript アプリケーションには次のコード行が含まれている必要があります。

```javascript
const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");

const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>コピーしてアプリケーションに貼り付けるコード サンプルを選択します。

* [**一般的なドキュメント**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**事前構築済みの請求書**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、セキュリティで保護された方法を使用して資格情報を格納し、アクセスします。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services/cognitive-services-security.md)に関する記事を参照してください。

## <a name="try-it-general-document-model"></a>**試してみる**: 一般的なドキュメント モデル

> [!div class="checklist"]
>
> * この例では、**URI にフォーム ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。
> * URI で指定されたファイルの内容を分析するには、`beginExtractGenericDocument` メソッドを使用します。
> * ファイルの先頭付近にある `formUrl` 変数にファイル URI 値が追加されています。
> * サポートされているフィールドと対応する型の一覧については、[一般ドキュメント](../concept-general-document.md#named-entity-recognition-ner-categories)の概念に関するページを参照してください。

### <a name="add-the-following-code-to-your-general-document-application-on-the-line-below-the-apikey-variable"></a>一般的なドキュメント アプリケーションの `apiKey` 変数の下の行に、次のコードを追加する

```javascript

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = new DocumentAnalysisClient(endpoint, new DefaultAzureCredential(apiKey));

    const poller = await client.beginExtractGenericDocument(formUrl);

    const {
        keyValuePairs,
        entities
    } = await poller.pollUntilDone();

    if (keyValuePairs.length <= 0) {
        console.log("No key-value pairs were extracted from the document.");
    } else {
        console.log("Key-Value Pairs:");
        for (const {
                key,
                value,
                confidence
            } of keyValuePairs) {
            console.log("- Key  :", `"${key.content}"`);
            console.log("  Value:", `"${value?.content ?? "<undefined>"}" (${confidence})`);
        }
    }

    if (entities.length <= 0) {
        console.log("No entities were extracted from the document.");
    } else {
        console.log("Entities:");
        for (const entity of entities) {
            console.log(
                `- "${entity.content}" ${entity.category} - ${entity.subCategory ?? "<none>"} (${
          entity.confidence
        })`
            );
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});
```

## <a name="try-it-layout-model"></a>**試してみる**: レイアウト モデル

> [!div class="checklist"]
>
> * この例では、**URI にフォーム ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。
> * ファイルの先頭付近にある `formUrl` 変数にファイル URI 値が追加されています。
> * URI で指定されたファイルの内容を分析するには、`beginExtractLayout` メソッドを使用します。

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-apikey-variable"></a>レイアウト アプリケーションの `apiKey` 変数の下の行に、次のコードを追加する

```javascript

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginExtractLayout(formUrl);

    const {
        pages,
        tables
    } = await poller.pollUntilDone();

    if (pages.length <= 0) {
        console.log("No pages were extracted from the document.");
    } else {
        console.log("Pages:");
        for (const page of pages) {
            console.log("- Page", page.pageNumber, `(unit: ${page.unit})`);
            console.log(`  ${page.width}x${page.height}, angle: ${page.angle}`);
            console.log(`  ${page.lines.length} lines, ${page.words.length} words`);
        }
    }

    if (tables.length <= 0) {
        console.log("No tables were extracted from the document.");
    } else {
        console.log("Tables:");
        for (const table of tables) {
            console.log(
                `- Extracted table: ${table.columnCount} columns, ${table.rowCount} rows (${table.cells.length} cells)`
            );
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

## <a name="try-it-prebuilt-model"></a>**試してみる**: 事前構築済みモデル

このサンプルでは、請求書を例に、事前トレーニング済みのモデルを使用して、特定の種類の共通ドキュメントのデータを分析する方法を示します。

> [!div class="checklist"]
>
> * この例では、事前構築済みモデルを使用して請求書ドキュメントを分析します。 このクイックスタートでは、Microsoft の[サンプル請求書ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)を使用できます。
> * ファイルの先頭にある `invoiceUrl` 変数にファイル URI 値が追加されています。
> * URI で特定のファイルを分析するには、`beginAnalyzeDocuments` メソッドを使用して、モデル ID として `PrebuiltModels.Invoice` を渡します。返される値は、送信されたドキュメントに関するデータを含む `result` オブジェクトです。
> * わかりやすくするために、サービスから返されるキーと値のペアはすべてここには表示されません。 サポートされているフィールドと対応する型の一覧については、[請求書](../concept-invoice.md#field-extraction)の概念に関するページを参照してください。

### <a name="choose-the-invoice-prebuilt-model-id"></a>請求書の事前構築済みモデル ID を選択する

請求書に限らず、複数の事前構築済みモデルから選択できます。各モデルには、独自のサポートされているフィールドのセットが含まれます。 分析操作に使用するモデルは、分析するドキュメントの種類によって異なります。 Form Recognizer サービスで現在サポートされている事前構築済みモデルのモデル ID を次に示します。

* [**prebuilt-invoice**](../concept-invoice.md): 請求書からテキスト、選択マーク、テーブル、キーと値のペア、キー情報を抽出します。
* [**prebuilt-receipt**](../concept-receipt.md): レシートからテキストとキー情報を抽出します。
* [**prebuilt-idDocument**](../concept-id-document.md): 運転免許証と国際パスポートからテキストとキー情報を抽出します。
* [**prebuilt-businessCard**](../concept-business-card.md): 名刺からテキストとキー情報を抽出します。

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-apikey-variable"></a>事前構築済みの請求書アプリケーションの `apiKey` 変数の下に、次のコードを追加する

```javascript

const {PreBuiltModels} = require("@azure/ai-form-recognizer");

// Use of PrebuiltModels.Receipt above (rather than the raw model ID), adds strong typing of the model's output

const invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function main() {

    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginAnalyzeDocuments(PrebuiltModels.Invoice, invoiceUrl);

    const {
        documents: [result]
    } = await poller.pollUntilDone();

    if (result) {
        const invoice = result.fields;

        console.log("Vendor Name:", invoice.vendorName?.value);
        console.log("Customer Name:", invoice.customerName?.value);
        console.log("Invoice Date:", invoice.invoiceDate?.value);
        console.log("Due Date:", invoice.dueDate?.value);

        console.log("Items:");
        for (const {
                properties: item
            } of invoice.items?.values ?? []) {
            console.log("-", item.productCode?.value ?? "<no product code>");
            console.log("  Description:", item.description?.value);
            console.log("  Quantity:", item.quantity?.value);
            console.log("  Date:", item.date?.value);
            console.log("  Unit:", item.unit?.value);
            console.log("  Unit Price:", item.unitPrice?.value);
            console.log("  Tax:", item.tax?.value);
            console.log("  Amount:", item.amount?.value);
        }

        console.log("Subtotal:", invoice.subTotal?.value);
        console.log("Previous Unpaid Balance:", invoice.previousUnpaidBalance?.value);
        console.log("Tax:", invoice.totalTax?.value);
        console.log("Amount Due:", invoice.amountDue?.value);
    } else {
        throw new Error("Expected at least one receipt in the result.");
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

## <a name="run-your-application"></a>アプリケーションを実行する

1. Form Recognizer アプリケーション (form-recognizer-app) があるフォルダーに移動します。

1. ターミナルで、次のコマンドを入力します。

```console
node index.js
```

おめでとうございます。 このクイックスタートでは、Form Recognizer JavaScript SDK を使用して、さまざまな方法で各種フォームを分析しました。 次に、Form Recognizer v3.0 API の詳細を把握するためにリファレンス ドキュメントを確認します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API v3.0 リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Form Recognizer JavaScript リファレンス ライブラリ](https://azuresdkdocs.blob.core.windows.net/$web/javascript/azure-ai-form-recognizer/4.0.0-beta.1/index.html)