---
title: 'クイックスタート: Python 用の質問応答クライアント ライブラリ'
description: このクイックスタートでは、Python 用の質問応答クライアント ライブラリの使用を開始する方法について説明します。
ms.topic: include
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/11/2021
ms.openlocfilehash: 0aa0f65e9a277cbcb28870b970ead3e357057b2f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353921"
---
このクイックスタートは、以下を行う Python 用の質問応答クライアント ライブラリに使用します。

* ナレッジ ベースから回答を取得する。
* 質問と共に送信したテキストの本文から回答を取得する。
* 質問に対する回答の信頼度スコアを取得する。

[API リファレンス ドキュメント][questionanswering_refdocs] | [ソース コード][questionanswering_client_src] | [パッケージ (PyPI)][questionanswering_pypi_package] | [Python サンプル][questionanswering_samples] |

[questionanswering_client_class]: https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient
[questionanswering_client_src]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-questionanswering/
[questionanswering_pypi_package]: https://pypi.org/project/azure-ai-language-questionanswering/
[questionanswering_refdocs]: https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html
[questionanswering_samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-questionanswering/samples/README.md

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* 質問応答の API キーとエンドポイントを生成するには、カスタム質問応答機能を有効にした[言語リソース](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)が必要です。
    * 言語リソースがデプロイされたら、 **[リソースに移動]** を選びます。 API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
* クエリを実行する既存のナレッジ ベース。 ナレッジ ベースをまだ設定していない場合は、[**Language Studio のクイックスタート**](../quickstart/sdk.md)に関するページに記載されている手順に従ってください。 または、この [Surface ユーザー ガイド URL](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf) をデータ ソース として使用するナレッジ ベースを追加します。

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

```console
pip install azure-ai-language-questionanswering
```

## <a name="query-a-knowledge-base"></a>ナレッジ ベースにクエリを実行する

### <a name="generate-an-answer-from-a-knowledge-base"></a>ナレッジ ベースから回答を生成する

以下の例では、[get_answers](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers) を使ってナレッジ ベースに対してクエリを実行し、質問に対する回答を得ることができます。 このコードは、専用の .py ファイルや [Jupyter Notebook または Lab](https://jupyter.org/) のセルにコピーすることができます。

以下の変数を実際の値に変更して、以下のコードを更新する必要があります。

|変数名 | 値 |
|--------------------------|-------------|
| `endpoint`               | この値は、Azure portal からリソースを確認する際に、 **[Keys & Endpoint]\(キーとエンドポイント\)** セクションで確認することができます。 または、 **[Language Studio]**  >  **[質問応答]**  >  **[ナレッジ ベースをデプロイする]**  >  **[Get prediction URL]\(予測 URL の取得\)** で値を見つけることができます。 エンドポイントの例: `https://southcentralus.api.cognitive.microsoft.com/`|
| `credential` | この値は、Azure portal からリソースを確認する際に、 **[Keys & Endpoint]\(キーとエンドポイント\)** セクションで確認することができます。 Key1 または Key2 を使用できます。 常に 2 つの有効なキーを持つことで、ダウンタイムなしで安全にキーのローテーションを行うことができます。 または、 **[Language Studio]**  >  **[質問応答]**  >  **[ナレッジ ベースをデプロイする]**  >  **[Get prediction URL]\(予測 URL の取得\)** で値を見つけることができます。 キー値は、サンプル要求の一部です。|
| `knowledge_base_project` | 質問応答プロジェクトの名前。|
| `deployment`             | 使用可能な値は `test` および `production` です。 `production` は、 **[Language Studio]**  >  **[質問応答]**  >  **[ナレッジ ベースをデプロイする]** からナレッジ ベースをデプロイしていることに依存します。|

```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.language.questionanswering import QuestionAnsweringClient

endpoint = "https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/"
credential = AzureKeyCredential("{YOUR-LANGUAGE-RESOURCE-KEY}")
knowledge_base_project = "{YOUR-PROJECT-NAME}"
deployment = "production"

def main():
    client = QuestionAnsweringClient(endpoint, credential)
    with client:
        question="How much battery life do I have left?"
        output = client.get_answers(
            question = question,
            project_name=knowledge_base_project,
            deployment_name=deployment
        )
    print("Q: {}".format(question))
    print("A: {}".format(output.answers[0].answer))

if __name__ == '__main__':
    main()
```

この例では変数をハード コーディングしています。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](../../../../key-vault/general/overview.md) で安全なキー記憶域を確保できます。

上記のコードを実行すると、前提条件のデータ ソースを使用している場合は、次のような回答が表示されます。

```
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
```

この回答の精度に関する質問応答による信頼度については、既存の print ステートメントの下に print ステートメントを追加します。

```python
print("Q: {}".format(question))
print("A: {}".format(output.answers[0].answer))
print("Confidence Score: {}".format(output.answers[0].confidence_score)) # add this line 
```

これで、信頼度スコアを含む結果が返されます。

```
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
Confidence Score: 0.9185
```

信頼度スコアは、0 から 1 の値で返されます。 これはパーセンテージのように考えることができるため、100 を掛けます。つまり、信頼度スコアが 0.9185 の場合、これがナレッジ ベースに基づいた、質問に対する正しい答えであることを質問応答は 91.85% 確信していることを意味します。

信頼度スコアが特定のしきい値を下回る回答を除外したい場合は、[AnswerOptions](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.models.html#azure.ai.language.questionanswering.models.AnswersOptions) を変更して `confidence_threshold` パラメーターを追加します。

```python
        output = client.get_answers(
            confidence_threshold = 0.95, #add this line
            question = question,
            project_name=knowledge_base_project,
            deployment_name=deployment
        )
```

前回のコードの実行から、信頼度スコアが `.9185` であることが分かっているため、しきい値を `.95` に設定すると、[既定の回答](../how-to/change-default-answer.md)が返されます。

```
Q: How much battery life do I have left?
A: No good match found in KB
Confidence Score: 0.0
```

## <a name="query-text-without-a-knowledge-base"></a>ナレッジ ベースを使用せずにテキストに対してクエリを実行する

[get_answers_from_text](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers_from_text) を使用することで、ナレッジ ベースなしで質問応答を使用することもできます。 この場合、質問を送信する際に、質問と、回答の検索先となる関連テキスト レコードの両方を質問応答に提供します。

この例の場合は、`endpoint` と `credential` の変数だけを変更する必要があります。

```python
import os
from azure.core.credentials import AzureKeyCredential
from azure.ai.language.questionanswering import QuestionAnsweringClient
from azure.ai.language.questionanswering import models as qna

endpoint = "https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/"
credential = AzureKeyCredential("YOUR-LANGUAGE-RESOURCE-KEY")

def main():
    client = QuestionAnsweringClient(endpoint, credential)
    with client:
        question="How long does it takes to charge a surface?"
        input = qna.AnswersFromTextOptions(
            question=question,
            text_documents=[
                "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
                "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.",
                "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
                "The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface.",
            ]
        )


        output = client.get_answers_from_text(input)

    best_answer = [a for a in output.answers if a.confidence > 0.9][0]
    print(u"Q: {}".format(input.question))
    print(u"A: {}".format(best_answer.answer))
    print("Confidence Score: {}".format(output.answers[0].confidence))

if __name__ == '__main__':
    main()
```

このコードは、専用の .py ファイルや [Jupyter Notebook または Lab](https://jupyter.org/) の新しいセルにコピーすることができます。 この例では、次のような結果が返されます。

```
Q: How long does it takes to charge surface?
A: Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.
Confidence Score: 0.9254655838012695
```

今回は、すべての回答を繰り返し処理し、最大の信頼性スコアが 0.9 より大きい回答のみを返します。 [get_answers_from_text](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers_from_text) で使用できるオプションについて詳しく理解するには、[AnswersFromTextOptions パラメーター](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.models.html#azure.ai.language.questionanswering.models.AnswersFromTextOptions)に関するページを参照してください。