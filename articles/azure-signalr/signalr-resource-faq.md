---
title: Azure SignalR Service についてよく寄せられる質問
description: トラブルシューティングや一般的な使用シナリオなど、Azure SignalR Service に関してよく寄せられる質問への回答を示します。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489563"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service の FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service は運用環境で利用できますか?

はい。
一般公開の発表については、「[Azure SignalR サービスの一般公開](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/)」を参照してください。 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) は完全にサポートされています。

ASP.NET SignalR のサポートはまだ "*パブリック プレビュー*" 段階です。 [コード例はこちらに示されています](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)。

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>クライアント接続が閉じられ、"利用可能なサーバーがありません" というエラー メッセージが表示されます。 これはどういうことですか?

このエラーは、クライアントが Azure SignalR Service にメッセージを送信している場合にのみ発生します。

アプリケーション サーバーがなく、Azure SignalR Service REST API のみを使用している場合、この動作は "*仕様*" です。
サーバーレス アーキテクチャの場合、クライアント接続は "*リッスン*" モードになり、Azure SignalR Service にメッセージは送信されません。
[REST API の詳細については、こちら](./signalr-quickstart-rest-api.md)を参照してください。

アプリケーション サーバーがある場合、このエラー メッセージはアプリケーション サーバーが Azure SignalR Service インスタンスに接続されていないことを意味します。

次の原因が考えられます。
- Azure SignalR Service と接続されているアプリケーション サーバーがない。 アプリケーション サーバーのログを調べて、接続エラーの可能性を確認してください。 これは、アプリケーション サーバーが複数ある高可用性設定ではまれです。
- Azure SignalR Service インスタンスとの接続に問題がある。 この問題は一時的なものであり、インスタンスは自動的に復旧します。
1 時間以上続く場合は、[GitHub で問題をオープン](https://github.com/Azure/azure-signalr/issues/new)するか、[Azure でサポート要求を作成](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)してください。

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>複数のアプリケーション サーバーが存在する場合、クライアント メッセージはすべてのサーバーに送信されますか? それとも 1 つのサーバーだけに送信されますか?

クライアントとアプリケーション サーバーとの間には 1 対 1 のマッピングが存在します。 クライアントからのメッセージは常に同じアプリケーション サーバーに送信されます。

このマッピングは、クライアントまたはアプリケーション サーバーが切断されるまで保持されます。

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>いずれかのアプリケーション サーバーがダウンした場合に、それを見つけたり、通知を受け取ったりするにはどうすればよいですか?

Azure SignalR Service によって、アプリケーション サーバーからのハートビートが監視されます。
指定された期間ハートビートを受信しなかった場合、アプリケーション サーバーはオフラインと見なされます。 このアプリケーション サーバーにマップされているすべてのクライアント接続が切断されます。

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>ASP.NET Core SignalR SDK から Azure SignalR Service SDK に切り替えると、カスタムの `IUserIdProvider` で例外がスローされるのはなぜですか?

ASP.NET Core SignalR SDK と Azure SignalR Service SDK では、`IUserIdProvider` を呼び出すときのパラメーター `HubConnectionContext context` が異なります。

ASP.NET Core SignalR の場合、`HubConnectionContext context` はすべてのプロパティに有効な値が指定された物理クライアント接続からのコンテキストです。

Azure SignalR Service SDK の場合、`HubConnectionContext context` は論理クライアント接続からのコンテキストです。 物理クライアントは Azure SignalR Service インスタンスに接続されるため、限られた数のプロパティのみが指定されます。

現在のところ、`HubConnectionContext.GetHttpContext()` と `HubConnectionContext.User` のみがアクセス可能です。
[ソース コードを確認](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)できます。

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>ASP.NET Core SignalR を使用して、サーバー側の Azure SignalR Service で使用可能な転送を構成できますか? たとえば、WebSocket 転送を無効にすることはできますか?

いいえ。

Azure SignalR Service は、ASP.NET Core SignalR が既定でサポートする 3 つの転送をすべて提供します。 これは構成できません。 Azure SignalR Service によって、すべてのクライアント接続の接続と転送が処理されます。

「[ASP.NET Core SignalR の構成](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)」に記載されているように、クライアント側の転送を構成することができます。

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Azure portal に表示されるメッセージ数や接続数などのメトリックの意味を教えてください。 集計の種類としてどれを選べばよいでしょうか?

これらのメトリックの計算の詳細については、「[Azure SignalR Service でのメッセージと接続](signalr-concept-messages-and-connections.md)」を参照してください。

Azure SignalR Service リソースの概要ペインでは、適切な集計の種類が既に選択されています。 メトリック ペインに移動すると、参照として [Azure SignalR Service でのメッセージと接続](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr)に対して集計の種類を使用できます。

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>`Default`、`Serverless`、`Classic` の各サービス モードの意味を教えてください。 どのように選べばよいでしょうか?

モードについては、次の情報を参照してください。
* `Default` モードにはハブ サーバーが "*必要*" です。 このモードでは、Azure SignalR Service によって、その接続先のハブ サーバー接続にクライアント トラフィックがルーティングされます。 Azure SignalR Service により、接続先のハブ サーバーが確認されます。 サービスでは、接続先のハブ サーバーを検出できない場合、受信クライアント接続が拒否されます。 このモードでは、*Management API* を使用し、Azure SignalR Service 経由で直接、接続先のクライアントを管理することもできます。
* `Serverless` モードでは、どのサーバー接続も許可 "*されません*"。 つまり、すべてのサーバー接続が拒否されます。 すべてのクライアントがサーバーレス モードである必要があります。 クライアントは Azure SignalR Service に接続されます。ユーザーは通常、サーバーレス テクノロジ (*Azure Functions* など) を使用して、ハブのロジックを処理します。 Azure SignalR Service のサーバーレス モードを使用する[簡単な例を参照してください](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu)。
* `Classic` モードは、混在した状態です。 ハブにサーバー接続がある場合、新しいクライアントはハブ サーバーにルーティングされます。 ない場合、クライアントはサーバーレス モードになります。 

  これにより、問題が発生する可能性があります。 たとえば、すべてのサーバー接続が少しの間失われ、一部のクライアントがハブ サーバーにルーティングされずに、サーバーレス モードになります。

モードの選択に関するガイドラインをいくつか以下に示します。
- ハブ サーバーがない場合は、`Serverless` を選択します。
- すべてのハブにハブ サーバーがある場合は、`Default` を選択します。
- ハブ サーバーがあるハブとないハブがある場合は、`Classic` を選択できますが、これによって問題が発生する可能性があります。 その場合は、2 つのインスタンス (1 つは `Serverless` で、もう 1 つは `Default`) を作成することをお勧めします。

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Azure SignalR Service を使用する場合と ASP.NET SignalR を使用する場合で、機能上の違いはありますか?
Azure SignalR Service を使用する場合、ASP.NET SignalR のいくつかの API と機能はサポートされません。
- クライアントとハブとの間で任意の状態 (一般に `HubState` と呼ばれる) を渡す機能はサポートされません。
- `PersistentConnection` クラスはサポートされません。
- "*Forever Frame の転送*" はサポートされません。
- クライアントがオフラインの場合、Azure SignalR Service ではクライアントに送信されたメッセージが再生されなくなりました。
- Azure SignalR Service を使用する場合、1 つのクライアント接続のトラフィックは、接続期間中、常に 1 つのアプリ サーバー インスタンスにルーティングされます ("*スティッキー*" ともいう)。

ASP.NET SignalR のサポートは互換性に重点が置かれているため、ASP.NET Core SignalR の一部の新機能はサポートされません。 たとえば、*MessagePack* や *Streaming* は ASP.NET Core SignalR アプリケーションでのみ使用できます。

さまざまなサービス モード (`Classic`、`Default`、`Serverless`) に対して、Azure SignalR Service を構成できます。 ASP.NET では、`Serverless` モードはサポートされません。 また、データプレーンの REST API もサポートされません。

## <a name="where-does-my-data-reside"></a>データはどこに存在するか?

Azure SignalR Service は、データ プロセッサ サービスとして機能します。 お客様のコンテンツは格納されず、データ所在地は仕様で含まれています。 診断用の Azure Storage など、他の Azure サービスを Azure SignalR Service と併用する場合は、データ所在地を Azure リージョンで維持する方法に関するガイダンスについて、[こちらのホワイト ペーパー](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)をご覧ください。
