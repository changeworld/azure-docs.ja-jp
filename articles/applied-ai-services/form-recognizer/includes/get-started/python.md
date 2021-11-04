---
title: 'はじめに: Python v2.1 用 Azure Form Recognizer クライアント ライブラリ'
titleSuffix: Azure Applied AI Services
description: Azure Form Recognizer Python クライアント ライブラリ SDK v2.1 を使用したフォームとドキュメントの処理、データ抽出、分析
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: includes
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: f323d19c1941837c92a66f18eaf8be88260f058e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030217"
---
> [!IMPORTANT]
>
> このクイックスタートは、Azure Form Recognizer REST API **v2.1** を対象としています。
>

[リファレンス ドキュメント](/python/api/azure-ai-formrecognizer) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [パッケージ (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

このクイックスタートでは、次の API を使用して、フォームとドキュメントから構造化データを抽出します。

* [レイアウト](#try-it-layout-model)

* [請求書](#try-it-prebuilt-model)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)

* [Python 3.x](https://www.python.org/)

  * Python のインストールには、[pip](https://pip.pypa.io/en/stable/) が含まれている必要があります。 pip がインストールされているかどうかを確認するには、コマンド ラインで `pip --version` を実行します。 最新バージョンの Python をインストールして pip を入手してください。

* Cognitive Services または Form Recognizer リソース。 Azure サブスクリプションを用意できたら、Azure portal で[単一サービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)または[マルチサービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)の Form Recognizer リソースを作成し、キーとエンドポイントを取得します。 Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

    > [!TIP]
    > 1 つのエンドポイント/キーで複数の Cognitive Services にアクセスする予定の場合は、Cognitive Services リソースを作成します。 Form Recognizer アクセスのみの場合は、Form Recognizer リソースを作成します。 [Azure Active Directory 認証](/azure/active-directory/authentication/overview-authentication)を使用する場合は、単一サービス リソースが必要になることに注意してください。

* リソースがデプロイされたら、 **[リソースに移動]** を選択します。 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

## <a name="set-up"></a>設定

ローカル環境でターミナル ウィンドウを開き、pip を使用して Python 用 Azure Form Recognizer クライアント ライブラリをインストールします。

```console
pip install azure-ai-formrecognizer

```

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

任意のエディターまたは IDE で、**form_recognizer_quickstart.py** という新しい Python アプリケーションを作成します。 その後で、次のライブラリをインポートします。

```python
import os
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential
```

### <a name="create-variables-for-your-azure-resource-endpoint-and-key"></a>Azure リソースのエンドポイントおよびキー用の変数を作成する

```python
endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"
```

この時点で、Python アプリケーションには次のコード行が含まれている必要があります。

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import DocumentAnalysisClient
from azure.core.credentials import AzureKeyCredential

endpoint = "YOUR_FORM_RECOGNIZER_ENDPOINT"
key = "YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY"
```

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
> * ファイルの先頭付近にある `formUrl` 変数にファイル URI 値が追加されています。
> * URI で指定されたファイルの内容を分析するには、`begin_recognize_content_from_url` メソッドを使用します。

### <a name="add-the-following-code-to-your-layout-application-on-the-line-below-the-key-variable"></a>レイアウト アプリケーションの `key` 変数の下の行に、次のコードを追加する

```python

  def format_bounding_box(bounding_box):
    if not bounding_box:
        return "N/A"
    return ", ".join(["[{}, {}]".format(p.x, p.y) for p in bounding_box])

 def recognize_content():
    # sample form document
    formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

    form_recognizer_client = FormRecognizerClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
    form_pages = poller.result()

    for idx, content in enumerate(form_pages):
        print(
            "Page has width: {} and height: {}, measured with unit: {}".format(
                content.width, content.height, content.unit
            )
        )
        for table_idx, table in enumerate(content.tables):
            print(
                "Table # {} has {} rows and {} columns".format(
                    table_idx, table.row_count, table.column_count
                )
            )
            print(
                "Table # {} location on page: {}".format(
                    table_idx, format_bounding_box(table.bounding_box)
                )
            )
            for cell in table.cells:
                print(
                    "...Cell[{}][{}] has text '{}' within bounding box '{}'".format(
                        cell.row_index,
                        cell.column_index,
                        cell.text,
                        format_bounding_box(cell.bounding_box),
                    )
                )

        for line_idx, line in enumerate(content.lines):
            print(
                "Line # {} has word count '{}' and text '{}' within bounding box '{}'".format(
                    line_idx,
                    len(line.words),
                    line.text,
                    format_bounding_box(line.bounding_box),
                )
            )
            if line.appearance:
                if (
                    line.appearance.style_name == "handwriting"
                    and line.appearance.style_confidence > 0.8
                ):
                    print(
                        "Text line '{}' is handwritten and might be a signature.".format(
                            line.text
                        )
                    )
            for word in line.words:
                print(
                    "...Word '{}' has a confidence of {}".format(
                        word.text, word.confidence
                    )
                )

        for selection_mark in content.selection_marks:
            print(
                "Selection mark is '{}' within bounding box '{}' and has a confidence of {}".format(
                    selection_mark.state,
                    format_bounding_box(selection_mark.bounding_box),
                    selection_mark.confidence,
                )
            )
        print("----------------------------------------")


if __name__ == "__main__":
    recognize_content()
```

## <a name="try-it-prebuilt-model"></a>**試してみる**: 事前構築済みモデル

このサンプルでは、請求書を例として使用して、事前トレーニング済みのモデルを使用して、特定の種類の一般的なドキュメントのデータを分析する方法を示します。 [**請求書のフィールド**](../../concept-invoice.md#field-extraction)の一覧については、事前構築済みの概念のページを参照してください

> [!div class="checklist"]
>
> * この例では、事前構築済みモデルを使用して請求書ドキュメントを分析します。 このクイックスタートでは、Microsoft の[サンプル請求書ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)を使用できます。
> * ファイルの上部の 'formUrl' 変数にファイル URI 値を追加しました。
> * 特定のファイルを URI で分析するには、''begin_recognize_invoices_from_url' メソッドを使用します。
> * わかりやすくするために、サービスから返されるフィールドはすべてここには表示されません。 サポートされているすべてのフィールドと対応する型の一覧については、[請求書](../../concept-invoice.md#field-extraction)の概念に関するページを参照してください。

### <a name="choose-a-prebuilt-model"></a>事前構築済みモデルを選択する

請求書に限らず、複数の事前構築済みモデルから選択できます。各モデルには、独自のサポートされているフィールドのセットが含まれます。 分析操作に使用するモデルは、分析するドキュメントの種類によって異なります。 Azure Form Recognizer サービスで現在サポートされている事前構築済みモデルを次に示します。

* [**請求書**](../../concept-invoice.md): 請求書からテキスト、選択マーク、テーブル、フィールド、キー情報を抽出します。
* [**レシート**](../../concept-receipt.md): レシートからテキストとキー情報を抽出します。
* [**身分証明書**](../../concept-id-document.md): 運転免許証と国際パスポートからテキストとキー情報を抽出します。
* [**名刺**](../../concept-business-card.md): 名刺からテキストとキー情報を抽出します。

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-below-the-key-variable"></a>事前構築済みの請求書アプリケーションの `key` 変数の下に、次のコードを追加する

```python

def recognize_invoice():

    invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"

    form_recognizer_client = FormRecognizerClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )

    poller = form_recognizer_client.begin_recognize_invoices_from_url(
        invoiceUrl, locale="en-US"
    )
    invoices = poller.result()

    for idx, invoice in enumerate(invoices):
        vendor_name = invoice.fields.get("VendorName")
        if vendor_name:
            print(
                "Vendor Name: {} has confidence: {}".format(
                    vendor_name.value, vendor_name.confidence
                )
            )
        vendor_address = invoice.fields.get("VendorAddress")
        if vendor_address:
            print(
                "Vendor Address: {} has confidence: {}".format(
                    vendor_address.value, vendor_address.confidence
                )
            )
        vendor_address_recipient = invoice.fields.get("VendorAddressRecipient")
        if vendor_address_recipient:
            print(
                "Vendor Address Recipient: {} has confidence: {}".format(
                    vendor_address_recipient.value, vendor_address_recipient.confidence
                )
            )
        customer_name = invoice.fields.get("CustomerName")
        if customer_name:
            print(
                "Customer Name: {} has confidence: {}".format(
                    customer_name.value, customer_name.confidence
                )
            )
        customer_id = invoice.fields.get("CustomerId")
        if customer_id:
            print(
                "Customer Id: {} has confidence: {}".format(
                    customer_id.value, customer_id.confidence
                )
            )
        customer_address = invoice.fields.get("CustomerAddress")
        if customer_address:
            print(
                "Customer Address: {} has confidence: {}".format(
                    customer_address.value, customer_address.confidence
                )
            )
        customer_address_recipient = invoice.fields.get("CustomerAddressRecipient")
        if customer_address_recipient:
            print(
                "Customer Address Recipient: {} has confidence: {}".format(
                    customer_address_recipient.value,
                    customer_address_recipient.confidence,
                )
            )
        invoice_id = invoice.fields.get("InvoiceId")
        if invoice_id:
            print(
                "Invoice Id: {} has confidence: {}".format(
                    invoice_id.value, invoice_id.confidence
                )
            )
        invoice_date = invoice.fields.get("InvoiceDate")
        if invoice_date:
            print(
                "Invoice Date: {} has confidence: {}".format(
                    invoice_date.value, invoice_date.confidence
                )
            )
        invoice_total = invoice.fields.get("InvoiceTotal")
        if invoice_total:
            print(
                "Invoice Total: {} has confidence: {}".format(
                    invoice_total.value, invoice_total.confidence
                )
            )
        due_date = invoice.fields.get("DueDate")
        if due_date:
            print(
                "Due Date: {} has confidence: {}".format(
                    due_date.value, due_date.confidence
                )
            )
        purchase_order = invoice.fields.get("PurchaseOrder")
        if purchase_order:
            print(
                "Purchase Order: {} has confidence: {}".format(
                    purchase_order.value, purchase_order.confidence
                )
            )
        billing_address = invoice.fields.get("BillingAddress")
        if billing_address:
            print(
                "Billing Address: {} has confidence: {}".format(
                    billing_address.value, billing_address.confidence
                )
            )
        billing_address_recipient = invoice.fields.get("BillingAddressRecipient")
        if billing_address_recipient:
            print(
                "Billing Address Recipient: {} has confidence: {}".format(
                    billing_address_recipient.value,
                    billing_address_recipient.confidence,
                )
            )
        shipping_address = invoice.fields.get("ShippingAddress")
        if shipping_address:
            print(
                "Shipping Address: {} has confidence: {}".format(
                    shipping_address.value, shipping_address.confidence
                )
            )
        shipping_address_recipient = invoice.fields.get("ShippingAddressRecipient")
        if shipping_address_recipient:
            print(
                "Shipping Address Recipient: {} has confidence: {}".format(
                    shipping_address_recipient.value,
                    shipping_address_recipient.confidence,
                )
            )
        print("Invoice items:")
        for idx, item in enumerate(invoice.fields.get("Items").value):
            item_description = item.value.get("Description")
            if item_description:
                print(
                    "......Description: {} has confidence: {}".format(
                        item_description.value, item_description.confidence
                    )
                )
            item_quantity = item.value.get("Quantity")
            if item_quantity:
                print(
                    "......Quantity: {} has confidence: {}".format(
                        item_quantity.value, item_quantity.confidence
                    )
                )
            unit = item.value.get("Unit")
            if unit:
                print(
                    "......Unit: {} has confidence: {}".format(
                        unit.value, unit.confidence
                    )
                )
            unit_price = item.value.get("UnitPrice")
            if unit_price:
                print(
                    "......Unit Price: {} has confidence: {}".format(
                        unit_price.value, unit_price.confidence
                    )
                )
            product_code = item.value.get("ProductCode")
            if product_code:
                print(
                    "......Product Code: {} has confidence: {}".format(
                        product_code.value, product_code.confidence
                    )
                )
            item_date = item.value.get("Date")
            if item_date:
                print(
                    "......Date: {} has confidence: {}".format(
                        item_date.value, item_date.confidence
                    )
                )
            tax = item.value.get("Tax")
            if tax:
                print(
                    "......Tax: {} has confidence: {}".format(tax.value, tax.confidence)
                )
            amount = item.value.get("Amount")
            if amount:
                print(
                    "......Amount: {} has confidence: {}".format(
                        amount.value, amount.confidence
                    )
                )
        subtotal = invoice.fields.get("SubTotal")
        if subtotal:
            print(
                "Subtotal: {} has confidence: {}".format(
                    subtotal.value, subtotal.confidence
                )
            )
        total_tax = invoice.fields.get("TotalTax")
        if total_tax:
            print(
                "Total Tax: {} has confidence: {}".format(
                    total_tax.value, total_tax.confidence
                )
            )
        previous_unpaid_balance = invoice.fields.get("PreviousUnpaidBalance")
        if previous_unpaid_balance:
            print(
                "Previous Unpaid Balance: {} has confidence: {}".format(
                    previous_unpaid_balance.value, previous_unpaid_balance.confidence
                )
            )
        amount_due = invoice.fields.get("AmountDue")
        if amount_due:
            print(
                "Amount Due: {} has confidence: {}".format(
                    amount_due.value, amount_due.confidence
                )
            )
        service_start_date = invoice.fields.get("ServiceStartDate")
        if service_start_date:
            print(
                "Service Start Date: {} has confidence: {}".format(
                    service_start_date.value, service_start_date.confidence
                )
            )
        service_end_date = invoice.fields.get("ServiceEndDate")
        if service_end_date:
            print(
                "Service End Date: {} has confidence: {}".format(
                    service_end_date.value, service_end_date.confidence
                )
            )
        service_address = invoice.fields.get("ServiceAddress")
        if service_address:
            print(
                "Service Address: {} has confidence: {}".format(
                    service_address.value, service_address.confidence
                )
            )
        service_address_recipient = invoice.fields.get("ServiceAddressRecipient")
        if service_address_recipient:
            print(
                "Service Address Recipient: {} has confidence: {}".format(
                    service_address_recipient.value,
                    service_address_recipient.confidence,
                )
            )
        remittance_address = invoice.fields.get("RemittanceAddress")
        if remittance_address:
            print(
                "Remittance Address: {} has confidence: {}".format(
                    remittance_address.value, remittance_address.confidence
                )
            )
        remittance_address_recipient = invoice.fields.get("RemittanceAddressRecipient")
        if remittance_address_recipient:
            print(
                "Remittance Address Recipient: {} has confidence: {}".format(
                    remittance_address_recipient.value,
                    remittance_address_recipient.confidence,
                )
            )


if __name__ == "__main__":
    recognize_invoice()

```

## <a name="run-your-application"></a>アプリケーションを実行する

1. **form_recognizer_quickstart.py** ファイルがあるフォルダーに移動します。

1. ご利用のターミナルで、次のコマンドを入力します。

```console
python form_recognizer_quickstart.py
```

おめでとうございます。 このクイックスタートでは、Form Recognizer Python SDK を使用して、さまざまな方法で各種フォームを分析しました。 次に、Form Recognizer v3.0 API の詳細を把握するためにリファレンス ドキュメントを確認します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API v2.1 リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Azure Form Recognizer Python リファレンス ライブラリ](/python/api/overview/azure/ai-formrecognizer-readme?view=azure-python&preserve-view=true)
