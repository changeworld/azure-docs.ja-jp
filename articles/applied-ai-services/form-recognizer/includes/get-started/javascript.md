---
title: 'クイック スタート: Azure Form Recognizer JavaScript SDK v2.1'
titleSuffix: Azure Applied AI Services
description: Azure Form Recognizer JavaScript クライアント ライブラリ v2.1 を使用したフォームとドキュメントの処理、データ抽出、分析
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: f978333bdb10433f19831b5255010dacef7c81aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030210"
---
> [!IMPORTANT]
>
> このクイックスタートは、Azure Form Recognizer REST API **v2.1** を対象としています。

[リファレンスのドキュメント](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-latest&preserve-view=true) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

このクイックスタートでは、次の API を使用して、フォームとドキュメントから構造化データを抽出します。

* [レイアウト](#try-it-layout-model)

* [請求書](#try-it-prebuilt-model)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)。

* [Visual Studio Code](https://code.visualstudio.com/) または好みの IDE の最新バージョン。

* [Node.js](https://nodejs.org/about/releases/) の最新 LTS バージョン

* Cognitive Services または Form Recognizer リソース。 Azure サブスクリプションを用意できたら、Azure portal で[単一サービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)または[マルチサービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)の Form Recognizer リソースを作成し、キーとエンドポイントを取得します。 Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

    > [!TIP]
    > 1 つのエンドポイント/キーで複数の Cognitive Services にアクセスする予定の場合は、Cognitive Services リソースを作成します。 Form Recognizer アクセスのみの場合は、Form Recognizer リソースを作成します。 [Azure Active Directory 認証](/azure/active-directory/authentication/overview-authentication)を使用する場合は、単一サービス リソースが必要になることに注意してください。

* リソースがデプロイされたら、 **[リソースに移動]** を選択します。 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

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
    npm install @azure/ai-form-recognizer
    ```

    アプリの `package.json` ファイルが依存関係によって更新されます。

1. `index.js` という名前のファイルを作成して開き、次のライブラリをインポートします。

    ```javascript
    const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
    ```

1. 自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。

    ```javascript
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    ```

1. この時点で、JavaScript アプリケーションには次のコード行が含まれている必要があります。

    ```javascript

    const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");

    const endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";
    const apiKey = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    ```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>コピーしてアプリケーションに貼り付けるコード サンプルを選択します。

* [**Layout**](#try-it-layout-model)

* [**事前構築済みの請求書**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、セキュリティで保護された方法を使用して資格情報を格納し、アクセスします。 詳細は、Cognitive Services [セキュリティ]((/azure/cognitive-services/cognitive-services-security.md) に関する記事を参照してください。

## <a name="try-it-layout-model"></a>**試してみる**: レイアウト モデル

> [!div class="checklist"]
>
> * この例では、**URI にフォーム ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。
> * ファイルの先頭付近にある `formUrl` 変数にファイル URI 値が追加されています。
> * URI で指定されたファイルの内容を分析するには、`beginRecognizeContent` メソッドを使用します。

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-apikey-variable"></a>レイアウト アプリケーションの `apiKey` 変数の下の行に、次のコードを追加する

```javascript
const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function recognizeContent() {
    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeContentFromUrl(formUrl);
    const pages = await poller.pollUntilDone();

    if (!pages || pages.length === 0) {
        throw new Error("Expecting non-empty list of pages!");
    }

    for (const page of pages) {
        console.log(
            `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
        );
        for (const table of page.tables) {
            for (const cell of table.cells) {
                console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
            }
        }
    }
}

recognizeContent().catch((err) => {
    console.error("The sample encountered an error:", err);
});

```

## <a name="try-it-prebuilt-model"></a>**試してみる**: 事前構築済みモデル

このサンプルでは、請求書を例として使用して、事前トレーニング済みのモデルを使用して、特定の種類の一般的なドキュメントのデータを分析する方法を示します。 [**請求書のフィールド**](../../concept-invoice.md#field-extraction)の一覧については、事前構築済みの概念のページを参照してください

> [!div class="checklist"]
>
> * この例では、事前構築済みモデルを使用して請求書ドキュメントを分析します。 このクイックスタートでは、Microsoft の[サンプル請求書ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)を使用できます。
> * ファイルの先頭にある `invoiceUrl` 変数にファイル URI 値が追加されています。
> * URI で指定されたファイルの内容を分析するには、`beginRecognizeInvoices` メソッドを使用します。
> * わかりやすくするために、サービスから返されるフィールドはすべてここには表示されません。 サポートされているすべてのフィールドと対応する型の一覧については、[請求書](../../concept-invoice.md#field-extraction)の概念に関するページを参照してください。

### <a name="choose-a-prebuilt-model"></a>事前構築済みモデルを選択する

請求書に限らず、複数の事前構築済みモデルから選択できます。各モデルには、独自のサポートされているフィールドのセットが含まれます。 分析操作に使用するモデルは、分析するドキュメントの種類によって異なります。 Azure Form Recognizer サービスで現在サポートされている事前構築済みモデルを次に示します。

* [**請求書**](../../concept-invoice.md): 請求書からテキスト、選択マーク、テーブル、フィールド、キー情報を抽出します。
* [**レシート**](../../concept-receipt.md): レシートからテキストとキー情報を抽出します。
* [**身分証明書**](../../concept-id-document.md): 運転免許証と国際パスポートからテキストとキー情報を抽出します。
* [**名刺**](../../concept-business-card.md): 名刺からテキストとキー情報を抽出します。

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-apikey-variable"></a>事前構築済みの請求書アプリケーションの `apiKey` 変数の下に、次のコードを追加する

```javascript

const invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

async function recognizeInvoices() {

    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await client.beginRecognizeInvoicesFromUrl(invoiceUrl);
    const [invoice] = await poller.pollUntilDone();

    if (invoice === undefined) {
        throw new Error("Failed to extract data from at least one invoice.");
    }

    /**
     * This is a helper function for printing a simple field with an elemental type.
     */
    function fieldToString(field) {
        const {
            name,
            valueType,
            value,
            confidence
        } = field;
        return `${name} (${valueType}): '${value}' with confidence ${confidence}'`;
    }

    console.log("Invoice fields:");

    /**
     * Invoices contain a lot of optional fields, but they are all of elemental types
     * such as strings, numbers, and dates, so we will just enumerate them all.
     */
    for (const [name, field] of Object.entries(invoice.fields)) {
        if (field.valueType !== "array" && field.valueType !== "object") {
            console.log(`- ${name} ${fieldToString(field)}`);
        }
    }

    // Invoices also support nested line items, so we can iterate over them.
    let idx = 0;

    console.log("- Items:");

    const items = invoice.fields["Items"]?.value;
    for (const item of items ?? []) {
        const value = item.value;

        // Each item has several subfields that are nested within the item. We'll
        // map over this list of the subfields and filter out any fields that
        // weren't found. Not all fields will be returned every time, only those
        // that the service identified for the particular document in question.

        const subFields = [
                "Description",
                "Quantity",
                "Unit",
                "UnitPrice",
                "ProductCode",
                "Date",
                "Tax",
                "Amount"
            ]
            .map((fieldName) => value[fieldName])
            .filter((field) => field !== undefined);

        console.log(
            [
                `  - Item #${idx}`,
                // Now we will convert those fields into strings to display
                ...subFields.map((field) => `    - ${fieldToString(field)}`)
            ].join("\n")
        );
    }
}

recognizeInvoices().catch((err) => {
    console.error("The sample encountered an error:", err);
});

```

おめでとうございます。 このクイックスタートでは、Form Recognizer JavaScript SDK を使用して、さまざまな方法で各種フォームを分析しました。 次に、Form Recognizer v3.0 API の詳細を把握するためにリファレンス ドキュメントを確認します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API v2.1 リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Form Recognizer JavaScript リファレンス ライブラリ](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-latest&preserve-view=true)
