---
title: Azure Communication Services のジョブ分類の概念
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services ジョブ ルーターの分類の概念について説明します。
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: c31c0831f625f65075f3c1f5cde2647ebc73ce64
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308429"
---
# <a name="job-classification-concepts"></a>ジョブ分類の概念

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services ジョブ ルーターでは、ジョブが送信されるときに、**分類** と呼ばれるプロセスを使用します。 この記事では、ジョブを分類するさまざまな方法と、このプロセスがそのジョブに与える影響について説明します。

## <a name="job-classification-overview"></a>ジョブ分類の概要

ジョブ ルーターでは、静的または動的という、ジョブを分類するための 2 つの主な方法を使用します。 呼び出し元のアプリケーションにキュー ID、優先順位、またはワーカー セレクターに関する知識がある場合は、分類ポリシーなしでジョブを送信できます (**静的分類** と呼ばれます)。 ジョブ ルーターでキュー ID が決定されるようにしたい場合は、そのジョブのプロパティを変更するために分類ポリシーを使用できます (**動的分類** と呼ばれます)。

ジョブ ルーター SDK を使用してジョブを送信すると、分類のプロセスにより、Azure Communication Services Event Grid サブスクリプションに送信されるイベントが発生します。 分類ライフサイクルの一部として生成されたイベントによって、ジョブ ルーターが実行しているアクションに関する分析情報が提供されます。 たとえば、分類が成功すると **RouterJobClassified** が生成され、失敗すると **RouterJobClassificationFailed** が生成されます。

ジョブを分類するプロセスには、必要に応じて次のプロパティの設定が含まれます。

- キュー ID
- 優先度
- ワーカー セレクター

## <a name="static-classification"></a>静的分類

事前に定義されたキュー ID、優先順位、ワーカー セレクターを使用してジョブを送信することにより、すばやく開始できます。 ワーカーへの割り当ての前にユーザーが分類ポリシーを指定して更新しない限り、ジョブを送信した後、ジョブ ルーターではこれらのプロパティを変更しません。 送信した後、ジョブの分類ポリシー プロパティを更新することができます。それにより、動的分類プロセスがトリガーされます。

> [!NOTE]
> ジョブ ルーター SDK を使用してジョブのプロパティを手動で更新することによって、動的分類の結果をオーバーライドするオプションがあります。 たとえば、最初に静的なキュー ID を指定し、次に動的に分類される分類ポリシー ID でジョブを更新した後、そのキュー ID をオーバーライドすることもできます。

## <a name="dynamic-classification"></a>動的分類

ジョブを送信するときに分類ポリシーを指定すると、ジョブ ルーターではキュー ID、優先順位を動的に割り当て、さらにワーカー セレクターも潜在的に変更できます。 この種類の分類は、実行時に呼び出し元のアプリケーションに、キュー ID を含むジョブのどのプロパティの知識も必要なくなるため便利です。

### <a name="queue-selectors"></a>キュー セレクター

分類ポリシーでは、特定のジョブのためにどのキュー ID を選択するかを決定するために分類プロセスによって使用される `QueueSelector` を参照できます。 ジョブ ルーターには次の `QueueSelector` の種類が存在し、これらは、分類中にキュー選択プロセスに適用できるオプションです。

**QueueLabelSelector -** ジョブ ルーター キューを作成するとき、ジョブ分類中にキュー選択プロセスに役立つラベルを指定できます。 この種類のセレクターでは、`LabelSelectorAttachment` の種類のコレクションを使用して、分類プロセス中にキューを選択するための最も高い柔軟性を提供します。 このセレクターを使用すると、ジョブ分類プロセスでは、ラベルに基づいてキュー ID を選択できます。 詳細については、[下の](#using-labels-and-selectors-in-classification)セクションを参照してください。

**QueueIdSelector -** このセレクターでは、多くのルール エンジンのうちのいずれかを使用して、ルールの結果に基づいてジョブのキュー ID を決定できます。 詳細については、[RouterRule の概念](router-rule-concepts.md)に関するページを参照してください。

### <a name="worker-selectors"></a>ワーカー セレクター

分類ポリシーのワーカー セレクターには、ラベルに基づいてワーカー セレクターをジョブにアタッチするために分類プロセスによって使用される `LabelSelectorAttachment` の種類のコレクションが含まれています。 詳細については、[下の](#using-labels-and-selectors-in-classification)セクションを参照してください。

### <a name="prioritization-rule"></a>優先順位付けルール

ジョブの優先順位は、`QueueIdSelector` の動作方法と同様に、多くのルール エンジンのうちのいずれかを使用して分類中に解決できます。 詳細については、[RouterRule の概念](router-rule-concepts.md)に関するページを参照してください。

## <a name="using-labels-and-selectors-in-classification"></a>分類でのラベルとセレクターの使用

ジョブ ルーターでは、ジョブ、ワーカー、キューのキーと値のペアの "ラベル" を使用して、ルーティングに関するさまざまな決定を行います。 `QueueSelector` で `LabelSelectorAttachment` を使用しているときは、フィルターのように機能します。 `WorkerSelectors` のコンテキスト内で使用されているときは、セレクターを、そのジョブで作成された初期セットにアタッチします。 次の `LabelSelectorAttachment` の種類を使用できます。

**静的ラベル セレクター -** 常に、指定された `LabelSelector` をアタッチします。

**条件付きラベル セレクター -** ルールによって定義された条件を評価します。  それが `true` に解決された場合は、指定されたセレクターのコレクションが適用されます。

**パススルー ラベル セレクター -** キーと `LabelOperator` を使用して、そのキーの存在をチェックします。 このセレクターを `QueueLabelSelector` で使用すると、ラベルのセットに基づいてキューを照合できます。 `WorkerSelectors` で使用されているときは、ジョブの `WorkerSelectors` にそのジョブのキーと値のペアがアタッチされます。

**ルール ラベル セレクター -** セレクターのコレクションを多くのルール エンジンのうちのいずれかから取得します。 詳細については、[RouterRule の概念](router-rule-concepts.md)に関するページを参照してください。

**重み付け割り当てラベル セレクター -** それぞれが割合に基づく重み付けと、重み付けの割り当てに基づいて適用するセレクターのコレクションを指定する `WeightedAllocation` オブジェクトのコレクション。 たとえば、ジョブの 30% を "Contoso" に移動し、ジョブの 70% を "Fabrikam" に移動することもできます。

## <a name="reclassifying-a-job"></a>ジョブの再分類
ジョブが分類された後は、そのジョブを次の方法で再分類できます。

1. ジョブ ラベルを更新することができます。それにより、ジョブ ルーターは、以前の分類ポリシーを使用して新しいラベルを評価します。
2. ジョブの分類ポリシー ID を更新することができます。それにより、ジョブ ルーターは、既存のジョブを新しいポリシーに対して処理します。
3. 例外ポリシーの **トリガー** では、ジョブの再分類を要求する **アクション** を実行できます。 

> [!NOTE]
> ジョブ ルーター SDK には、ジョブ ルーターに再分類プロセスを実行させることなく、単純にラベルを更新する `UpdateJobLabels` メソッドが含まれています。
