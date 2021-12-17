---
title: Conversational Language Understanding で発話にタグを付ける方法
titleSuffix: Azure Cognitive Services
description: この記事では、Conversational Language Understanding プロジェクトで発話にタグを付ける方法について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: 73ab3df8a7f13ec2c734206772f55bee626b871a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339936"
---
# <a name="how-to-tag-utterances"></a>発話にタグを付ける方法

プロジェクトの[スキーマを作成](build-schema.md)したら、プロジェクトにトレーニング発話を追加する必要があります。 発話は、ユーザーがプロジェクトと対話するときに使うものと似たものとする必要があります。 発話を追加するときは、それが属する意図を割り当てる必要があります。 発話を追加した後、発話内の単語に、プロジェクト内のエンティティでラベル付けします。 エンティティに対するラベルは、異なる発話間で一貫している必要があります。

タグ付けは、発話を意図に割り当て、それらにエンティティでラベル付けするプロセスです。 発話にタグ付けして、プロジェクトの基になる機械学習モデルをトレーニングするデータを導入して調整することに時間を費やす必要があります。 機械学習モデルでは、提供した例に基づいて一般化が行われます。 提供する例が多いほど、モデルのデータ ポイントが増えて一般化がさらに改善されます。

> [!NOTE]
>  エンティティの学習対象コンポーネントは、そのエンティティの発話にラベルを付けるときにのみ定義されます。 また、学習対象コンポーネントをラベル付けせずに、リストまたは事前構築済みのコンポーネント _のみ_ を含むエンティティを持つこともできます。 詳細については、[エンティティ コンポーネント](../concepts/entity-components.md)の記事を参照してください。

プロジェクトで複数の言語を有効にする場合は、追加する発話の言語も指定する必要があります。 Conversational Language Understanding の多言語機能の一部として、ある主要言語でプロジェクトをトレーニングした後、使用可能なその他の言語で予測することができます。 モデルのパフォーマンスが良くないと判断される場合、他の言語に例を追加すると、それらの言語でモデルのパフォーマンスが向上しますが、サポートするすべての言語でデータを複製しないようにしてください。 

たとえば、ユーザーとのカレンダー ボットのパフォーマンスを向上させるために、開発者はほとんどの例を英語で追加し、いくつかの例をスペイン語やフランス語で追加することもできます。 次のような発話を追加できます。

* "_Set a meeting with **Matt** and **Kevin** **tomorrow** at **12 PM**._ " (英語)
* "_Reply as **tentative** to the **weekly update** meeting._ " (英語)
* "_Cancelar mi **próxima** reunión_." (スペイン語)

オーケストレーション ワークフロー プロジェクトでは、接続された意図のトレーニングに使われるデータはプロジェクト内で提供されません。 その代わりに、プロジェクトはトレーニング中、接続されたサービス (接続されている LUIS アプリケーション、Conversational Language Understanding プロジェクト、カスタム質問と回答のナレッジ ベースなど) からデータを取得します。 ただし、どのサービスにも接続されていない意図を作る場合は、その意図に発話を追加する必要があります。

たとえば、開発者はこれらのそれぞれのスキルに対して意図を作り、これをそれぞれのカレンダー プロジェクト、メール プロジェクト、会社の FAQ のナレッジ ベースに接続することができます。 

## <a name="tag-utterances"></a>発話にタグ付けする

:::image type="content" source="../media/tag-utterances.png" alt-text="Language Studio で発話にタグ付けするためのページのスクリーンショット。" lightbox="../media/tag-utterances.png":::

*セクション 1* では、発話を追加します。 ドロップダウン リストからいずれかの意図を選び、発話の言語 (該当する場合) および発話自体を選ぶ必要があります。 発話のテキスト ボックスで *Enter* キーを押して発話を追加します。

*セクション 2* では、プロジェクトのエンティティを含めます。 追加したエンティティのいずれかを選び、*セクション 3* に示すように、テキストにマウス ポインターを合わせ、発話内のエンティティにラベルを付けることができます。 **[+ エンティティの追加]** ボタンを押して新しいエンティティを追加することもできます。 発話内のエンティティのラベルを非表示にすることもできます。 

*セクション 3* では、追加した発話を組み込みます。 ラベルを付けるテキストをドラッグすると、エンティティのコンテキスト メニューが表示されます。

:::image type="content" source="../media/label-utterance-menu.png" alt-text="ラベルを追加するテキストを選ぶ例。" lightbox="../media/label-utterance-menu.png":::

> [!NOTE]
> LUIS とは異なり、重複するエンティティにラベルを付けることはできません。 同じ文字に複数のエンティティによってラベル付けすることはできません。

## <a name="filter-utterances"></a>発話をフィルター処理する

**[フィルター]** をクリックすると、フィルター ペインで選んだ意図とエンティティに関連付けられている発話のみを表示できます。
[[スキーマの作成]](./build-schema.md) ページで意図をクリックすると、 **[Tag Utterances]\(発話にタグ付け\)** ページに移動します。その意図は自動的にフィルター処理されています。 

## <a name="next-steps"></a>次の手順
* [モデルをトレーニングして評価する](./train-model.md)
