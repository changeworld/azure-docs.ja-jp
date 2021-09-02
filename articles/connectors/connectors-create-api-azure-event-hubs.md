---
title: Azure Event Hubs に接続する
description: イベント ハブに接続し、Azure Logic Apps のワークフローにトリガーまたはアクションを追加します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/16/2021
tags: connectors
ms.openlocfilehash: 079d131cac55c6d7a54547a3720546ab6422f7d5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473078"
---
# <a name="connect-to-an-event-hub-from-workflows-in-azure-logic-apps"></a>Azure Logic Apps のワークフローからイベント ハブに接続する

Azure Event Hubs コネクタを使用すると、ロジック アプリ ワークフローを Azure のイベント ハブに接続できます。 その後、ワークフローでイベント ハブに送信されるイベントを監視および管理することができます。 たとえば、ワークフローは、イベント ハブからイベントを確認、送信、受信できます。 この記事では、イベント ハブに接続し、イベント ハブ トリガーまたはアクションをワークフローに追加する方法を示して、Azure Event Hubs コネクタを使用する開始手順について説明します。

Azure Event Hubs または Azure Logic Apps の詳細については、「[Azure Event Hubs とは](../event-hubs/event-hubs-about.md)」または「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="available-operations"></a>使用可能な操作

すべての操作と、プロパティ、制限など、その他の技術情報については、[Event Hubs コネクタの参照ページ](/connectors/eventhubs/)を参照してください。

> [!NOTE]
> [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) でホストされるロジック アプリの場合、このコネクタの ISE バージョンでは、代わりに [ISE メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)が使用されます。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* [Event Hubs の名前空間とイベント ハブ](../event-hubs/event-hubs-create.md)

* イベント ハブにアクセスするロジック アプリ ワークフロー

  Event Hubs トリガーでワークフローを開始するには、空のワークフローが必要です。 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) に慣れていない場合は、この[クイックスタートを試して、ロジック アプリ ワークフローの例を作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)してください。

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>アクセス許可を確認し、接続文字列を取得する

ワークフローでイベント ハブにアクセスできるようにするには、アクセス許可を確認してから、イベント ハブの名前空間の接続文字列を取得します。

1. [Azure portal](https://portal.azure.com) で、特定のイベント ハブではなく、Event Hubs の *名前空間* に移動します。

1. 名前空間メニューで **[設定]** の **[共有アクセス ポリシー]** を選択します。 **[要求]** 列で、少なくともその名前空間の **[管理]** アクセス許可が付与されていることを確認します。

   ![Azure portal、Event Hubs 名前空間、[要求] 列に表示される [管理] アクセス許可を示すスクリーンショット。](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. 後で手動で接続情報を入力する場合は、イベント ハブの名前空間の接続文字列を取得します。

   1. **[ポリシー]** 列で **[RootManageSharedAccessKey]** を選択します。

   1. ご自身の主キーの接続文字列を検索します。 後で使用できるように接続文字列をコピーして保存します。

      ![[コピー] ボタンが選択された状態で主キーの接続文字列を示したスクリーンショット。](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > 接続文字列が Event Hubs 名前空間に関連付けられているか、特定のイベント ハブに関連付けられているかを確認するには、接続文字列に `EntityPath` パラメーターがないかどうかを確認します。 このパラメーターがある場合、接続文字列は特定の Event Hubs "エンティティ" を対象としており、ワークフロー アプリで使用する正しい文字列ではありません。

<a name="create-connection"></a>

## <a name="create-an-event-hub-connection"></a>イベント ハブ接続を作成する

Event Hubs トリガーまたはアクションを初めて追加すると、イベント ハブへの接続を作成するように求められます。

1. 求められたら、次のいずれかのオプションを選択します。

   * 次の接続情報を入力します。

     | プロパティ | 必須 | 値 | 説明 |
     |----------|----------|-------|-------------|
     | **Connection Name** | はい | <*connection-name*> | 作成する接続の名前 |
     | **イベント ハブの名前空間** | Yes | <*event-hubs-namespace*> | 使用する Event Hubs 名前空間を選択します。 |
     |||||

   * 以前に保存した接続文字列を手動で入力する場合は、 **[接続情報を手動で入力する]** を選択します。 [接続文字列を検索する方法](#permissions-connection-string)に関するセクションを参照してください。

1. 使用する Event Hubs ポリシーを選択してから (選択されていない場合)、 **[作成]** を選択します。

   ![入力された接続情報を示し、[作成] が選択されたスクリーンショット。](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

1. 接続を作成したら、[Event Hubs トリガーを追加](#add-trigger)するか、[Event Hubs アクションを追加](#add-action)します。

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Event Hubs トリガーを追加します。

Azure Logic Apps では、すべてのワークフローは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から開始します。トリガーは、特定の条件が満たされたときに起動されます。 トリガーが起動するたび、Logic Apps サービスによってワークフロー インスタンスが作成され、ワークフローの手順の実行が開始されます。

次の手順では、たとえば **イベント ハブでイベントを使用できるとき** など、トリガーを追加する一般的な方法について説明します。 この例では、イベント ハブ内に新しいイベントがあるかを確認し、新しいイベントが存在するときにワークフローの実行を開始するトリガーを追加する方法を示します。

1. Logic Apps デザイナーで、空のロジック アプリ ワークフローがまだ開いていない場合にこれを開きます。

1. [操作の選択] 検索ボックスに「`event hubs`」と入力します。 トリガーの一覧から、 **[イベント ハブでイベントを使用できるとき]** という名前のトリガーを選択します。

   ![トリガーの選択](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. イベント ハブへの接続を作成するように求められたら、[要求された接続情報を入力](#create-connection)します。

1. トリガーには、監視するイベント ハブの次のような情報を提供します。

   | プロパティ | 必須 | Description |
   |----------|----------|-------------|
   | **イベント ハブ名** | Yes | 監視するイベント ハブの名前 |
   | **Content type** | No | イベントのコンテンツの種類 既定では、 `application/octet-stream`です。 |
   | **コンシューマー グループ名** | No | イベントを読み取るために使用する [Event Hubs のコンシューマー グループ名](../event-hubs/event-hubs-features.md#consumer-groups)。 指定されていない場合は、既定のコンシューマー グループが使用されます。 |
   | **最大イベント数** | No | 最大イベント数。 トリガーは、1 からこのプロパティによって指定されたイベントの数までの値を返します。 |
   | **間隔** | はい | ワークフローの実行間隔を、[頻度] に指定された単位に基づいて表す正の整数。 |
   | **頻度** | はい | 繰り返しの時間の単位 |
   ||||

   その他のプロパティについては、[**Add new parameter**] (新しいパラメーターを追加) 一覧を開いてください。 パラメーターを選択すると、そのプロパティがトリガーに追加されます。次に例を示します。

   ![トリガーのプロパティ](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   **その他のプロパティ**

   | プロパティ | 必須 | Description |
   |----------|----------|-------------|
   | **コンテンツ スキーマ** | No | イベント ハブから読み取るイベントの JSON コンテンツ スキーマ。 たとえば、コンテンツのスキーマを指定する場合は、スキーマと一致するイベントのみのワークフローをトリガーできます。 |
   | **最小パーティション キー** | No | 読み取る最小の[パーティション](../event-hubs/event-hubs-features.md#partitions) ID を入力します。 既定では、すべてのパーティションが読み取られます。 |
   | **最大パーティション キー** | No | 読み取る最大の[パーティション](../event-hubs/event-hubs-features.md#partitions) ID を入力します。 既定では、すべてのパーティションが読み取られます。 |
   | **タイム ゾーン** | いいえ | 開始時刻を指定したときに限り適用されます。このトリガーに UTC オフセットを指定することはできないためです。 適用するタイム ゾーンを選択してください。 <p>詳細については、「[定期的に実行されるタスクとワークフローを Azure Logic Apps で作成、実行する](../connectors/connectors-native-recurrence.md)」を参照してください。 |
   | **[開始時刻]** | いいえ | 開始時刻を次の形式で指定します。 <p>YYYY-MM-DDThh:mm:ss (タイム ゾーンを選択した場合)<p>または<p>YYYY-MM-DDThh:mm:ssZ (タイム ゾーンを選択しなかった場合)<p>詳細については、「[定期的に実行されるタスクとワークフローを Azure Logic Apps で作成、実行する](../connectors/connectors-native-recurrence.md)」を参照してください。 |
   ||||

1. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

1. 続いて、トリガー出力を使用して他のタスクを実行できるように、1 つ以上のアクションを追加します。

   たとえば、カテゴリなどの特定の値に基づいてイベントをフィルター処理するには、**イベントの送信** アクションで条件を満たすイベントだけが送信されるように条件を追加できます。 

## <a name="trigger-polling-behavior"></a>ポーリングの動作をトリガーする

Event Hubs トリガーはすべて、長いポーリングのトリガーです。 この動作は、トリガーが起動されると、そのトリガーはすべてのイベントを処理し、イベント ハブに追加のイベントが表示されるまで 30 秒待つことを示します。 設計上、30 秒以内にイベントが表示されない場合、そのトリガーはスキップされます。 そうでない場合、トリガーはご自身のイベント ハブが空になるまでイベントの読み取りを続けます。 次のトリガーのポーリングは、トリガーのプロパティで設定する繰り返し間隔に基づいて実行されます。

たとえば、4 つのパーティションでトリガーを設定している場合、トリガーがすべてのパーティションのポーリングを完了するまで、この遅延で最大 2 分とられます。 この遅延内でイベントを受信しなかった場合、トリガーの実行はスキップされます。 そうでない場合、トリガーはご自身のイベント ハブが空になるまでイベントの読み取りを続けます。 次のトリガーのポーリングは、トリガーのプロパティで指定する繰り返し間隔に基づいて発生します。

メッセージが表示される特定のパーティションがわかっている場合は、トリガーの最大と最小のパーティション キーを設定することによって、これらのパーティションからのみイベントを読み取るようにトリガーを更新できます。 詳細については、「[Event Hubs トリガーを追加する](#add-trigger)」のセクションを確認してください。
     
## <a name="trigger-checkpoint-behavior"></a>チェックポイントの動作をトリガーする

Event Hubs トリガーがイベント ハブ内の各パーティションからイベントを読み取るときに、トリガーは独自の状態を使用して、ストリーム オフセット (パーティション内のイベント位置) と、トリガーによりイベントが読み取られるパーティションに関する情報を保持します。

ワークフローを実行するごとに、トリガーは、トリガーの状態によって保持されるストリーム オフセットから起動して、パーティションからイベントを読み取ります。 トリガーは、ラウンドロビン方式で、イベント ハブ内の各パーティションを反復処理し、後続のトリガーの実行でイベントを読み取ります。 1 回の実行で、一度に 1 つのパーティションからイベントを取得します。

トリガーでは、ストレージでこのチェックポイント機能を使用しないので、追加コストは発生しません。 ただし、Event Hubs トリガーを更新するとトリガーの状態がリセットされ、これによってトリガーがストリームの先頭のイベントを読み取ることがあります。

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>イベント ハブ アクションを追加する

Azure Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)はトリガーまたは別のアクションに従い、ワークフローで何らかの操作を実行します。 次の手順では、**送信イベント** などのアクションを追加する一般的な方法について説明します。 この例では、ワークフローは、イベント ハブで新しいイベントをチェックする Event Hubs トリガーを使用して起動されます。

1. Logic Apps デザイナーで、ロジック アプリ ワークフローがまだ開いていない場合にこれを開きます。

1. トリガーまたは別のアクションで、新しいステップを追加します。

   既存のステップの間にステップを追加するには、矢印の上にマウスを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. [操作の選択] 検索ボックスに「`event hubs`」と入力します。 [アクション] 一覧で、 **[送信イベント]** という名前のアクションを選択します。

   ![[Send event] (イベントの送信) アクションを選択します。](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. イベント ハブへの接続を作成するように求められたら、[要求された接続情報を入力](#create-connection)します。

1. アクションでは、送信するイベントに関する情報を提供します。

   | プロパティ | 必須 | Description |
   |----------|----------|-------------|
   | **イベント ハブ名** | Yes | イベントの送信先となるイベント ハブ |
   | **コンテンツ** | No | 送信するイベントのコンテンツ |
   | **Properties** | No | 送信するアプリのプロパティと値 |
   | **パーティション キー** | No | イベントを送信する[パーティション](../event-hubs/event-hubs-features.md#partitions) ID |
   ||||

   その他のプロパティについては、[**Add new parameter**] (新しいパラメーターを追加) 一覧を開いてください。 パラメーターを選択すると、そのプロパティがアクションに追加されます。次に例を示します。

   ![イベント ハブ名を選択し、イベントの内容を入力する](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   たとえば、別のイベント ハブに Event Hubs トリガーから出力を送信できます。

   ![イベントのサンプルを送信](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

## <a name="connector-reference"></a>コネクタのレファレンス

すべての操作と、プロパティ、制限など、その他の技術情報については、[Event Hubs コネクタの参照ページ](/connectors/eventhubs/)を参照してください。

> [!NOTE]
> [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) でホストされるロジック アプリの場合、このコネクタの ISE バージョンでは、代わりに [ISE メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)が使用されます。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
