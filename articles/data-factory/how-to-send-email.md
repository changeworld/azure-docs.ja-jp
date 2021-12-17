---
title: 電子メールを送信する方法
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Azure Synapse パイプラインを使用して電子メールを送信する方法について説明します。
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: 64f7fcf5cfd5c24cb8d34e29572f7fbb1314414a
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668905"
---
# <a name="send-an-email-with-an-azure-data-factory-or-azure-synapse-pipeline"></a>Azure Data Factory または Azure Synapse パイプラインを使用して電子メールを送信する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

多くの場合、パイプラインの実行中または実行後に通知を送信する必要があります。 通知では、予防的アラートが提供され、問題を検出するための事後対応型の監視の必要性が軽減されます。  この記事では、Azure Data Factory またはAzure Synapse パイプラインからの電子メール通知を構成する方法を説明します。 

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
- **ロジック アプリ**。 パイプラインから電子メール送信をトリガーするには、[Logic Apps](../logic-apps/logic-apps-overview.md) を使用してワークフローを定義します。 ロジック アプリ ワークフローの作成の詳細については、[ロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

## <a name="create-the-email-workflow-in-your-logic-app"></a>ロジック アプリで電子メール ワークフローを作成する

`SendEmailFromPipeline` という名前のロジック アプリ ワークフローを作成します。 ワークフロー トリガーを `When an HTTP request is received` として定義し、`Office 365 Outlook – Send an email (V2)` のアクションを追加します。

:::image type="content" source="media/how-to-send-email/logic-app-workflow-designer.png" alt-text="HTTP 要求トリガーからの [メールの送信 (V2)] アクションを含むロジック アプリ ワークフロー デザイナーを示しています。":::

HTTP 要求トリガーでは、`Request Body JSON Schema` にこちらの JSON を指定します。

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

ロジック アプリ デザイナーの HTTP 要求はこのようになります。

:::image type="content" source="media/how-to-send-email/logic-app-http-request-trigger.png" alt-text="[要求本文の JSON スキーマ] フィールドが設定された HTTP 要求トリガーのロジック アプリ ワークフロー デザイナーを示しています。":::

**[メールの送信 (V2)]** アクションでは、要求本文の JSON スキーマのプロパティを使用して、電子メールの書式設定方法をカスタマイズします。

:::image type="content" source="media/how-to-send-email/logic-app-email-action.png" alt-text="[メールの送信 (V2)] アクションのロジック アプリ ワークフロー デザイナーを示しています。":::

ワークフローを保存します。 次に、新しいワークフローの [ワークフロー URL] をメモします。

:::image type="content" source="media/how-to-send-email/logic-app-workflow-url.png" alt-text="ワークフローの URL が強調表示された ロジック アプリ ワークフローの [概要] タブを示しています。":::

## <a name="create-a-pipeline-to-trigger-your-logic-app-email-workflow"></a>ロジック アプリの電子メール ワークフローをトリガーするパイプラインを作成する

電子メールを送信するロジック アプリ ワークフローを作成したら、**Web** アクティビティを使用してパイプラインからそれをトリガーできます。  

1. 新しいパイプラインを作成し、 **[全般]** カテゴリの下にある **[Web]** アクティビティを見つけて編集キャンバスにドラッグします。

1. 新しい **[Web1]** アクティビティを選択し、 **[設定]** タブを選択します。

   前に作成したロジック アプリ ワークフローの URL を **[URL]** フィールドに指定します。

   **[本文]** に次の JSON を指定します。
    ```json
       {
        "message" : "This is a custom dynamic message from your pipeline with run ID @{pipeline().RunId}.",
        "dataFactoryName" : "@{pipeline().DataFactory}", 
        "pipelineName" : "@{pipeline().Pipeline}", 
        "receiver" : "@{pipeline().parameters.receiver}"
       }
    ```
    
    動的式を使用して、パイプライン内のイベントで役立つメッセージを生成します。  こちらの JSON 形式はロジック アプリで定義した JSON 形式と一致しており、必要に応じてこれらをカスタマイズできることも知っておいてください。
    
    :::image type="content" source="media/how-to-send-email/pipeline-with-web-activity-calling-logic-app.png" alt-text="ロジック アプリ ワークフローの URL と JSON メッセージ本文を使用して構成された Web アクティビティを含むパイプラインを示しています。":::

1. パイプライン デザイナーの背景領域を選択してパイプラインのプロパティ ページを選択し、receiver という新しいパラメーターを追加して、電子メール アドレスを [既定値] として指定します。
   
   この例では、任意に定義したパイプライン パラメーターから受信者の電子メールを指定します。  受信者の値は、任意の式から、またはリンクされたデータ ソースからも取得できます。

   :::image type="content" source="media/how-to-send-email/pipeline-receiver-email-parameter.png" alt-text="パイプライン デザイナーの receiver パラメーターの構成を示しています。":::

1. パイプラインを公開してから手動でトリガーし、電子メールが想定どおり送信されることを確認します。

   :::image type="content" source="media/how-to-send-email/pipeline-manually-trigger.png" alt-text="パイプラインを手動でトリガーする方法を示しています。"::: 

## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>システム変数と式を使用して動的メッセージを追加する

[システム変数](control-flow-system-variables.md)と[式](control-flow-expression-language-functions.md)を使用して、メッセージを動的にすることができます。 次に例を示します。  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

上記の式では、エラーが発生したコピー アクティビティから関連するエラー メッセージが返されます。これを、電子メールを送信する Web アクティビティにリダイレクトすることができます。 詳細については、[コピー アクティビティの出力のプロパティ](copy-activity-monitoring.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

[パイプラインから Teams 通知を送信する方法](how-to-send-notifications-to-teams.md)
