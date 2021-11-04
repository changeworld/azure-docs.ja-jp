---
title: 'はじめに: Java v2.1 用 Azure Form Recognizer クライアント ライブラリ'
description: Java 用 Azure Form Recognizer SDK を使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4e93e5b157cc7bb17eb2e66a97770f4af0d915c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030226"
---
> [!IMPORTANT]
>
> このクイックスタートでは、Azure Form Recognizer バージョン **2.1** を対象とします。

[リファレンスのドキュメント](/java/api/overview/azure/ai-formrecognizer-readme) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [パッケージ (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [サンプル](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

このクイックスタートでは、次の API を使用して、フォームとドキュメントから構造化データを抽出します。

* [レイアウト](#try-it-layout-model)

* [請求書](#try-it-prebuilt-model)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)。
* [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true
) バージョン 8 以降。
* Cognitive Services または Form Recognizer リソース。 Azure サブスクリプションを用意できたら、Azure portal で[単一サービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)または[マルチサービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)の Form Recognizer リソースを作成し、キーとエンドポイントを取得します。 Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

> [!TIP]
> 1 つのエンドポイント/キーで複数の Cognitive Services にアクセスする予定の場合は、Cognitive Services リソースを作成します。 Form Recognizer アクセスのみの場合は、Form Recognizer リソースを作成します。 [Azure Active Directory 認証](/azure/active-directory/authentication/overview-authentication)を使用する場合は、単一サービス リソースが必要になることに注意してください。

* リソースがデプロイされたら、 **[リソースに移動]** をクリックします。 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

## <a name="set-up"></a>設定

### <a name="create-a-new-gradle-project"></a>新しい Gradle プロジェクトを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、**form-recognizer-app** という名前のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir form-recognizer-app && form-recognizer-app
```

1. 作業ディレクトリから `gradle init` コマンドを実行します。 次のコマンドを実行すると、*build.gradle.kts* を含む、Gradle 用の重要なビルド ファイルが作成されます。これは、アプリケーションを作成して構成するために、実行時に使用されます。

    ```console
    gradle init --type basic
    ```

1. **DSL** を選択するよう求められたら、**Kotlin** を選択します。

1. 既定のプロジェクト名 (form-recognizer-app) をそのまま使用する

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

このクイックスタートでは、Gradle 依存関係マネージャーを使用します。 クライアント ライブラリとその他の依存関係マネージャーの情報については、[Maven Central Repository](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) を参照してください。

プロジェクトの *build.gradle.kts* ファイルに、必要なプラグインと設定と共に、クライアント ライブラリを `implementation` ステートメントとして含めます。

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.1")
}
```

### <a name="create-a-java-file"></a>Java ファイルを作成する

作業ディレクトリから、次のコマンドを実行します。

```console
mkdir -p src/main/java
```

次のディレクトリ構造を作成します。

:::image type="content" source="../../media/quickstarts/java-directories.png" alt-text="スクリーンショット: Java ディレクトリ構造":::

Java ディレクトリに移動し、*FormRecognizer.java* という名前のファイルを作成します。  それを任意のエディターまたは IDE で開き、次のパッケージ宣言と `import` ステートメントを追加します。

```java
import com.azure.ai.formrecognizer.*;
import com.azure.ai.formrecognizer.models.*;

import java.util.concurrent.atomic.AtomicReference;
import java.util.List;
import java.util.Map;
import java.time.LocalDate;

import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-applications-main-method"></a>アプリケーションの Main メソッドに貼り付けるコード サンプルを選択してコピーします。

* [**Layout**](#try-it-layout-model)

* [**事前構築済みの請求書**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、セキュリティで保護された方法を使用して資格情報を格納し、アクセスします。 詳細については、Cognitive Services の[セキュリティ](/azure/cognitive-services/cognitive-services-security.md)に関するページを参照してください。

## <a name="try-it-layout-model"></a>**試してみる**: レイアウト モデル

テキスト、選択マーク、テキスト スタイル、およびテーブルの構造を、対応する境界領域の座標と共にドキュメントから抽出します。

> [!div class="checklist"]
>
> * この例では、**URI にフォーム ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。
> * URI で指定されたファイルの内容を分析するには、`beginRecognizeContentFromUrl` メソッドを使用します。
> * Main メソッドの `formUrl` 変数にファイル URI 値を追加してあります。

次のコードを使用して、アプリケーションの **FormRecognizer** クラスを更新します (キーとエンドポイントの変数は、Azure portal の Azure Form Recognizer インスタンスからの値で更新する必要があります)。

```java

static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

public static void main(String[] args) {FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
                .credential(new AzureKeyCredential(key)).endpoint(endpoint).buildClient();

    String formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";

    System.out.println("Get form content...");
        GetContent(recognizerClient, formUrl);
  }
    private static void GetContent(FormRecognizerClient recognizerClient, String invoiceUri) {
        String analyzeFilePath = invoiceUri;
        SyncPoller<FormRecognizerOperationResult, List<FormPage>> recognizeContentPoller = recognizerClient
                .beginRecognizeContentFromUrl(analyzeFilePath);

        List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
        // </snippet_getcontent_call>
        // <snippet_getcontent_print>
        contentResult.forEach(formPage -> {
            // Table information
            System.out.println("----Recognizing content ----");
            System.out.printf("Has width: %f and height: %f, measured with unit: %s.%n", formPage.getWidth(),
                    formPage.getHeight(), formPage.getUnit());
            formPage.getTables().forEach(formTable -> {
                System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                        formTable.getColumnCount());
                formTable.getCells().forEach(formTableCell -> {
                    System.out.printf("Cell has text %s.%n", formTableCell.getText());
                });
                System.out.println();
            });
        });
    }

```

## <a name="try-it-prebuilt-model"></a>**試してみる**: 事前構築済みモデル

このサンプルでは、請求書を例として使用して、事前トレーニング済みのモデルを使用して、特定の種類の一般的なドキュメントのデータを分析する方法を示します。

> [!div class="checklist"]
>
> * この例では、事前構築済みモデルを使用して請求書ドキュメントを分析します。 このクイックスタートでは、Microsoft の[サンプル請求書ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)を使用できます。
> * URI で指定されたファイルの内容を分析するには、`beginRecognizeInvoicesFromUrl` を使用します。
> * Main メソッドの `invoiceUrl` 変数にファイル URI 値を追加してあります。
> * わかりやすくするために、サービスから返されるフィールドはすべてここには表示されません。 サポートされているすべてのフィールドと対応する型の一覧については、[請求書](../../concept-invoice.md#field-extraction)の概念に関するページを参照してください。

### <a name="choose-a-prebuilt-model"></a>事前構築済みモデルを選択する

請求書に限らず、複数の事前構築済みモデルから選択できます。各モデルには、独自のサポートされているフィールドのセットが含まれます。 分析操作に使用するモデルは、分析するドキュメントの種類によって異なります。 Azure Form Recognizer サービスで現在サポートされている事前構築済みモデルを次に示します。

* [**請求書**](../../concept-invoice.md): 請求書からテキスト、選択マーク、テーブル、フィールド、キー情報を抽出します。
* [**レシート**](../../concept-receipt.md): レシートからテキストとキー情報を抽出します。
* [**身分証明書**](../../concept-id-document.md): 運転免許証と国際パスポートからテキストとキー情報を抽出します。
* [**名刺**](../../concept-business-card.md): 名刺からテキストとキー情報を抽出します。

次のコードを使用して、アプリケーションの **FormRecognizer** クラスを更新します (キーとエンドポイントの変数は、Azure portal の Azure Form Recognizer インスタンスからの値で更新する必要があります)。

```java

static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

public static void main(String[] args) {
    FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder().credential(new AzureKeyCredential(key)).endpoint(endpoint).buildClient();

    String invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

    System.out.println("Analyze invoice...");
        AnalyzeInvoice(recognizerClient, invoiceUrl);
  }
    private static void AnalyzeInvoice(FormRecognizerClient recognizerClient, String invoiceUrl) {
      SyncPoller < FormRecognizerOperationResult,
        List < RecognizedForm >> recognizeInvoicesPoller = recognizerClient.beginRecognizeInvoicesFromUrl(invoiceUrl);
      List < RecognizedForm > recognizedInvoices = recognizeInvoicesPoller.getFinalResult();

      for (int i = 0; i < recognizedInvoices.size(); i++) {
        RecognizedForm recognizedInvoice = recognizedInvoices.get(i);
        Map < String,
        FormField > recognizedFields = recognizedInvoice.getFields();
        System.out.printf("----------- Recognized invoice info for page %d -----------%n", i);
        FormField vendorNameField = recognizedFields.get("VendorName");
        if (vendorNameField != null) {
            if (FieldValueType.STRING == vendorNameField.getValue().getValueType()) {
                String merchantName = vendorNameField.getValue().asString();
                System.out.printf("Vendor Name: %s, confidence: %.2f%n", merchantName, vendorNameField.getConfidence());
            }
        }

        FormField vendorAddressField = recognizedFields.get("VendorAddress");
        if (vendorAddressField != null) {
            if (FieldValueType.STRING == vendorAddressField.getValue().getValueType()) {
                String merchantAddress = vendorAddressField.getValue().asString();
                System.out.printf("Vendor address: %s, confidence: %.2f%n", merchantAddress, vendorAddressField.getConfidence());
            }
        }

        FormField customerNameField = recognizedFields.get("CustomerName");
        if (customerNameField != null) {
            if (FieldValueType.STRING == customerNameField.getValue().getValueType()) {
                String merchantAddress = customerNameField.getValue().asString();
                System.out.printf("Customer Name: %s, confidence: %.2f%n", merchantAddress, customerNameField.getConfidence());
            }
        }

        FormField customerAddressRecipientField = recognizedFields.get("CustomerAddressRecipient");
        if (customerAddressRecipientField != null) {
            if (FieldValueType.STRING == customerAddressRecipientField.getValue().getValueType()) {
                String customerAddr = customerAddressRecipientField.getValue().asString();
                System.out.printf("Customer Address Recipient: %s, confidence: %.2f%n", customerAddr, customerAddressRecipientField.getConfidence());
            }
        }

        FormField invoiceIdField = recognizedFields.get("InvoiceId");
        if (invoiceIdField != null) {
            if (FieldValueType.STRING == invoiceIdField.getValue().getValueType()) {
                String invoiceId = invoiceIdField.getValue().asString();
                System.out.printf("Invoice Id: %s, confidence: %.2f%n", invoiceId, invoiceIdField.getConfidence());
            }
        }

        FormField invoiceDateField = recognizedFields.get("InvoiceDate");
        if (customerNameField != null) {
            if (FieldValueType.DATE == invoiceDateField.getValue().getValueType()) {
                LocalDate invoiceDate = invoiceDateField.getValue().asDate();
                System.out.printf("Invoice Date: %s, confidence: %.2f%n", invoiceDate, invoiceDateField.getConfidence());
            }
        }

        FormField invoiceTotalField = recognizedFields.get("InvoiceTotal");
        if (customerAddressRecipientField != null) {
            if (FieldValueType.FLOAT == invoiceTotalField.getValue().getValueType()) {
                Float invoiceTotal = invoiceTotalField.getValue().asFloat();
                System.out.printf("Invoice Total: %.2f, confidence: %.2f%n", invoiceTotal, invoiceTotalField.getConfidence());
            }
        }
    }
}

```

## <a name="build-and-run-your-application"></a>アプリケーションをビルドおよび実行する

メイン プロジェクト ディレクトリ **form-recognizer-app** に戻ります。

1. `build` コマンドを使用してアプリケーションをビルドします。

```console
gradle build
```

1. `run` コマンドを使用してアプリケーションを実行します。

```console
gradle run
```

おめでとうございます。 このクイックスタートでは、Azure Form Recognizer Java SDK を使用して、さまざまな方法でフォームとドキュメントを分析しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを探索します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API v2.1 リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Azure Form Recognizer Java ライブラリ リファレンス](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-stable&preserve-view=true)
