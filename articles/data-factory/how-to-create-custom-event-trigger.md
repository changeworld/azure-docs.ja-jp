---
title: Azure Data Factory でカスタム イベント トリガーを作成する
description: Azure Data Factory で、Event Grid に発行されたカスタム イベントに応答してパイプラインを実行するトリガーを作成する方法について説明します。
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: d91e1f52f0844317b049086489bda25c079ee9be
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2021
ms.locfileid: "109634295"
---
# <a name="create-a-custom-event-trigger-to-run-a-pipeline-in-azure-data-factory-preview"></a>Azure Data Factory でカスタム イベント トリガーを作成してパイプラインを実行する (プレビュー)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

イベントドリブン アーキテクチャ (EDA) は、イベントの運用、検出、使用、および応答を含む一般的なデータ統合パターンです。 多くの場合、データ統合シナリオでは、特定のイベントが発生したときに Azure Data Factory ユーザーがパイプラインをトリガーする必要があります。 Data Factory と [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) がネイティブに統合され、[カスタム トピック](../event-grid/custom-topics.md)がカバーされました。 イベント グリッド トピックにイベントを送信します。 Data Factory では、トピックをサブスクライブしてリッスンし、それに応じてパイプラインをトリガーします。

> [!NOTE]
> この記事で説明されている統合は、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) に依存しています。 サブスクリプションが Event Grid リソース プロバイダーに登録されていることを確認してください。 詳細については、「[リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)」を参照してください。 `Microsoft.EventGrid/eventSubscriptions/` アクションを実行できる必要があります。 このアクションは、[EventGrid EventSubscription 共同作成者](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor)の組み込みロールの一部です。

パイプライン パラメーターとカスタム イベント トリガーを組み合わせると、パイプラインの実行でカスタム `data` ペイロードを解析して参照できます。 カスタム イベント ペイロードの `data` フィールドは自由形式の JSON キー値構造なので、イベント駆動型パイプラインの実行を制御できます。

> [!IMPORTANT]
> パラメーター化で参照されるキーがカスタム イベント ペイロードに存在しない場合、`trigger run` は失敗します。 プロパティ `keyName` が存在しないため式を評価できないというエラーが表示されます。 この場合、イベントによってトリガーされる `pipeline run` は **ありません**。

## <a name="set-up-a-custom-topic-in-event-grid"></a>Event Grid でカスタム トピックを設定する

Data Factory でカスタム イベント トリガーを使用するには、"*まず*" [Event Grid でカスタム トピック](../event-grid/custom-topics.md)を設定する必要があります。

Azure Event Grid に移動し、自分でトピックを作成します。 カスタム トピックを作成する方法の詳細については、Azure Event Grid の[ポータルのチュートリアル](../event-grid/custom-topics.md#azure-portal-tutorials)と [CLI のチュートリアル](../event-grid/custom-topics.md#azure-cli-tutorials)に関する記事を参照してください。

> [!NOTE]
> このワークフローは、ストレージ イベント トリガーとは異なります。 ここでは、トピックは Data Factory によって設定されません。

Data Factory は、イベントが [Event Grid イベント スキーマ](../event-grid/event-schema.md)に従うことを想定しています。 イベント ペイロードに次のフィールドがあることを確認してください。

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="use-data-factory-to-create-a-custom-event-trigger"></a>Data Factory を使用してカスタム イベント トリガーを作成する

1. Azure Data Factory に移動してサインインします。

1. **[編集]** タブに切り替えます。鉛筆アイコンを探します。

1. メニューの **[トリガー]** を選択し、 **[新規作成/編集]** を選択します。

1. **[Add Triggers]\(トリガーの追加\)** ページで、 **[Choose trigger]\(トリガーの選択\)** を選択し、 **[+ 新規]** を選択します。

1. **[種類]** に **[カスタム イベント]** を選択します。

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Data Factory UI で新しいカスタム イベント トリガーを作成するための作成者ページのスクリーンショット。" lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. [Azure サブスクリプション] ドロップダウンからカスタム トピックを選択するか、イベント トピックのスコープを手動で入力します。

   > [!NOTE]
   > Data Factory でカスタム イベント トリガーを作成または変更するには、適切なロールベースのアクセス制御 (Azure RBAC) を持つ Azure アカウントを使用する必要があります。 追加のアクセス許可は不要です。 Data Factory のサービス原則では、Event Grid に対する特別なアクセス許可は必要 "*ありません*"。 アクセス制御の詳細については、「[ロールベースのアクセス制御](#role-based-access-control)」セクションを参照してください。

1. **[次で始まるサブジェクト]** および **[次で終わるサブジェクト]** プロパティを使用して、トリガー イベントをフィルター処理できます。 これらのプロパティは両方とも省略可能です。

1. **[+ 新規]** を使用して、フィルター処理する **[イベントの種類]** を追加します。 カスタム イベント トリガーの一覧では、OR リレーションシップが使用されます。 `eventType` プロパティを持つカスタム イベントが一覧上の 1 つと一致すると、パイプラインの実行がトリガーされます。 イベントの種類では大文字と小文字が区別されます。 たとえば、次のスクリーンショットでは、トリガーは、サブジェクトが *factories* で始まるすべての `copycompleted` または `copysucceeded` イベントと一致します。

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Data Factory の UI でイベントの種類とサブジェクトのフィルター処理を説明する [トリガーの編集] ページのスクリーンショット。":::

1. カスタム イベント トリガーを使用して、カスタム `data` ペイロードを解析し、パイプラインに送信できます。 パイプライン パラメーターを作成し、 **[パラメーター]** ページで値を入力します。 形式 `@triggerBody().event.data._keyName_` を使用してデータ ペイロードを解析し、パイプラインのパラメーターに値を渡します。
 
   詳細については、次の記事を参照してください。
   - [パイプラインでのトリガー メタデータの参照](how-to-use-trigger-parameterization.md)
   - [カスタム イベント トリガーのシステム変数](control-flow-system-variables.md#custom-event-trigger-scope)

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="パイプライン パラメーターの設定のスクリーンショット。":::

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="カスタム イベントのデータ ペイロードを参照するための [パラメーター] ページのスクリーンショット。":::

1. パラメーターを入力したら、 **[OK]** を選択します。

## <a name="json-schema"></a>JSON スキーマ

次の表に、カスタム イベント トリガーに関連するスキーマ要素の概要を示します。

| JSON 要素 | 説明 | Type | 使用できる値 | 必須 |
|---|----------------------------|---|---|---|
| `scope` | イベント グリッド トピックの Azure Resource Manager リソース ID。 | String | Azure Resource Manager ID | はい |
| `events` | このトリガーを起動するイベントの種類。 | 文字列の配列    |  | はい。少なくとも 1 つの値が必要です。 |
| `subjectBeginsWith` | `subject` フィールドは、トリガーを起動するために指定されたパターンで始まる必要があります。 たとえば、*factories* を指定すると、*factories* で始まるイベント サブジェクトに対してのみトリガーが起動されます。 | String   | | いいえ |
| `subjectEndsWith` | `subject` フィールドは、トリガーを起動するために指定されたパターンで終わる必要があります。 | String   | | いいえ |

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure Data Factory では、承認されていないアクセスを禁止するために Azure RBAC が使用されます。 正常に機能するには、Data Factory で、以下を行うためのアクセス権が必要です。
- イベントをリッスンする。
- イベントから更新をサブスクライブする。
- カスタム イベントにリンクされているパイプラインをトリガーする。

カスタム イベント トリガーを正常に作成または更新するには、適切なアクセス権を持つ Azure アカウントを使用して Data Factory にサインインする必要があります。 それ以外の場合は、"_アクセス拒否_" エラーが発生して操作が失敗します。

Data Factory には、Event Grid に対する特別なアクセス許可は必要ありません。 この操作のために Data Factory サービス プリンシパルに特別な Azure RBAC アクセス許可を割り当てる必要も "*ありません*"。

具体的には、`/subscriptions/####/resourceGroups//####/providers/Microsoft.EventGrid/topics/someTopics` に対する `Microsoft.EventGrid/EventSubscriptions/Write` 権限が必要です。

## <a name="next-steps"></a>次のステップ

* [トリガー実行](concepts-pipeline-execution-triggers.md#trigger-execution)に関する詳細情報を取得する。
* [パイプライン実行でのトリガー メタデータの参照](how-to-use-trigger-parameterization.md)方法について学ぶ。
