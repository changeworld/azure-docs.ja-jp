---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.custom: devx-track-java, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: 203d9fed872f2b36bfc6f528b0b7c991a2473981
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017532"
---
[リファレンス ドキュメント](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [パッケージ](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.2.0-beta.1) | [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) バージョン 8 以降
* Azure サブスクリプションを入手したら、Azure portal で<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="言語リソースを作成する"  target="_blank">言語リソースを作成</a>して、キーとエンドポイントを取得します。  デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 分析機能を使うには、Standard (S) 価格レベルの言語リソースが必要です。

## <a name="setting-up"></a>設定

### <a name="add-the-client-library"></a>クライアント ライブラリを追加する

好みの IDE または開発環境で Maven プロジェクトを作成します。 次に、自分のプロジェクトの *pom.xml* ファイルに次の依存関係を追加します。 [その他のビルド ツール](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.2.0-beta.1)の実装構文はオンラインで確認できます。

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.2.0-beta.1</version>
    </dependency>
</dependencies>
```

## <a name="code-example"></a>コードの例

`Example.java` という名前で Java ファイルを作成します。 ファイルを開き、下のコードをコピーします。 `key` 変数はリソースのキーに置き換え、`endpoint` 変数はリソースのエンドポイントに置き換えてください。 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
import java.util.ArrayList;
import java.util.List;
import com.azure.core.util.polling.SyncPoller;
import com.azure.ai.textanalytics.util.*;

public class Example {

    private static String KEY = "replace-with-your-key-here";
    private static String ENDPOINT = "replace-with-your-endpoint-here";

    public static void main(String[] args) {
        TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
        summarizationExample(client);
    }
    // Method to authenticate the client object with your key and endpoint
    static TextAnalyticsClient authenticateClient(String key, String endpoint) {
        return new TextAnalyticsClientBuilder()
                .credential(new AzureKeyCredential(key))
                .endpoint(endpoint)
                .buildClient();
    }
    // Example method for summarizing text
    static void summarizationExample(TextAnalyticsClient client) {
        List<String> documents = new ArrayList<>();
        documents.add(
                "The extractive summarization feature uses natural language processing techniques "
                + "to locate key sentences in an unstructured text document. "
                + "These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. "
                + "They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. "
                + "In the public preview, extractive summarization supports several languages. "
                + "It is based on pretrained multilingual transformer models, part of our quest for holistic representations. "
                + "It draws its strength from transfer learning across monolingual and harness the shared nature of languages "
                + "to produce models of improved quality and efficiency.");
    
        SyncPoller<AnalyzeActionsOperationDetail, AnalyzeActionsResultPagedIterable> syncPoller =
                client.beginAnalyzeActions(documents,
                        new TextAnalyticsActions().setDisplayName("{tasks_display_name}")
                                .setExtractSummaryActions(
                                        new ExtractSummaryAction()),
                        "en",
                        new AnalyzeActionsOptions());
    
        syncPoller.waitForCompletion();
    
        syncPoller.getFinalResult().forEach(actionsResult -> {
            System.out.println("Extractive Summarization action results:");
            for (ExtractSummaryActionResult actionResult : actionsResult.getExtractSummaryResults()) {
                if (!actionResult.isError()) {
                    for (ExtractSummaryResult documentResult : actionResult.getDocumentsResults()) {
                        if (!documentResult.isError()) {
                            System.out.println("\tExtracted summary sentences:");
                            for (SummarySentence summarySentence : documentResult.getSentences()) {
                                System.out.printf(
                                        "\t\t Sentence text: %s, length: %d, offset: %d, rank score: %f.%n",
                                        summarySentence.getText(), summarySentence.getLength(),
                                        summarySentence.getOffset(), summarySentence.getRankScore());
                            }
                        } else {
                            System.out.printf("\tCannot extract summary sentences. Error: %s%n",
                                    documentResult.getError().getMessage());
                        }
                    }
                } else {
                    System.out.printf("\tCannot execute Extractive Summarization action. Error: %s%n",
                            actionResult.getError().getMessage());
                }
            }
        });
    }
}

```

### <a name="output"></a>出力

```console
Extractive Summarization action results:
    Extracted summary sentences:
         Sentence text: The extractive summarization feature uses natural language processing techniques to locate key sentences in an unstructured text document., length: 156, offset: 0, rank score: 0.980000.
         Sentence text: This feature is provided as an API for developers., length: 50, offset: 224, rank score: 0.990000.
         Sentence text: They can use it to build intelligent solutions based on the relevant information extracted to support various use cases., length: 120, offset: 275, rank score: 1.000000.
```
