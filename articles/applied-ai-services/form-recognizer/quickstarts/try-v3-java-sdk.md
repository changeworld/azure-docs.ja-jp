---
title: 'クイックスタート: Form Recognizer Java SDK v3.0 | プレビュー'
titleSuffix: Azure Applied AI Services
description: Form Recognizer Java クライアント ライブラリ SDK v3.0 を使用したフォームとドキュメントの処理、データ抽出、分析 (プレビュー)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 29cb08ef3933d63906c658da20cb7e00a20b7d32
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717825"
---
# <a name="quickstart-java-client-library-sdk-v30--preview"></a>クイックスタート: Java クライアント ライブラリ SDK v3.0 | プレビュー

>[!NOTE]
> Form Recognizer v3.0 は現在、パブリック プレビュー段階です。 一部の機能がサポートされなかったり、機能が制限されたりすることがあります。

[リファレンスのドキュメント](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-formrecognizer/4.0.0-beta.1/index.html) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [パッケージ (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [サンプル](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

Java プログラミング言語を使用して、Azure Form Recognizer の使用を開始します。 Azure Form Recognizer は、機械学習を使用してドキュメントからフォーム フィールド、テキスト、テーブルを抽出して分析するクラウドベースの Azure Applied AI Service です。 お使いのワークフローとアプリケーションに Microsoft のクライアント ライブラリ SDK を統合して、Form Recognizer モデルを簡単に呼び出すことができます。 テクノロジを学習している場合は、無料のサービスを使用することをお勧めします。 無料のページは 1 か月あたり 500 ページに制限されていることに注意してください。

Azure Form Recognizer の機能と開発オプションの詳細については、「[概要](../overview.md#form-recognizer-features-and-development-options)」ページを参照してください。

このクイックスタートでは、次の機能を使用して、フォームとドキュメントからデータと値を分析および抽出します。

* [🆕**一般的なドキュメント**](#try-it-general-document-model): テキスト、テーブル、構造、キーと値のペア、名前付きエンティティを分析および抽出します。

* [**レイアウト**](#try-it-layout-model) - モデルをトレーニングすることなく、フォーム ドキュメント内のテーブル、行、単語、およびラジオ ボタンやチェック ボックスなどの選択マークを分析および抽出します。

* [**事前構築済みの請求書**](#try-it-prebuilt-model): 事前トレーニング済みの請求書モデルを使用して、請求書から共通フィールドを分析および抽出します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)。
* [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true
) バージョン 8 以降。
* Cognitive Services または Form Recognizer リソース。 Azure サブスクリプションを用意できたら、Azure portal で[単一サービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)または[マルチサービス](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)の Form Recognizer リソースを作成し、キーとエンドポイントを取得します。 Free 価格レベル (<ph id="ph1">`F0`</ph>) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

> [!TIP] 
> 1 つのエンドポイント/キーで複数の Cognitive Services にアクセスする予定の場合は、Cognitive Services リソースを作成します。 Form Recognizer アクセスのみの場合は、Form Recognizer リソースを作成します。 [Azure Active Directory 認証](../../../active-directory/authentication/overview-authentication.md)を使用する場合は、単一サービス リソースが必要になることに注意してください。

* リソースがデプロイされたら、 **[リソースに移動]** をクリックします。 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal のキーとエンドポイントの場所。":::

## <a name="set-up"></a>設定

### <a name="create-a-new-gradle-project"></a>新しい Gradle プロジェクトを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、**form-recognizer-app** という名前のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir form-recognizer-app && form-recognizer-app
```

作業ディレクトリから `gradle init` コマンドを実行します。 次のコマンドを実行すると、*build.gradle.kts* を含む、Gradle 用の重要なビルド ファイルが作成されます。これは、アプリケーションを作成して構成するために、実行時に使用されます。

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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "4.0.0-beta.1")
}
```

### <a name="create-a-java-file"></a>Java ファイルを作成する

作業ディレクトリから、次のコマンドを実行します。

```console
mkdir -p src/main/java
```

次のディレクトリ構造を作成します。

:::image type="content" source="../media/quickstarts/java-directories.png" alt-text="スクリーンショット: Java ディレクトリ構造":::

新しいフォルダーに移動し、*FormRecognizer.java* という名前のファイルを作成します (`gradle init` コマンドの実行時に作成されます)。 それを任意のエディターまたは IDE で開き、次のパッケージ宣言と `import` ステートメントを追加します。

```java
package com.azure.ai.formrecognizer;

import com.azure.ai.formrecognizer.models.AnalyzeDocumentOptions;
import com.azure.ai.formrecognizer.models.AnalyzedDocument;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.HttpPipeline;
import com.azure.core.http.HttpPipelineBuilder;
import com.azure.core.util.Context;

import java.io.ByteArrayInputStream;
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.nio.file.Files;
import java.util.Arrays;
```

### <a name="select-a-code-sample-to-copy-and-paste-into-your-applications-main-method"></a>アプリケーションの Main メソッドに貼り付けるコード サンプルを選択してコピーします。

* [**一般的なドキュメント**](#try-it-general-document-model)

* [**Layout**](#try-it-layout-model)

* [**事前構築済みの請求書**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、セキュリティで保護された方法を使用して資格情報を格納し、アクセスします。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services/cognitive-services-security.md)に関するページを参照してください。

## <a name="try-it-general-document-model"></a>**試してみる**: 一般的なドキュメント モデル

> [!div class="checklist"]
>
> * この例では、**URI にフォーム ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。
> * URI で特定のファイルを分析するには、`beginAnalyzeDocumentFromUrl` メソッドを使用して、モデル ID として `prebuilt-document` を渡します。返される値は、送信されたドキュメントに関するデータを含む `AnalyzeResult` オブジェクトです。
> * Main メソッドの `documentUrl` 変数にファイル URI 値を追加してあります。
> * わかりやすくするために、サービスから返されるエンティティ フィールドはすべてここには表示されません。 サポートされているすべてのフィールドと対応する型の一覧については、[一般ドキュメント](../concept-general-document.md#named-entity-recognition-ner-categories)の概念に関するページを参照してください。

次のコードを使用して、アプリケーションの **FormRecognizer** クラスを更新します (キーとエンドポイントの変数は、Azure portal の Azure Form Recognizer インスタンスからの値で更新する必要があります)。

```java
public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String documentUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";
        String modelId = "prebuilt-document";
        SyncPoller < DocumentOperationResult, AnalyzeResult > analyzeDocumentPoller =
            documentAnalysisClient.beginAnalyzeDocumentFromUrl(modelId, documentUrl);

        AnalyzeResult analyzeResult = analyzeDocumentPoller.getFinalResult();

        for (int i = 0; i < analyzeResult.getDocuments().size(); i++) {
            final AnalyzedDocument analyzedDocument = analyzeResult.getDocuments().get(i);
            System.out.printf("----------- Analyzing document %d -----------%n", i);
            System.out.printf("Analyzed document has doc type %s with confidence : %.2f%n",
                analyzedDocument.getDocType(), analyzedDocument.getConfidence());
        }

        analyzeResult.getPages().forEach(documentPage - > {
            System.out.printf("Page has width: %.2f and height: %.2f, measured with unit: %s%n",
                documentPage.getWidth(),
                documentPage.getHeight(),
                documentPage.getUnit());

            // lines
            documentPage.getLines().forEach(documentLine - >
                System.out.printf("Line %s is within a bounding box %s.%n",
                    documentLine.getContent(),
                    documentLine.getBoundingBox().toString()));

            // words
            documentPage.getWords().forEach(documentWord - >
                System.out.printf("Word %s has a confidence score of %.2f%n.",
                    documentWord.getContent(),
                    documentWord.getConfidence()));
        });

        // tables
        List < DocumentTable > tables = analyzeResult.getTables();
        for (int i = 0; i < tables.size(); i++) {
            DocumentTable documentTable = tables.get(i);
            System.out.printf("Table %d has %d rows and %d columns.%n", i, documentTable.getRowCount(),
                documentTable.getColumnCount());
            documentTable.getCells().forEach(documentTableCell - > {
                System.out.printf("Cell '%s', has row index %d and column index %d.%n",
                    documentTableCell.getContent(),
                    documentTableCell.getRowIndex(), documentTableCell.getColumnIndex());
            });
            System.out.println();
        }

        // Entities
        analyzeResult.getEntities().forEach(documentEntity - > {
            System.out.printf("Entity category : %s, sub-category %s%n: ",
                documentEntity.getCategory(), documentEntity.getSubCategory());
            System.out.printf("Entity content: %s%n: ", documentEntity.getContent());
            System.out.printf("Entity confidence: %.2f%n", documentEntity.getConfidence());
        });

        // Key-value
        analyzeResult.getKeyValuePairs().forEach(documentKeyValuePair - > {
            System.out.printf("Key content: %s%n", documentKeyValuePair.getKey().getContent());
            System.out.printf("Key content bounding region: %s%n",
                documentKeyValuePair.getKey().getBoundingRegions().toString());

            System.out.printf("Value content: %s%n", documentKeyValuePair.getValue().getContent());
            System.out.printf("Value content bounding region: %s%n", documentKeyValuePair.getValue().getBoundingRegions().toString());
        });
        Build a custom document analysis model
        In 3. x.x, creating a custom model required specifying useTrainingLabels to indicate whether to use labeled data when creating the custom model with the beginTraining method.
        In 4. x.x, we introduced the new general document model(prebuilt - document) to replace the train without labels functionality from 3. x.x which extracts entities, key - value pairs, and layout from a document with the beginBuildModel method.In 4. x.x the beginBuildModel always returns labeled data otherwise.
        Train a custom model using 3. x.x beginTraining:

            String trainingFilesUrl = "{SAS_URL_of_your_container_in_blob_storage}";
        SyncPoller < FormRecognizerOperationResult, CustomFormModel > trainingPoller =
            formTrainingClient.beginTraining(trainingFilesUrl,
                false,
                new TrainingOptions()
                .setModelName("my model trained without labels"),
                Context.NONE);

        CustomFormModel customFormModel = trainingPoller.getFinalResult();

        // Model Info
        System.out.printf("Model Id: %s%n", customFormModel.getModelId());
        System.out.printf("Model name given by user: %s%n", customFormModel.getModelName());
        System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
        System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
        System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());

        System.out.println("Recognized Fields:");
        // looping through the subModels, which contains the fields they were trained on
        // Since the given training documents are unlabeled we still group them but, they do not have a label.
        customFormModel.getSubmodels().forEach(customFormSubmodel - > {
            System.out.printf("Submodel Id: %s%n: ", customFormSubmodel.getModelId());
            // Since the training data is unlabeled, we are unable to return the accuracy of this model
            customFormSubmodel.getFields().forEach((field, customFormModelField) - >
                System.out.printf("Field: %s Field Label: %s%n",
                    field, customFormModelField.getLabel()));
        });

        System.out.println();
        customFormModel.getTrainingDocuments().forEach(trainingDocumentInfo - > {
            System.out.printf("Document name: %s%n", trainingDocumentInfo.getName());
            System.out.printf("Document status: %s%n", trainingDocumentInfo.getStatus());
            System.out.printf("Document page count: %d%n", trainingDocumentInfo.getPageCount());
            if (!trainingDocumentInfo.getErrors().isEmpty()) {
                System.out.println("Document Errors:");
                trainingDocumentInfo.getErrors().forEach(formRecognizerError - >
                    System.out.printf("Error code %s, Error message: %s%n", formRecognizerError.getErrorCode(),
                        formRecognizerError.getMessage()));
            }
        });
    }
```

## <a name="try-it-layout-model"></a>**試してみる**: レイアウト モデル

テキスト、選択マーク、テキスト スタイル、およびテーブルの構造を、対応する境界領域の座標と共にドキュメントから抽出します。

> [!div class="checklist"]
>
> * この例では、**URI にフォーム ドキュメント ファイル** が必要になります。 このクイックスタートでは、Microsoft の[サンプル フォーム ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf)を使用できます。
> * URI で特定のファイルを分析するには、`beginAnalyzeDocumentFromUrl` メソッドを使用して、モデル ID として `prebuilt-layout` を渡します。返される値は、送信されたドキュメントに関するデータを含む `AnalyzeResult` オブジェクトです。
> * Main メソッドの `documentUrl` 変数にファイル URI 値を追加してあります。

次のコードを使用して、アプリケーションの **FormRecognizer** クラスを更新します (キーとエンドポイントの変数は、Azure portal の Azure Form Recognizer インスタンスからの値で更新する必要があります)。

```java
public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String documentUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"
        String modelId = "prebuilt-layout";

        SyncPoller < DocumentOperationResult, AnalyzeResult > analyzeLayoutResultPoller =
            documentAnalysisClient.beginAnalyzeDocument(modelId, documentUrl);

        AnalyzeResult analyzeLayoutResult = analyzeLayoutResultPoller.getFinalResult();

        // pages
        analyzeLayoutResult.getPages().forEach(documentPage - > {
            System.out.printf("Page has width: %.2f and height: %.2f, measured with unit: %s%n",
                documentPage.getWidth(),
                documentPage.getHeight(),
                documentPage.getUnit());

            // lines
            documentPage.getLines().forEach(documentLine - >
                System.out.printf("Line %s is within a bounding box %s.%n",
                    documentLine.getContent(),
                    documentLine.getBoundingBox().toString()));

            // selection marks
            documentPage.getSelectionMarks().forEach(documentSelectionMark - >
                System.out.printf("Selection mark is %s and is within a bounding box %s with confidence %.2f.%n",
                    documentSelectionMark.getState().toString(),
                    documentSelectionMark.getBoundingBox().toString(),
                    documentSelectionMark.getConfidence()));
        });

        // tables
        List < DocumentTable > tables = analyzeLayoutResult.getTables();
        for (int i = 0; i < tables.size(); i++) {
            DocumentTable documentTable = tables.get(i);
            System.out.printf("Table %d has %d rows and %d columns.%n", i, documentTable.getRowCount(),
                documentTable.getColumnCount());
            documentTable.getCells().forEach(documentTableCell - > {
                System.out.printf("Cell '%s', has row index %d and column index %d.%n", documentTableCell.getContent(),
                    documentTableCell.getRowIndex(), documentTableCell.getColumnIndex());
            });
            System.out.println();
        }
    }
```

## <a name="try-it-prebuilt-model"></a>**試してみる**: 事前構築済みモデル

このサンプルでは、請求書を例として使用して、事前トレーニング済みのモデルを使用して、特定の種類の一般的なドキュメントのデータを分析する方法を示します。

> [!div class="checklist"]
>
> * この例では、事前構築済みモデルを使用して請求書ドキュメントを分析します。 このクイックスタートでは、Microsoft の[サンプル請求書ドキュメント](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf)を使用できます。
> * URI で特定のファイルを分析するには、`beginAnalyzeDocumentFromUrl` メソッドを使用して、モデル ID として `prebuilt-invoice` を渡します。返される値は、送信されたドキュメントに関するデータを含む `AnalyzeResult` オブジェクトです。
> * Main メソッドの `invoiceUrl` 変数にファイル URI 値を追加してあります。
> * わかりやすくするために、サービスから返されるキーと値のペアはすべてここには表示されません。 サポートされているフィールドと対応する型の一覧については、[請求書](../concept-invoice.md#field-extraction)の概念に関するページを参照してください。

### <a name="choose-the-invoice-prebuilt-model-id"></a>請求書の事前構築済みモデル ID を選択する

請求書に限らず、複数の事前構築済みモデルから選択できます。各モデルには、独自のサポートされているフィールドのセットが含まれます。 分析操作に使用するモデルは、分析するドキュメントの種類によって異なります。 Form Recognizer サービスで現在サポートされている事前構築済みモデルのモデル ID を次に示します。

* [**prebuilt-invoice**](../concept-invoice.md): 請求書からテキスト、選択マーク、テーブル、キーと値のペア、キー情報を抽出します。
* [**prebuilt-receipt**](../concept-receipt.md): レシートからテキストとキー情報を抽出します。
* [**prebuilt-idDocument**](../concept-id-document.md): 運転免許証と国際パスポートからテキストとキー情報を抽出します。
* [**prebuilt-businessCard**](../concept-business-card.md): 名刺からテキストとキー情報を抽出します。

次のコードを使用して、アプリケーションの **FormRecognizer** クラスを更新します (キーとエンドポイントの変数は、Azure portal の Azure Form Recognizer インスタンスからの値で更新する必要があります)。

```java

public class FormRecognizer {

    static final String key = "PASTE_YOUR_FORM_RECOGNIZER_SUBSCRIPTION_KEY_HERE";
    static final String endpoint = "PASTE_YOUR_FORM_RECOGNIZER_ENDPOINT_HERE";

    public static void main(String[] args) {

        DocumentAnalysisClient documentAnalysisClient = new DocumentAnalysisClientBuilder()
            .credential(new AzureKeyCredential("{key}"))
            .endpoint("{endpoint}")
            .buildClient();

        String invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf"
        String modelId = "prebuilt-invoice";

        PollerFlux < DocumentOperationResult, AnalyzeResult > analyzeInvoicePoller = client.beginAnalyzeDocumentFromUrl("prebuilt-invoice", invoiceUrl);

        Mono < AnalyzeResult > analyzeInvoiceResultMono = analyzeInvoicePoller
            .last()
            .flatMap(pollResponse - > {
                if (pollResponse.getStatus().isComplete()) {
                    System.out.println("Polling completed successfully");
                    return pollResponse.getFinalResult();
                } else {
                    return Mono.error(new RuntimeException("Polling completed unsuccessfully with status:" +
                        pollResponse.getStatus()));
                }
            });

        analyzeInvoiceResultMono.subscribe(analyzeInvoiceResult - > {
            for (int i = 0; i < analyzeInvoiceResult.getDocuments().size(); i++) {
                AnalyzedDocument analyzedInvoice = analyzeInvoiceResult.getDocuments().get(i);
                Map < String, DocumentField > invoiceFields = analyzedInvoice.getFields();
                System.out.printf("----------- Analyzing invoice  %d -----------%n", i);
                DocumentField vendorNameField = invoiceFields.get("VendorName");
                if (vendorNameField != null) {
                    if (DocumentFieldType.STRING == vendorNameField.getType()) {
                        String merchantName = vendorNameField.getValueString();
                        System.out.printf("Vendor Name: %s, confidence: %.2f%n",
                            merchantName, vendorNameField.getConfidence());
                    }
                }

                DocumentField vendorAddressField = invoiceFields.get("VendorAddress");
                if (vendorAddressField != null) {
                    if (DocumentFieldType.STRING == vendorAddressField.getType()) {
                        String merchantAddress = vendorAddressField.getValueString();
                        System.out.printf("Vendor address: %s, confidence: %.2f%n",
                            merchantAddress, vendorAddressField.getConfidence());
                    }
                }

                DocumentField customerNameField = invoiceFields.get("CustomerName");
                if (customerNameField != null) {
                    if (DocumentFieldType.STRING == customerNameField.getType()) {
                        String merchantAddress = customerNameField.getValueString();
                        System.out.printf("Customer Name: %s, confidence: %.2f%n",
                            merchantAddress, customerNameField.getConfidence());
                    }
                }

                DocumentField customerAddressRecipientField = invoiceFields.get("CustomerAddressRecipient");
                if (customerAddressRecipientField != null) {
                    if (DocumentFieldType.STRING == customerAddressRecipientField.getType()) {
                        String customerAddr = customerAddressRecipientField.getValueString();
                        System.out.printf("Customer Address Recipient: %s, confidence: %.2f%n",
                            customerAddr, customerAddressRecipientField.getConfidence());
                    }
                }

                DocumentField invoiceIdField = invoiceFields.get("InvoiceId");
                if (invoiceIdField != null) {
                    if (DocumentFieldType.STRING == invoiceIdField.getType()) {
                        String invoiceId = invoiceIdField.getValueString();
                        System.out.printf("Invoice ID: %s, confidence: %.2f%n",
                            invoiceId, invoiceIdField.getConfidence());
                    }
                }

                DocumentField invoiceDateField = invoiceFields.get("InvoiceDate");
                if (customerNameField != null) {
                    if (DocumentFieldType.DATE == invoiceDateField.getType()) {
                        LocalDate invoiceDate = invoiceDateField.getValueDate();
                        System.out.printf("Invoice Date: %s, confidence: %.2f%n",
                            invoiceDate, invoiceDateField.getConfidence());
                    }
                }

                DocumentField invoiceTotalField = invoiceFields.get("InvoiceTotal");
                if (customerAddressRecipientField != null) {
                    if (DocumentFieldType.FLOAT == invoiceTotalField.getType()) {
                        Float invoiceTotal = invoiceTotalField.getValueFloat();
                        System.out.printf("Invoice Total: %.2f, confidence: %.2f%n",
                            invoiceTotal, invoiceTotalField.getConfidence());
                    }
                }

                DocumentField invoiceItemsField = invoiceFields.get("Items");
                if (invoiceItemsField != null) {
                    System.out.printf("Invoice Items: %n");
                    if (DocumentFieldType.LIST == invoiceItemsField.getType()) {
                        List < DocumentField > invoiceItems = invoiceItemsField.getValueList();
                        invoiceItems.stream()
                            .filter(invoiceItem - > DocumentFieldType.MAP == invoiceItem.getType())
                            .map(formField - > formField.getValueMap())
                            .forEach(formFieldMap - > formFieldMap.forEach((key, formField) - > {
                                // See a full list of fields found on an invoice here:
                                // https://aka.ms/formrecognizer/invoicefields
                                if ("Description".equals(key)) {
                                    if (DocumentFieldType.STRING == formField.getType()) {
                                        String name = formField.getValueString();
                                        System.out.printf("Description: %s, confidence: %.2fs%n",
                                            name, formField.getConfidence());
                                    }
                                }
                                if ("Quantity".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float quantity = formField.getValueFloat();
                                        System.out.printf("Quantity: %f, confidence: %.2f%n",
                                            quantity, formField.getConfidence());
                                    }
                                }
                                if ("UnitPrice".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float unitPrice = formField.getValueFloat();
                                        System.out.printf("Unit Price: %f, confidence: %.2f%n",
                                            unitPrice, formField.getConfidence());
                                    }
                                }
                                if ("ProductCode".equals(key)) {
                                    if (DocumentFieldType.FLOAT == formField.getType()) {
                                        Float productCode = formField.getValueFloat();
                                        System.out.printf("Product Code: %f, confidence: %.2f%n",
                                            productCode, formField.getConfidence());
                                    }
                                }
                            }));
                    }
                }
            }
        });
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
> [REST API v3.0 リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)

> [!div class="nextstepaction"]
> [Azure Form Recognizer Java ライブラリ リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-formrecognizer/4.0.0-beta.1/index.html)