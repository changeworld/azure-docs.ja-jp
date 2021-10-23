---
title: Microsoft Teams チャネルに通知を送信する方法
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics パイプラインから Microsoft Teams チャネルに通知を送信する方法について説明します
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.custom: synapse
ms.topic: how-to
ms.subservice: tutorials
ms.date: 09/29/2021
ms.openlocfilehash: 98fa799cc5fd933d21fb0130093381c628f8e23c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660953"
---
# <a name="send-notifications-to-a-microsoft-teams-channel-from-an-azure-data-factory-or-synapse-analytics-pipeline"></a>Azure Data Factory または Synapse Analytics パイプラインから Microsoft Teams チャネルに通知を送信する

多くの場合、パイプラインの実行中または実行後に通知を送信する必要があります。 通知では、予防的アラートが提供され、問題を検出するための事後対応型の監視の必要性が軽減されます。  データ ファクトリまたは Synapse パイプラインで呼び出すことができる[ロジック アプリを使用して電子メール通知を送信する方法](tutorial-control-flow-portal.md#create-email-workflow-endpoints)について説明します。  多くの企業では、コラボレーションのために Microsoft Teams を使用することもますます増えています。  この記事では、パイプライン アラートから Microsoft Teams への通知を構成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

パイプラインから Teams に通知を送信する前に、Teams チャネルの[受信 Webhook](/microsoftteams/platform/webhooks-and-connectors/how-to/connectors-using) を作成する必要があります。 この目的で新しい Teams チャネルを作成する必要がある場合は、[Teams のドキュメント](https://support.microsoft.com/office/create-a-channel-in-teams-fda0b75e-5b90-4fb8-8857-7e102b014525)を参照してください。  

1.  Microsoft Teams を開き、[アプリ] タブにアクセスします。"受信 Webhook" を検索し、[受信 Webhook] コネクタを選択します。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-incoming-webhook-connector.png" alt-text="Teams の [アプリ] タブの 受信 Webhook アプリを示しています。":::

1.  通知を送信する Teams サイトにコネクタを追加します。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-connector-to-site.png" alt-text="受信 Webhook アプリの &quot;[チームに追加]&quot; ボタンが強調表示されています。":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-prod-notifications.png" alt-text="Teams の 受信 Webhook アプリの [構成] ダイアログの、チーム選択のプロンプトを示しています。":::

1.  必要に応じて Webhook に名前を付け、必要に応じて、メッセージを識別するためのアイコンをアップロードします。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-icon.png" alt-text="受信 Webhook のオプションのページの名前プロパティ、オプションのイメージのアップロード、および &quot;[作成]&quot; ボタンが強調表示されています。":::  

1.  後で ADF で使用できるように、作成時に生成された Webhook URL のストアをコピーします。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-copy-webhook-url.png" alt-text="受信 Webhook のオプションのページの、作成後の新しい Webhook URL を示しています。":::

1.  Webhook コネクタを追加するチャネルで通知を確認できます。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-channel-notification.png" alt-text="Webhook コネクタを追加した Teams チャネルの通知を示しています。":::
        
## <a name="steps-to-send-notifications-on-teams-channel-from-a-pipeline"></a>パイプラインから Teams チャネルで通知を送信する手順:

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

1.  新しい "**テンプレートからのパイプライン**" を作成します。 テンプレート ギャラリーには、Teams 通知を簡単に開始できるようにするためのパイプライン テンプレートが用意されています。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template.png" alt-text="Azure Data Factory Studio の &quot;[テンプレートからのパイプライン]&quot; メニューを示しています。":::

1.  "teams" を検索し、 **[Microsoft Teams でチャネルに通知を送信する]** テンプレートを選択して使用します。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog.png" alt-text="テンプレート ギャラリーの &quot;[Microsoft Teams でチャネルに通知を送信する]&quot; テンプレートを示しています。":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template.png" alt-text="テンプレート ギャラリーで選択された後の、&quot;[Microsoft Teams でチャネルに通知を送信する]&quot; テンプレートの詳細を示しています。":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="&quot;[Microsoft Teams でチャネルに通知を送信する]&quot; テンプレートによって作成されたパイプラインのプロパティを示しています。":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

1.  新しい "**テンプレートからのパイプライン**" を作成します。 テンプレート ギャラリーには、Teams 通知を簡単に開始できるようにするためのパイプライン テンプレートが用意されています。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template-synapse.png" alt-text="Azure Data Factory Studio の &quot;[テンプレートからのパイプライン]&quot; メニューを示しています。":::

1.  "teams" を検索し、 **[Microsoft Teams でチャネルに通知を送信する]** テンプレートを選択して使用します。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog-synapse.png" alt-text="テンプレート ギャラリーの &quot;[Microsoft Teams でチャネルに通知を送信する]&quot; テンプレートを示しています。":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template-synapse.png" alt-text="テンプレート ギャラリーで選択された後の、&quot;[Microsoft Teams でチャネルに通知を送信する]&quot; テンプレートの詳細を示しています。":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="&quot;[Microsoft Teams でチャネルに通知を送信する]&quot; テンプレートによって作成されたパイプラインのプロパティを示しています。":::

---

3.  関連するパラメーターの既定値については、現在の Data Factory の "**サブスクリプション ID**"、"**リソース グループ**"、**Teams Webhook URL**" ([前提条件](#prerequisites)を参照) を追加することをお勧めします。
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/webhook-recommended-properties.png" alt-text="&quot;[Microsoft Teams でチャネルに通知を送信する]&quot; テンプレートによって作成されたパイプラインの推奨されるプロパティを示しています。":::

    これらのパラメーターは、監視 URL を作成するために使用されます。 (パイプラインが属しているのと同じデータ ファクトリの) 有効なサブスクリプションとリソース グループを指定していないとします。 この場合、通知には有効なパイプライン監視 URL が含まれませんが、メッセージは引き続き機能します。  さらに、これらのパラメーターを追加することで、これらの値を別のパイプラインから常に渡す必要がなくなります。 これらの値をメタデータ駆動型の方法で制御する場合は、それに応じて変更する必要があります。
    
1.  Teams チャネルで通知を送信するパイプラインに **Execute Pipeline** アクティビティを追加します。 **Execute Pipeline** アクティビティの **[呼び出されたパイプライン]** として、 **[Microsoft Teams でチャネルに通知を送信する]** テンプレートから生成されたパイプラインを選択します。

     :::image type="content" source="media/how-to-send-notifications-to-teams/execute-pipeline-activity.png" alt-text="&quot;[Microsoft Teams でチャネルに通知を送信する]&quot; テンプレートによって作成されたパイプラインでの &quot;Execute pipeline&quot; アクティビティを示しています。":::

1.  アクティビティの種類に基づいて、必要に応じてパラメーターをカスタマイズします。

    :::image type="content" source="media/how-to-send-notifications-to-teams/customize-parameters-by-activity-type.png" alt-text="&quot;[Microsoft Teams でチャネルに通知を送信する]&quot; テンプレートによって作成されたパイプラインのパラメーターのカスタマイズを示しています。":::   
  
1.  Teams で通知を受け取ります。

    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-notifications-view-pipeline-run.png" alt-text="Teams チャネルのパイプライン通知を示しています。":::
## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>システム変数と式を使用して動的メッセージを追加する

[システム変数](control-flow-system-variables.md)と[式](control-flow-expression-language-functions.md)を使用して、メッセージを動的にすることができます。 次に例を示します。  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

上記の式は、エラーから関連するエラー メッセージを返します。これは、Teams チャネルで通知として送信できます。 詳細については、[コピー アクティビティの出力のプロパティ](copy-activity-monitoring.md)に関する記事を参照してください。

また、Microsoft Teams でサポートされている[通知ペイロードのスキーマ](https://adaptivecards.io/explorer/AdaptiveCard.html)を確認し、必要に応じて上記のテンプレートをカスタマイズすることもお勧めします。

## <a name="next-steps"></a>次の手順

[パイプラインからメールを送信する方法](how-to-send-email.md)