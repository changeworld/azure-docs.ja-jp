---
title: Azure Stream Analytics での Event Hub レシーバーのトラブルシューティング
description: この記事は､Stream Analytics ジョブにおいて Event Hubs 入力用に複数の コンシューマー グループを使用する方法を説明しています｡
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2018
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>Azure Stream Analytics での Event Hub レシーバーのトラブルシューティング

Azure Stream Analytics で Azure Event Hubs を使用して、ジョブからデータを取り込んだり出力したりできます。 Event Hubs を使用する際のベスト プラクティスは、複数のコンシューマー グループを使用して、ジョブの拡張性を確保することです。 その理由の 1 つとしては、特定の入力に関する Stream Analytics ジョブのリーダーの数が、1 つのコンシューマー グループ内のリーダーの数に影響することが挙げられます。 レシーバーの正確な数は、スケールアウト トポロジ ロジックの内部実装の詳細に基づいて決まります。 レシーバーの数は外部に公開されません。 リーダーの数は、ジョブのスタート時かジョブのアップグレード中に変更される可能性があります。

レシーバー数が最大数を超えると､`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.` のエラーが表示されます｡

> [!NOTE]
> ジョブのアップグレード中にリーダーの数が変更されると、一時的な警告が監査ログに書き込まれます。 Stream Analytics ジョブは、これらの一時的な問題から自動的に回復します。

## <a name="add-a-consumer-group-in-event-hubs"></a>Event Hubs でコンシューマー グループを追加する
Event Hubs インスタンスに新しいコンシューマー グループを追加するには、次の手順に従います。

1. Azure ポータルにサインインします。

2. Event Hubs のある場所を探します｡

3. **Entities** 見出しの部分から **Event Hubs** を選択します｡

4. 名前でイベント ハブを選択します｡

5. **Event Hubs インスタンス** ページの **Entities** 見出しの部分から **コンシューマー グループ** を選択します｡ **$Default** という名前のコンシューマー グループが表示されます｡

6. **[+ コンシューマー グループ]** を選択し、新しいコンシューマー グループを追加します。 

   ![Event Hubs でコンシューマー グループを追加する](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Stream Analytics ジョブで入力を作成し､イベント ハブをポイントすると､そこにコンシューマー グループを指定したことになります｡ 名前を指定しなかった場合は､$Default が使用されます｡ コンシューマー グループを新規作成したら､Stream Analytics ジョブで Event Hub 入力を編集し､新しいコンシューマー グループの名前を指定します｡


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>パーティションあたりのリーダーの数が Event Hubs の上限 (5 つ) を上回る局面

ストリーミング クエリ構文で同じ入力 Event Hub リソースが複数回参照されている場合、ジョブ エンジンは、同じコンシューマー グループからクエリ 1 つに複数のリーダーを使用することができます。 同じコンシューマー グループに対する参照回数が多すぎると､上限の 5 つを超え､エラーになることがあります｡ その場合は､以下で説明するソリューションを使って､複数の入力を複数のコンシューマー グループにまたがらせることで分割することができます｡ 

パーティションあたりのリーダーの数が Event Hubs の上限である 5 つを上回るシナリオとしては、次のものがあります。

* 複数の SELECT ステートメント: **同じ**イベント ハブ入力を参照する複数の SELECT ステートメントを使用する場合、各 SELECT ステートメントによって新しいレシーバーが作成されます。
* UNION: UNION を使用する場合、**同じ**イベント ハブとコンシューマー グループを参照する複数の入力を使用できます。
* SELF JOIN: SELF JOIN 操作を使用する場合、**同じ**イベント ハブを複数回参照できます。

## <a name="solution"></a>解決策

次のベスト プラクティスは、パーティションあたりのリーダーの数が 5 つという Event Hubs の制限を超えるシナリオに対処するのに役立ちます。

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>WITH 句を使用してクエリを複数のステップに分割する

WITH 句を使用すると、クエリ内で FROM によって参照できる一時的な名前付き結果セットを指定できます。 WITH 句は、単一の SELECT ステートメントの実行スコープで定義します。

たとえば、次のクエリの代わりに、

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

次のクエリを使用します。

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>入力が異なるコンシューマー グループにバインドされるようにする

3 つ以上の入力が同じ Event Hubs コンシューマー グループに接続されるクエリでは、別のコンシューマー グループを作成します。 そのためには、追加の Stream Analytics 入力を作成する必要があります。


## <a name="next-steps"></a>次の手順
* [Stream Analytics ジョブのスケール設定](stream-analytics-scale-jobs.md)
* [Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
