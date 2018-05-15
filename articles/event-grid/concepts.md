---
title: Azure Event Grid の概念
description: Azure Event Grid とその概念について説明します。 Event Grid のいくつかの主要コンポーネントを定義します。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/24/2018
ms.author: babanisa
ms.openlocfilehash: 8ddde98b448f4d6d6f24a2ee47acf9240593622c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="concepts-in-azure-event-grid"></a>Azure Event Grid の概念

この記事では、Azure Event Grid の主要な概念について説明します。

## <a name="events"></a>イベント

イベントは、システム内で発生した何かを完全に記述する最小限の情報です。 すべてのイベントは、イベントの発生元、イベントの発生時間、一意識別子などの一般的な情報を持っています。 各イベントには、特定の種類のイベントにのみ関連する情報も含まれます。 たとえば、Azure Storage に作成される新しいファイルに関するイベントには、`lastTimeModified` 値などのファイルの詳細が含まれます。 または、Event Hubs イベントには、キャプチャ ファイルの URL が含まれます。 各イベントは、64 KB のデータに制限されます。

## <a name="event-sourcespublishers"></a>イベント ソース/発行元

イベント ソースは、イベントの発生場所です。 たとえば、Azure Storage は、BLOB 作成イベントのイベント ソースです。 Azure VM Fabric は、仮想マシン イベントのイベント ソースです。 Event Grid へのイベントの発行は、イベント ソースの責任で行われます。

サポートされている任意の Event Grid ソースの実装方法については、「[Event sources in Azure Event Grid (Azure Event Grid 内のイベント ソース)](event-sources.md)」を参照してください。

## <a name="topics"></a>トピック

発行元は、イベントをトピックに分類します。 Event Grid トピックには、発行元がイベントを送信するエンドポイントが含まれます。 サブスクライバーは、特定の種類のイベントに応答するために、どのトピックをサブスクライブするかを決定します。 トピックは、サブスクライバーがイベントを適切に使用する方法を学習できるようにするイベント スキーマも提供します。

システム トピックは、Azure サービスが提供する組み込みのトピックです。 カスタム トピックは、アプリケーションとサード パーティのトピックです。

アプリケーションを設計するときに、作成するトピック数を柔軟に決定することができます。 大規模なソリューションの場合、関連するイベントのカテゴリごとにカスタム トピックを作成します。 たとえば、ユーザー アカウントの変更と注文の処理に関連するイベントを送信するアプリケーションがあるものとします。 イベント ハンドラーで両方のイベント カテゴリが必要になることはまずありません。 このような場合は、2 つのカスタム トピックを作成し、イベント ハンドラーが自分と関係のあるトピックをサブスクライブできるようにします。 小規模なソリューションの場合、すべてのイベントを 1 つのトピックに送信することをお勧めします。 イベントのサブスクライバーは、必要なイベントの種類をフィルター処理できます。

## <a name="event-subscriptions"></a>イベントのサブスクリプション

サブスクリプションは、サブスクライバーが受信するつもりのイベントのトピックを Event Grid に指示します。 サブスクリプションは、サブスクライバーにイベントを配信する方法に関する情報も保持します。

## <a name="event-handlers"></a>イベント ハンドラー

Event Grid から考えると、イベント ハンドラーはイベントの送信先です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 Event Grid は、複数の種類のサブスクライバーをサポートします。 Event Grid は、サブスクライバーの種類に応じたさまざまなメカニズムに従って、イベントの配信を保証します。 HTTP Webhook イベント ハンドラーでは、ハンドラーが状態コード `200 – OK` を返すまでイベントが再試行されます。 Azure Storage Queue では、Queue サービスがキューにメッセージのプッシュを正常に処理できるようになるまで、イベントが再試行されます。

サポートされている任意の Event Grid ハンドラーの実装方法については、「[Event handlers in Azure Event Grid (Azure Event Grid 内のイベント ハンドラー)](event-handlers.md)」を参照してください。

## <a name="filters"></a>フィルター

Event Grid トピックをサブスクライブするときに、エンドポイントに送信されるイベントをフィルター処理できます。 イベントの種類またはサブジェクトのパターンでフィルター処理できます。 詳細については、「[Event Grid subscription schema](subscription-creation-schema.md)」(Event Grid のサブスクリプション スキーマ) を参照してください。

## <a name="security"></a>セキュリティ

Event Grid は、トピックのサブスクライブと発行をセキュリティで保護します。 サブスクライブするときは、リソースまたは Event Grid トピックに対する十分なアクセス許可が必要です。 発行するときは、トピック用の SAS トークンまたはキー認証が必要です。 詳細については、「[Event Grid security and authentication](security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。

## <a name="failed-delivery"></a>失敗した配信

イベントがサブスクライバーのエンドポイントによって受信されたことを Event Grid が確認できない場合、イベントは再配信されます。 詳細については、[Event Grid のメッセージの配信と再試行](delivery-and-retry.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
