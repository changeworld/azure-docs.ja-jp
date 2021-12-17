---
title: Azure Communication Services の Job Router の概要
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services Job Router について説明します。
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 86902147b68272fb0ff2ef5873ad6cca80654843
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312402"
---
# <a name="job-router-concepts"></a>Job Router の概念

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services Job Router を使用すると、システム上の何らかの抽象的な供給を何らかの抽象的な需要に対応付ける際の問題を解決できます。 Job Router は Azure Event Grid と統合されており、準リアルタイムの通知を送信し、Job Router インスタンスの動作を制御する応答型アプリケーションを構築することができます。

## <a name="job-router-overview"></a>Job Router の概要

Job Router SDK を使用すると、作業単位を特定のリソースに対応付ける必要があるさまざまなビジネス シナリオを構築できます。 たとえば、多くの潜在的なコンタクト センターのエージェントとの一連の電話、または他の人との複数の同時セッションを処理するライブ エージェントとの Web チャット要求であると作業を定義することができます。 何らかの抽象的な作業単位を使用可能なリソースにルーティングする必要がある場合、[ジョブ](#job)と呼ばれる作業、[キュー](#queue)、[worker](#worker)、一連の[ポリシー](#policies)を定義する必要があります。ここでは、これらのコンポーネントが相互にどのように作用するかの動作の側面を定義します。

## <a name="job-router-architecture"></a>Job Router のアーキテクチャ

サービス内のアクションについてアプリケーションに通知するために、Azure Communication Services Job Router にはイベントが使用されます。 次の図は、ジョブの送信、worker の登録、ジョブ オファーの処理という Job Router に共通する簡単なフローを示しています。

### <a name="job-submission-flow"></a>ジョブの送信フロー

1. この Contoso アプリケーションから Azure Communication Services インスタンス内の Job Router にジョブが送信されます。
2. ジョブは分類され、**RouterJobClassified** というイベントが発生します。これには、ジョブに関するすべての情報と、分類プロセスによってプロパティがどのように変更されたかが含まれます。
 
    :::image type="content" source="../media/router/acs-router-job-submission.png" alt-text="Communication Services の Job Router からのジョブの送信を示す図。":::

### <a name="worker-registration-flow"></a>worker の登録フロー

1. worker がジョブを受け入れる準備ができると、Contoso のアプリケーションを介して Job Router に登録されます。
2. すると、Job Router から **RouterWorkerRegistered** が返送されます。

    :::image type="content" source="../media/router/acs-router-worker-registration.png" alt-text="Communication Services の Job Router の worker の登録を示す図。":::

### <a name="matching-and-accepting-a-job-flow"></a>ジョブ フローの対応付けと受け入れ

1. Job Router によってジョブに対応する worker が検出されると、作業が提示するために **RouterWorkerOfferIssued** が送信されます。これは、Contoso アプリケーションと、Azure SignalR Service などのプラットフォームを使用する接続済みユーザーの間でシグナルを送受信するときに使用されるものです。
2. worker はオファーを受け入れます。
3. Job Router から、worker がジョブに割り当てられたことを示す **RouterworkerOfferAccepted** が Contoso Application に送信されます。

    :::image type="content" source="../media/router/acs-router-accept-offer.png" alt-text="Communication Services の Job Router による提示の受け入れを示す図。":::

## <a name="real-time-notifications"></a>リアルタイム通知

送信したワークロードに対して Job Router が実行しているアクションに関する通知を送信するために、Azure Communication Services には Event Grid のメッセージング プラットフォームが使用されています。 ジョブの作成、完了、提示の受け入れといったジョブ ライフサイクル イベントなど、重要なアクションが発生するたびに、Job Router からイベントの形式でメッセージが送信されます。

## <a name="job"></a>ジョブ

ジョブは、使用できる worker にルーティングする必要のある作業単位を表します。 ジョブを定義するには、Azure Communication Services Job Router SDK を使用するか、REST API に認証された要求を送信します。 多くの場合、ジョブには、呼び出し ID やチケット番号などの何らかの一意の識別子への参照と、実行されている作業の特徴が含まれています。

## <a name="queue"></a>キュー

ジョブが作成されると、送信時に静的に、または分類ポリシーの適用によって動的に、キューに割り当てられます。 ジョブは割り当てられたキューごとにグループ化されます。また、ワークロードの配分方法に応じてさまざまな特性を持つ可能性があります。 キューには、適格な worker にどのようにジョブを提示するかを決定する **配分ポリシー** が必要です。

Job Router のキューには、特定の条件が発生したときのジョブの動作を決定する例外ポリシーを含めることもできます。 たとえば、タイマーやその他の条件に基づいて、ジョブを別のキューに移動させる、優先度を上げる、またはその両方を実行することができます。

## <a name="worker"></a>ワーカー

worker は、特定のキューのジョブを処理するために使用できる供給を表します。 Job Router に登録された各 worker には、**ラベル**、それらに関連付けられた **キュー**、**チャネルの構成**、**合計容量スコア** のセットが設定されています。 これらの要素を使用して、いつ、どのようにジョブを worker にルーティングするかが Job Router によってリアルタイムで決定されます。

Azure Communication Services Job Router によって、worker の状態の保守と使用のために単純な **アクティブ**、**非アクティブ**、**ドレイン中** という状態が使用され、使用できるジョブを worker に対応付けるられるタイミングが決定されます。 この状態、チャネルの構成、合計容量スコアを組み合わて、Job Router によって実行可能な worker が計算され、ジョブに関連する提示が発行されます。

## <a name="policies"></a>ポリシー

Azure Communication Services Job Router によって、システムのさまざまな側面に動的な動作をアタッチする柔軟なポリシーが適用されます。 ポリシーに応じてジョブの優先度、格納するキューなどを変更するために、ジョブのラベルが使用および評価されます。 Job Router の一部のポリシーには、PowerFx を使用したインライン関数処理や、より複雑なシナリオ向けの Azure Function へのコールバックの機能があります。

**分類ポリシー -** 分類ポリシーを使用すると、Job Router によってキュー、優先度を定義することができます。また、送信者が送信時にこれらのパラメーターを確認できない、または認識していない場合に、worker セレクターを変更することができます。 分類の詳細については、[分類の概念](classification-concepts.md)に関するページを参照してください。

**配分ポリシー -** Job Router に新しい受信されると、適切な worker を特定し、ジョブの提示を管理するため配分ポリシーが使用されます。 さまざまな **モード** を使用して worker が選択され、ポリシーに基づいて、Job Router から 1 人以上の worker に同時に通知することができます。

**例外ポリシー -** 例外ポリシーを使用すると、トリガーに基づいてジョブの動作を制御し、目的のアクションを実行することができます。 例外ポリシーはキューにアタッチされているので、キュー内のジョブの動作を制御することができます。

## <a name="next-steps"></a>次のステップ

- [ルーター ルールの概念](router-rule-concepts.md)
- [分類の概念](classification-concepts.md)
- [配分の概念](distribution-concepts.md)
- [クイックスタート ガイド](../../quickstarts/router/get-started-router.md)
- [キューの管理](../../how-tos/router-sdk/manage-queue.md)
- [ジョブの分類](../../how-tos/router-sdk/job-classification.md)
- [ジョブをエスカレートする](../../how-tos/router-sdk/escalate-job.md)
- [イベントをサブスクライブする](../../how-tos/router-sdk/subscribe-events.md)
