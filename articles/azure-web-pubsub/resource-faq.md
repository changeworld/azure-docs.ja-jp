---
title: Azure Web PubSub サービスの FAQ
description: Azure Web PubSub サービスに関してよく寄せられる質問への回答を紹介します。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 11/11/2021
ms.openlocfilehash: dc8dd5e25a6b6aeb94d674156af2edd9dbcd008d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345605"
---
# <a name="azure-web-pubsub-service-faq"></a>Azure Web PubSub サービスの FAQ

これは、Azure Web PubSub サービスの FAQ です。 

## <a name="is-azure-web-pubsub-service-ready-for-production-use"></a>Azure Web PubSub サービスは運用環境に対応していますか?
はい。Azure Web PubSub サービスは一般提供されています。

## <a name="how-do-i-choose-between-azure-signalr-service-and-azure-web-pubsub-service"></a>Azure SignalR Service と Azure Web PubSub サービスのどちらかを選択するにはどうすればよいですか?

[Azure SignalR Service](https://azure.microsoft.com/services/signalr-service) と [Azure Web PubSub サービス](https://azure.microsoft.com/services/web-pubsub)はどちらも、大規模で高可用性を備えたリアルタイムの Web アプリケーションを簡単に構築するのに役立ち、お客様はメッセージング インフラストラクチャの管理ではなくビジネス ロジックに集中できます。 一般に、既に SignalR ライブラリを使用してリアルタイム アプリケーションを構築している場合は、Azure SignalR Service を選択できます。 代わりに、WebSocket とパブリッシュ/サブスクライブ パターンに基づいてリアルタイム アプリケーションを構築する汎用ソリューションを探している場合は、Azure Web PubSub サービスを選択できます。 Azure Web PubSub サービスは、Azure SignalR Service に代わるものでは **ありません**。 これらは異なるシナリオをターゲットにしています。

以下のような場合は、Azure SignalR Service の方が適しています。  

- ASP.NET または ASP.NET Core SignalR を既に使用していて、主に .NET を使用しているか、.NET エコシステム (Blazor など) と統合する必要がある。
- お使いのプラットフォームで使用できる SignalR クライアントがある。 
- さまざまな呼び出しパターン (RPC やストリーミング)、トランスポート (WebSocket、サーバー送信イベント、および長いポーリング) をサポートする確立されたプロトコルと、自分の代わりに接続の有効期間を管理するクライアントが必要である。 

次のような状況には、Azure Web PubSub サービスの方が適しています。  

- WebSocket テクノロジに基づいてリアルタイム アプリケーションを構築するか、WebSocket を介してパブリッシュ/サブスクライブする必要がある。
- 独自のサブプロトコルを構築したり、WebSocket を介して既存の高度なプロトコル (MQTT、WebSocket 経由の AMQP など) を使用したい。 
- 構成されたバックエンドを経由せずにクライアントにメッセージを送信するといった、軽量のサーバーを探している。  

##  <a name="where-does-my-data-reside"></a>データはどこに存在するか?

Azure Web PubSub サービスは、データ プロセッサ サービスとして機能します。 お客様のコンテンツは格納されず、データ所在地は仕様で含まれています。 診断用の Azure Storage など、他の Azure サービスを Azure Web PubSub サービスと併用する場合は、データ所在地を Azure リージョンで維持する方法に関するガイダンスについて、[こちらのホワイト ペーパー](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)をご覧ください。
