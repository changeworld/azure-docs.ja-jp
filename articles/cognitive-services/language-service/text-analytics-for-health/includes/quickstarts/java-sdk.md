---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.custom: devx-track-java, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: e71fc8573798c96676edfe9b172a8e419d60330a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090079"
---
[リファレンス ドキュメント](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-stable) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [パッケージ](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) | [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) バージョン 8 以降
* Azure サブスクリプションを入手したら、Azure portal で<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="言語リソースを作成する"  target="_blank">言語リソースを作成</a>して、キーとエンドポイントを取得します。  デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 分析機能を使うには、Standard (S) 価格レベルの言語リソースが必要です。

## <a name="setting-up"></a>設定

### <a name="add-the-client-library"></a>クライアント ライブラリを追加する

好みの IDE または開発環境で Maven プロジェクトを作成します。 次に、自分のプロジェクトの *pom.xml* ファイルに次の依存関係を追加します。 [その他のビルド ツール](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0)の実装構文はオンラインで確認できます。

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0</version>
    </dependency>
</dependencies>
```

## <a name="code-example"></a>コードの例

`EntityLinking.java` という名前で Java ファイルを作成します。 ファイルを開き、下のコードをコピーします。 `key` 変数はリソースのキーに置き換え、`endpoint` 変数はリソースのエンドポイントに置き換えてください。 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;

import java.util.List;
import java.util.Arrays;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;

import com.azure.ai.textanalytics.util.*;


public class Example {

    private static String KEY = "replace-with-your-key-here";
    private static String ENDPOINT = "replace-with-your-endpoint-here";

    public static void main(String[] args) {
        TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
        healthExample(client);
    }

    // Method to authenticate the client object with your key and endpoint
    static TextAnalyticsClient authenticateClient(String key, String endpoint) {
        return new TextAnalyticsClientBuilder()
                .credential(new AzureKeyCredential(key))
                .endpoint(endpoint)
                .buildClient();
    }

    // Example method for extracting information from healthcare-related text 
    static void healthExample(TextAnalyticsClient client){
        List<TextDocumentInput> documents = Arrays.asList(
                new TextDocumentInput("0",
                        "Prescribed 100mg ibuprofen, taken twice daily."));

        AnalyzeHealthcareEntitiesOptions options = new AnalyzeHealthcareEntitiesOptions().setIncludeStatistics(true);

        SyncPoller<AnalyzeHealthcareEntitiesOperationDetail, AnalyzeHealthcareEntitiesPagedIterable>
                syncPoller = client.beginAnalyzeHealthcareEntities(documents, options, Context.NONE);

        System.out.printf("Poller status: %s.%n", syncPoller.poll().getStatus());
        syncPoller.waitForCompletion();

        // Task operation statistics
        AnalyzeHealthcareEntitiesOperationDetail operationResult = syncPoller.poll().getValue();
        System.out.printf("Operation created time: %s, expiration time: %s.%n",
                operationResult.getCreatedAt(), operationResult.getExpiresAt());
        System.out.printf("Poller status: %s.%n", syncPoller.poll().getStatus());

        for (AnalyzeHealthcareEntitiesResultCollection resultCollection : syncPoller.getFinalResult()) {
            // Model version
            System.out.printf(
                    "Results of Azure Text Analytics for health entities\" Model, version: %s%n",
                    resultCollection.getModelVersion());

            for (AnalyzeHealthcareEntitiesResult healthcareEntitiesResult : resultCollection) {
                System.out.println("Document ID = " + healthcareEntitiesResult.getId());
                System.out.println("Document entities: ");
                // Recognized healthcare entities
                for (HealthcareEntity entity : healthcareEntitiesResult.getEntities()) {
                    System.out.printf(
                            "\tText: %s, normalized name: %s, category: %s, subcategory: %s, confidence score: %f.%n",
                            entity.getText(), entity.getNormalizedText(), entity.getCategory(),
                            entity.getSubcategory(), entity.getConfidenceScore());
                }
                // Recognized healthcare entity relation groups
                for (HealthcareEntityRelation entityRelation : healthcareEntitiesResult.getEntityRelations()) {
                    System.out.printf("Relation type: %s.%n", entityRelation.getRelationType());
                    for (HealthcareEntityRelationRole role : entityRelation.getRoles()) {
                        HealthcareEntity entity = role.getEntity();
                        System.out.printf("\tEntity text: %s, category: %s, role: %s.%n",
                                entity.getText(), entity.getCategory(), role.getName());
                    }
                }
            }
        }
    }
}

```

### <a name="output"></a>出力

```console
Poller status: IN_PROGRESS.
Operation created time: 2021-08-24T16:05:15Z, expiration time: 2021-08-25T16:05:15Z.
Poller status: SUCCESSFULLY_COMPLETED.
Results of Azure Text Analytics for health entities Model, version: 2021-05-15
Document ID = 0
Document entities: 
    Text: 100mg, normalized name: null, category: Dosage, subcategory: null, confidence score: 1.000000.
    Text: ibuprofen, normalized name: ibuprofen, category: MedicationName, subcategory: null, confidence score: 1.000000.
    Text: twice daily, normalized name: null, category: Frequency, subcategory: null, confidence score: 1.000000.
Relation type: DosageOfMedication.
    Entity text: 100mg, category: Dosage, role: Dosage.
    Entity text: ibuprofen, category: MedicationName, role: Medication.
Relation type: FrequencyOfMedication.
    Entity text: ibuprofen, category: MedicationName, role: Medication.
    Entity text: twice daily, category: Frequency, role: Frequency.
```
