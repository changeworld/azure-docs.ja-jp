---
title: ハブ、グループ、接続に関する Azure Web PubSub の基本的な概念
description: Azure Web PubSub で使用される用語について理解を深めます。
author: lianwei
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: dc05e81228149fbeae647af81f97bc1e3a320c1f
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997706"
---
# <a name="azure-web-pubsub-basic-concepts"></a>Azure Web PubSub の基本的な概念

Azure Web PubSub サービスは、リアルタイム メッセージング Web アプリを作成するのに役立ちます。 クライアントによるサービスへの接続には[標準の WebSocket プロトコル](https://datatracker.ietf.org/doc/html/rfc6455)が使用され、これらのクライアントを管理できるように、サービスにより [REST API](/rest/api/webpubsub) と SDK が公開されます。

## <a name="terms"></a>用語

サービスで使用される重要な用語を次に示します。

[!INCLUDE [Terms](includes/terms.md)]

## <a name="workflow"></a>ワークフロー

サービスを使用する一般的なワークフローを次に示します。

![Web PubSub サービス ワークフローを示す図。](./media/concept-service-internals/workflow.png)

上のワークフロー グラフに示されているように:

1. *クライアント* は、WebSocket トランスポートを使用してサービス `/client` エンドポイントに接続します。 サービスは、すべての WebSocket フレームを、構成されているアップストリーム (サーバー) に転送します。 WebSocket 接続では、サーバーが処理する任意のカスタム サブプロトコルを使用して接続できます。また、サービスでサポートされているサブプロトコル `json.webpubsub.azure.v1` を使用して接続することもでき、これにより、クライアントが pub/sub を直接実行できます。 詳細については、「[クライアント プロトコル](concept-service-internals.md#client_protocol)」を参照してください。

2. サービスは、さまざまなクライアント イベントで **CloudEvents HTTP プロトコル** を使用してサーバーを呼び出します。 [**CloudEvents**](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) は、Cloud Native Computing Foundation (CNCF) によってホストされるイベントの構造とメタデータ記述の標準化されたプロトコルに依存しない定義です。 詳細については、「[サーバー プロトコル](concept-service-internals.md#server_protocol)」を参照してください。

3. サーバーは、REST API を使用してサービスを呼び出し、クライアントにメッセージを送信したり、接続されているクライアントを管理したりできます。 詳細については、「[サーバー プロトコル](concept-service-internals.md#server_protocol)」を参照してください