---
title: 'はじめに: .NET v2.1 用 Azure Form Recognizer クライアント ライブラリ'
description: .NET 用 Azure Form Recognizer SDK を使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: bface4c7df450e3123f8e4be73e5c942ff95a8cc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030225"
---
<!-- markdownlint-disable MD024 -->

<!-- markdownlint-disable MD033 -->
> [!IMPORTANT]
>
> このクイックスタートでは、cURL を使用して REST API 呼び出しを実行し、Azure Form Recognizer API バージョン **2.1** を対象としています。
>
>* この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。

[リファレンスのドキュメント](/dotnet/api/overview/azure/ai.formrecognizer-readme) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

このクイックスタートでは、次の API を使用して、フォームとドキュメントから構造化データを抽出します。

* [レイアウト](#try-it-layout-model)

* [請求書](#try-it-prebuilt-model)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)。

* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) の現在のバージョン。 <!-- or [.NET Core](https://dotnet.microsoft.com/download). -->

* Cognitive Services または Form Recognizer リソース。 Azure サブスクリプションを用意できたら、Azure portal で[単一サービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)または[マルチサービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)の Form Recognizer リソースを作成し、キーとエンドポイントを取得します。 Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

    > [!TIP]
    > 1 つのエンドポイント/キーで複数の Cognitive Services にアクセスする予定の場合は、Cognitive Services リソースを作成します。 Form Recognizer アクセスのみの場合は、Form Recognizer リソースを作成します。 [Azure Active Directory 認証](/azure/active-directory/authentication/overview-authentication)を使用する場合は、単一サービス リソースが必要になることに注意してください。

* リソースがデプロイされたら、 **[リソースに移動]** をクリックします。 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

## <a name="set-up"></a>設定

1. Visual Studio 2019 を起動します。

1. スタート ページで、 [新しいプロジェクトの作成] を選択します。

    :::image type="content" source="../../media/quickstarts/start-window.png" alt-text="スクリーンショット: Visual Studio のスタート ウィンドウ。":::

1. **[新しいプロジェクトの作成]** ページで、検索ボックスに「**コンソール**」と入力します。 **[コンソール アプリケーション]** テンプレートを選択し、 **[次へ]** を選択します。

    :::image type="content" source="../../media/quickstarts/create-new-project.png" alt-text="スクリーンショット: Visual Studio の [新しいプロジェクトの作成] ページ。":::

1. **[新しいプロジェクトの構成]** ダイアログ ウィンドウの [プロジェクト名] ボックスに「`formRecognizer_quickstart`」と入力します。 その後、[次へ] を選択します。

    :::image type="content" source="../../media/quickstarts/configure-new-project.png" alt-text="スクリーンショット: Visual Studio の [新しいプロジェクトの構成] ダイアログ ウィンドウ。":::

1. **[追加情報]** ダイアログ ウィンドウで、 **[.NET 5.0 (最新)]** を選択し、 **[作成]** を選択します。

    :::image type="content" source="../../media/quickstarts/additional-information.png" alt-text="スクリーンショット: Visual Studio の [追加情報] ダイアログ ウィンドウ。":::

### <a name="install-the-client-library-with-nuget"></a>NuGet を使用してクライアント ライブラリをインストールする

 1. **formRecognizer_quickstart** プロジェクトを右クリックし、 **[NuGet パッケージの管理...]** を選択します。

    :::image type="content" source="../../media/quickstarts/select-nuget-package.png" alt-text="スクリーンショット: select-nuget-package.png":::

 1. [参照] タブを選択し、「Azure.AI.FormRecognizer」と入力します。

     :::image type="content" source="../../media/quickstarts/azure-nuget-package.png" alt-text="スクリーンショット: select-form-recognizer-package.png":::

 1. ドロップダウン メニューからバージョン **3.1.1** を選び、**[インストール]** を選択します。

## <a name="build-your-application"></a>アプリケーションをビルドする

Form Recognizer サービスと対話するには、`FormRecognizerClient` クラスのインスタンスを作成する必要があります。 これを行うには、ご自分の apiKey を使用して `AzureKeyCredential` を作成し、`AzureKeyCredential` とお使いの Form Recognizer `endpoint` を使用して `FormRecognizerClient` インスタンスを作成します。

1. **Program.cs** ファイルを開きます。

1. 次の using ディレクティブを含めます。

```csharp
using System;
using Azure;
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using System.Threading.Tasks;
```

1. `endpoint` と `apiKey` の環境変数を設定し、`AzureKeyCredential` と `FormRecognizerClient` のインスタンスを作成します。

```csharp
private static readonly string endpoint = "your-form-recognizer-endpoint";
private static readonly string apiKey = "your-api-key";
private static readonly AzureKeyCredential credential = new AzureKeyCredential(apiKey);
```

1. 行 `Console.Writeline("Hello World!");` を削除し、**Program.cs** ファイルの **Main** メソッドに **試してみる** のコード サンプルのいずれかを追加します。

    :::image type="content" source="../../media/quickstarts/add-code-here.png" alt-text="スクリーンショット: サンプル コードを Main メソッドに追加する。":::

1. アプリケーションの Main メソッドに貼り付けるコード サンプルを選択してコピーします。

    * [**Layout**](#try-it-layout-model)

    * [**事前構築済みの請求書**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、セキュリティで保護された方法を使用して資格情報を格納し、アクセスします。 詳細は、Cognitive Services [セキュリティ](.(/azure/cognitive-services/cognitive-services-security.md) に関する記事を参照してください。

## <a name="try-it-layout-model"></a>**試してみる**: レイアウト モデル

テキスト、選択マーク、テキスト スタイル、およびテーブルの構造を、対応する境界領域の座標と共にドキュメントから抽出します。

> [!div class="checklist"]
>
> * この例では、**URI にフォーム ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。
> * ファイル URI の値を `formUri` 変数に追加しました。
> * URI で指定されたファイルからレイアウトを抽出するには、`StartRecognizeContentFromUriAsync` メソッドを使用します。

### <a name="add-the-following-code-to-your-layout-application-main-method"></a>レイアウト アプリケーションの **Main** メソッドに次のコードを追加します。

```csharp

FormRecognizerClient recognizerClient = AuthenticateClient();

Task recognizeContent = RecognizeContent(recognizerClient);
Task.WaitAll(recognizeContent);
```

### <a name="add-the-following-code-below-the-main-method"></a>次のコードを **Main** メソッドの下に追加します。

```csharp

private static FormRecognizerClient AuthenticateClient()
            {
                var credential = new AzureKeyCredential(apiKey);
                var client = new FormRecognizerClient(new Uri(endpoint), credential);
                return client;
            }

            private static async Task RecognizeContent(FormRecognizerClient recognizerClient)
        {
            string formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";
            FormPageCollection formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(formUrl))
        .WaitForCompletionAsync();

            foreach (FormPage page in formPages)
            {
                Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count} lines.");

                for (int i = 0; i < page.Lines.Count; i++)
                {
                    FormLine line = page.Lines[i];
                    Console.WriteLine($"    Line {i} has {line.Words.Count} word{(line.Words.Count > 1 ? "s" : "")}, and text: '{line.Text}'.");
                }

                for (int i = 0; i < page.Tables.Count; i++)
                {
                    FormTable table = page.Tables[i];
                    Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
                    foreach (FormTableCell cell in table.Cells)
                    {
                        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains text: '{cell.Text}'.");
                    }
                }
            }
        }
    }
}

```

## <a name="try-it-prebuilt-model"></a>**試してみる**: 事前構築済みモデル

このサンプルでは、請求書を例として使用して、事前トレーニング済みのモデルを使用して、特定の種類の一般的なドキュメントのデータを分析する方法を示します。

> [!div class="checklist"]
>
> * この例では、事前構築済みモデルを使用して請求書ドキュメントを分析します。 このクイックスタートでは、Microsoft の[サンプル請求書ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)を使用できます。
> * Main メソッドの上部にある `invoiceUri` 変数にファイル URI 値が追加されています。
> * URI で指定されたファイルの内容を分析するには、`StartRecognizeInvoicesFromUriAsync` メソッドを使用します。
> * わかりやすくするために、サービスから返されるフィールドはすべてここには表示されません。 サポートされているすべてのフィールドと対応する型の一覧については、[請求書](../../concept-invoice.md#field-extraction)の概念に関するページを参照してください。

### <a name="choose-a-prebuilt-model"></a>事前構築済みモデルを選択する

請求書に限らず、複数の事前構築済みモデルから選択できます。各モデルには、独自のサポートされているフィールドのセットが含まれます。 分析操作に使用するモデルは、分析するドキュメントの種類によって異なります。 Azure Form Recognizer サービスで現在サポートされている事前構築済みモデルを次に示します。

* [**請求書**](../../concept-invoice.md): 請求書からテキスト、選択マーク、テーブル、フィールド、キー情報を抽出します。
* [**レシート**](../../concept-receipt.md): レシートからテキストとキー情報を抽出します。
* [**身分証明書**](../../concept-id-document.md): 運転免許証と国際パスポートからテキストとキー情報を抽出します。
* [**名刺**](../../concept-business-card.md): 名刺からテキストとキー情報を抽出します。

### <a name="add-the-following-code-to-your-prebuilt-invoice-application-main-method"></a>事前構築済みの請求書アプリケーションの **Main** メソッドに次のコードを追加します

```csharp
FormRecognizerClient recognizerClient = AuthenticateClient();

            Task analyzeinvoice = AnalyzeInvoice(recognizerClient, invoiceUrl);
            Task.WaitAll(analyzeinvoice);
```

### <a name="add-the-following-code-below-the-main-method"></a>次のコードを **Main** メソッドの下に追加します。

```csharp
   private static FormRecognizerClient AuthenticateClient() {
     var credential = new AzureKeyCredential(apiKey);
     var client = new FormRecognizerClient(new Uri(endpoint), credential);
     return client;
   }

   static string invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

   private static async Task AnalyzeInvoice(FormRecognizerClient recognizerClient, string invoiceUrl) {
     var options = new RecognizeInvoicesOptions() {
       Locale = "en-US"
     };
     RecognizedFormCollection invoices = await recognizerClient.StartRecognizeInvoicesFromUriAsync(new Uri(invoiceUrl), options).WaitForCompletionAsync();

     RecognizedForm invoice = invoices[0];

     FormField invoiceIdField;
     if (invoice.Fields.TryGetValue("InvoiceId", out invoiceIdField)) {
       if (invoiceIdField.Value.ValueType == FieldValueType.String) {
         string invoiceId = invoiceIdField.Value.AsString();
         Console.WriteLine($ "    Invoice Id: '{invoiceId}', with confidence {invoiceIdField.Confidence}");
       }
     }

     FormField invoiceDateField;
     if (invoice.Fields.TryGetValue("InvoiceDate", out invoiceDateField)) {
       if (invoiceDateField.Value.ValueType == FieldValueType.Date) {
         DateTime invoiceDate = invoiceDateField.Value.AsDate();
         Console.WriteLine($ "    Invoice Date: '{invoiceDate}', with confidence {invoiceDateField.Confidence}");
       }
     }

     FormField dueDateField;
     if (invoice.Fields.TryGetValue("DueDate", out dueDateField)) {
       if (dueDateField.Value.ValueType == FieldValueType.Date) {
         DateTime dueDate = dueDateField.Value.AsDate();
         Console.WriteLine($ "    Due Date: '{dueDate}', with confidence {dueDateField.Confidence}");
       }
     }

     FormField vendorNameField;
     if (invoice.Fields.TryGetValue("VendorName", out vendorNameField)) {
       if (vendorNameField.Value.ValueType == FieldValueType.String) {
         string vendorName = vendorNameField.Value.AsString();
         Console.WriteLine($ "    Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}");
       }
     }

     FormField vendorAddressField;
     if (invoice.Fields.TryGetValue("VendorAddress", out vendorAddressField)) {
       if (vendorAddressField.Value.ValueType == FieldValueType.String) {
         string vendorAddress = vendorAddressField.Value.AsString();
         Console.WriteLine($ "    Vendor Address: '{vendorAddress}', with confidence {vendorAddressField.Confidence}");
       }
     }

     FormField customerNameField;
     if (invoice.Fields.TryGetValue("CustomerName", out customerNameField)) {
       if (customerNameField.Value.ValueType == FieldValueType.String) {
         string customerName = customerNameField.Value.AsString();
         Console.WriteLine($ "    Customer Name: '{customerName}', with confidence {customerNameField.Confidence}");
       }
     }

     FormField customerAddressField;
     if (invoice.Fields.TryGetValue("CustomerAddress", out customerAddressField)) {
       if (customerAddressField.Value.ValueType == FieldValueType.String) {
         string customerAddress = customerAddressField.Value.AsString();
         Console.WriteLine($ "    Customer Address: '{customerAddress}', with confidence {customerAddressField.Confidence}");
       }
     }

     FormField customerAddressRecipientField;
     if (invoice.Fields.TryGetValue("CustomerAddressRecipient", out customerAddressRecipientField)) {
       if (customerAddressRecipientField.Value.ValueType == FieldValueType.String) {
         string customerAddressRecipient = customerAddressRecipientField.Value.AsString();
         Console.WriteLine($ "    Customer address recipient: '{customerAddressRecipient}', with confidence {customerAddressRecipientField.Confidence}");
       }
     }

     FormField invoiceTotalField;
     if (invoice.Fields.TryGetValue("InvoiceTotal", out invoiceTotalField)) {
       if (invoiceTotalField.Value.ValueType == FieldValueType.Float) {
         float invoiceTotal = invoiceTotalField.Value.AsFloat();
         Console.WriteLine($ "    Invoice Total: '{invoiceTotal}', with confidence {invoiceTotalField.Confidence}");
       }
     }
   }
 }
}
```

## <a name="run-your-application"></a>アプリケーションを実行する

プログラムをビルドして実行するには、formRecognizer_quickstart の横にある緑色の **[スタート]** ボタンを選択するか、**F5** キーを押します。

  :::image type="content" source="../../media/quickstarts/run-visual-studio.png" alt-text="スクリーンショット: Visual Studioプログラムの実行。":::

<!-- --- -->

おめでとうございます。 このクイックスタートでは、Form Recognizer C# SDK を使用して、さまざまな方法でフォームとドキュメントを分析しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを探索します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API v2.1 リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Azure Form Recognizer C# または .NET リファレンス ライブラリ](/dotnet/api/overview/azure/AI.FormRecognizer-readme)
