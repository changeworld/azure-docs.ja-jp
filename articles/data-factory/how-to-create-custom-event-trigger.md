---
title: Azure Data Factory でカスタム イベント トリガーを作成する
description: Event Grid に発行されたカスタム イベントに応答してパイプラインを実行するカスタム トリガーを Azure Data Factory で作成する方法について説明します。
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2d2f26b24e7fa10d9244de8f99d78c64a44b3d61
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785650"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>カスタム イベントに応答してパイプラインを実行するトリガーを作成する (プレビュー)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Data Factory のパイプラインで作成できるカスタム イベント トリガーについて説明します。

イベントドリブン アーキテクチャ (EDA) は、イベントの運用、検出、使用、および応答を含む一般的なデータ統合パターンです。 多くの場合、データ統合シナリオでは、Data Factory ユーザーが特定のイベントの発生に基づいてパイプラインをトリガーする必要があります。 Data Factory の [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) とのネイティブ統合により、[カスタム イベント](../event-grid/custom-topics.md)を利用できるようになりました。ユーザーが任意のイベントをイベント グリッド トピックに送信すると、Data Factory がそのトピックをサブスクライブしたり、リッスンしたりし、それに応じてパイプラインをトリガーします。

> [!NOTE]
> この記事で説明されている統合は、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) に依存しています。 サブスクリプションが Event Grid リソース プロバイダーに登録されていることを確認してください。 詳細については、「[リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)」を参照してください。 *Microsoft.EventGrid/eventSubscriptions/* * アクションを実行できる必要があります。 このアクションは、EventGrid EventSubscription 共同作成者の組み込みロールの一部です。

さらに、パイプライン パラメーターとカスタム イベント トリガーを組み合わせると、パイプラインの実行でカスタム _データ_ ペイロードを解析して参照できます。 カスタム イベント ペイロードの _データ_ フィールドは、自由形式の json キー値構造で、これによりユーザーはイベント ドリブン パイプラインの実行を最大限に制御できます。

> [!IMPORTANT]
> 多くの場合、パラメーター化で参照されるキーがカスタム イベント ペイロードに欠落している可能性があります。 プロパティ _keyName_ が存在しないため、_トリガーの実行_ は失敗し、式を評価できないというエラーが表示されます。 イベントによってトリガーされる _パイプラインの実行_ は __ありません__。

## <a name="setup-event-grid-custom-topic"></a>Event Grid カスタム トピックのセットアップ

Data Factory でカスタム イベント トリガーを使用するには、_まず_ [Event Grid でカスタム トピック](../event-grid/custom-topics.md)を設定する必要があります。 このワークフローは、Data Factory によってトピックが設定されるストレージ イベント トリガーとは異なります。 ここでは、Azure Event Grid に移動し、自分でトピックを作成する必要があります。 カスタム トピックを作成する方法の詳細については、Azure Event Grid の[ポータルのチュートリアル](../event-grid/custom-topics.md#azure-portal-tutorials)と [CLI のチュートリアル](../event-grid/custom-topics.md#azure-cli-tutorials)を参照してください。

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

## <a name="data-factory-ui"></a>Data Factory UI

このセクションでは、Azure Data Factory ユーザー インターフェイス内でストレージ イベント トリガーを作成する方法について説明します。

1. 鉛筆のシンボルを使用して表示された **[編集]** タブに切り替えます。

1. メニューの **[トリガー]** を選択して、 **[New/Edit]\(新規作成/編集\)** を選択します。

1. **[Add Triggers]\(トリガーの追加\)** ページで、 **[Choose trigger]\(トリガーの選択\)** を選択してから、 **[+新規]** を選択します。

1. トリガーの種類 **[カスタム イベント]** の選択

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Data Factory UI で新しいカスタム イベント トリガーを作成するための作成者ページのスクリーンショット。" lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. [Azure サブスクリプション] ドロップダウンからカスタム トピックを選択するか、イベント トピックのスコープを手動で入力します。

   > [!NOTE]
   > 新しいカスタム イベント トリガーの作成や既存のカスタム イベント トリガーの変更を行うには、Data Factory へのログインとストレージ イベント トリガーの発行に使用される Azure アカウントに、トピックに対する適切なロールベースのアクセス制御 (Azure RBAC) アクセス許可が付与されている必要があります。 追加のアクセス許可は不要です。Azure Data Factory のサービス プリンシパルには、Event Grid に対する特別なアクセス許可は必要 _ありません_。 アクセスの制御の詳細については、「[ロールベースのアクセス制御](#role-based-access-control)」セクションを参照してください。

1. **[次で始まるサブジェクト]** および **[次で終わるサブジェクト]** プロパティを使用して、パイプラインをトリガーするイベントをフィルター処理できます。 これらのプロパティは両方とも省略可能です。

1. **[+ 新規]** を使用して、フィルター処理する **[イベントの種類]** を追加します。 カスタム イベント トリガーでは、リストに対して OR リレーションシップが使用されます。カスタム イベントに、ここにリストされたものと一致する _eventType_ プロパティがある場合、パイプラインの実行がトリガーされます。 イベントの種類では大文字と小文字が区別されます。 たとえば、次のスクリーンショットでは、サブジェクトが「_factory_」で始まるすべての _copycompleted_ または _copycompleted_ イベントがトリガーされます。

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Data Factory の UI でイベントの種類とサブジェクトのフィルター処理を説明する [トリガーの編集] ページのスクリーンショット。":::

1. カスタム イベント トリガーでは、カスタム _データ_ ペイロードを解析し、パイプラインに送信できます。 まず、パイプライン パラメーターを作成し、 **[パラメーター]** ページで値を入力します。 **@triggerBody().event.data._keyName_** の形式を使用して、データ ペイロードを解析し、パイプライン パラメーターに値を渡します。 詳細については、[パイプライン内のトリガー メタデータの参照](how-to-use-trigger-parameterization.md)に関するページ、および[カスタム イベント トリガーのシステム変数](control-flow-system-variables.md#custom-event-trigger-scope)に関するページを参照してください。

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="パイプライン パラメーターの設定のスクリーンショット。":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="カスタム イベントのデータ ペイロードを参照するための [パラメーター] ページのスクリーンショット。":::

1. 完了したら **[OK]** をクリックします。

## <a name="json-schema"></a>JSON スキーマ

次の表に、カスタム イベント トリガーに関連するスキーマ要素の概要を示します。

| **JSON 要素** | **説明** | **Type** | **使用できる値** | **必須** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **スコープ (scope)** | イベント グリッド トピックの Azure Resource Manager リソース ID。 | String | Azure Resource Manager ID | はい |
| **events** | このトリガーを起動するイベントの種類。 | 文字列の配列    |  | はい。少なくとも 1 つの値が必要です。 |
| **subjectBeginsWith** | サブジェクト フィールドは、トリガーを起動するために指定されているパターンで始まる必要があります。 たとえば、`factories` では、`factories` で始まるイベント サブジェクトに対してのみトリガーが起動されます。 | String   | | いいえ |
| **subjectEndsWith** | サブジェクト フィールドは、トリガーを起動するために指定されているパターンで終わる必要があります。 | String   | | いいえ |

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure Data Factory は、Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、カスタム イベントにリンクされたパイプラインをリッスン、更新元をサブスクライブ、トリガーするための不正アクセスを禁止します。

* カスタム イベント トリガーの新規作成または既存の更新を正常に行うには、Data Factory にサインインしている Azure アカウントが、関連するストレージ アカウントに適切にアクセスできる必要があります。 できない場合は、_アクセス拒否_ によって操作が失敗します。
* Data Factory には Event Grid に対する特別なアクセス許可は必要ありません。この操作のために Data Factory サービス プリンシパルに特別な Azure RBAC アクセス許可を割り当てる必要は _ありません_。

具体的には、ユーザーは _/subscriptions/####/resourceGroups//####/providers/Microsoft.EventGrid/topics/someTopics_ に対する _Microsoft.EventGrid/EventSubscriptions/Write_ アクセス許可を必要とします。

## <a name="next-steps"></a>次のステップ

* トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#trigger-execution)」をご覧ください。
* パイプラインでトリガー メタデータを参照する方法については、[パイプライン実行でのトリガー メタデータの参照](how-to-use-trigger-parameterization.md)に関する記事を参照してください
