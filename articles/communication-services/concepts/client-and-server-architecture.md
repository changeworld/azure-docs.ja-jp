---
title: クライアントとサーバーのアーキテクチャ
titleSuffix: An Azure Communication Services concept document
description: Communication Services のアーキテクチャについて説明します。
author: probableprime
manager: mikben
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1346c9f6505c03ccebb2d2e2dc33e899050bfe20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672022"
---
# <a name="client-and-server-architecture"></a>クライアントとサーバーのアーキテクチャ

このページでは、さまざまな Azure Communication Service シナリオにおける一般的なアーキテクチャ コンポーネントとデータフローについて説明します。 関連コンポーネントは次のとおりです。

1. **クライアント アプリケーション。** この Web サイトまたはネイティブ アプリケーションは、エンドユーザーが通信を行うために利用します。 Azure Communication Services は、複数のブラウザーおよびアプリケーション プラットフォーム用の [SDK クライアント ライブラリ](sdk-options.md)を提供します。 コア SDK に加えて、[UI ライブラリ](https://aka.ms/acsstorybook)を使用してブラウザー アプリの開発を高速化できます。
1. **ID 管理サービス。**  このサービス機能を使用して、ユーザーとビジネス ロジック内のその他の概念を Azure Communication Services にマップするとともに、必要に応じてユーザーのトークンを作成することもできます。
1. **Call 管理サービス。**  このサービス機能を使用して、音声通話とビデオ通話を管理および監視できます。  このサービスでは、Calling Automation SDK と REST API を使用して、通話の作成やユーザーの招待、電話番号への通話の発信、音声の再生、DMTF トーンのリッスンを行うとともに、他の多くの通話機能を活用できます。


## <a name="user-access-management"></a>ユーザー アクセス管理

Azure Communication Services クライアントが Communication Services のリソースに安全にアクセスするためには、`user access tokens` が必要です。 トークンとその生成に必要な接続文字列またはマネージド ID の機密性により、`User access tokens` は信頼できるサービスによって生成および管理される必要があります。 アクセス トークンを適切に管理できないと、リソースの誤用によって追加料金が発生する可能性があります。

:::image type="content" source="../media/scenarios/architecture_v2_identity.svg" alt-text="ユーザー アクセス トークンのアーキテクチャを示す図。":::

### <a name="dataflows"></a>データフロー
1. ユーザーがクライアント アプリケーションを起動します。 このアプリケーションの設計とユーザーの認証スキームは、開発者が制御できます。
2. クライアント アプリケーションが、ID 管理サービスに接続します。 ID 管理サービスは、ユーザーおよびその他のアドレス指定可能なオブジェクト (サービスやボットなど) と、Azure Communication Service ID の間のマッピングを保持します。
3. ID 管理サービスが、該当する ID のユーザー アクセス トークンを作成します。 過去に Azure Communication Services ID が割り当てられていない場合は、新しい ID が作成されます。  

### <a name="resources"></a>リソース
- **概念:** [ユーザー ID](identity-model.md)
- **クイックスタート:** [アクセス トークンを作成して管理する](../quickstarts/access-tokens.md)
- **チュートリアル:** [Azure Functions を使用して ID 管理サービスを構築する](../tutorials/trusted-service-tutorial.md)

> [!IMPORTANT]
> 簡略化のために、以降のアーキテクチャ フローではユーザー アクセス管理とトークン配布については説明しません。


## <a name="calling-a-user-without-push-notifications"></a>プッシュ通知を使用しないユーザーへの通話の発信
最も単純な音声およびビデオ通話のシナリオには、プッシュ通知を使用しない、フォアグラウンドでの別のユーザーへの通話の発信があります。

:::image type="content" source="../media/scenarios/architecture_v2_calling_without_notifications.svg" alt-text="プッシュ通知を使用せずに通話の発信を行う Communication Services アーキテクチャを示す図。":::

### <a name="dataflows"></a>データフロー

1. 受け入れ側のユーザーは Call クライアントを初期化して、通話の着信を受信できるようにします。
2. 開始側のユーザーには、通話相手のユーザーの Azure Communication Services ID が必要となります。 一般的なエクスペリエンスでは、ユーザーの友人と、関連する Azure Communication Service ID とを照合する、ID 管理サービスによって管理される *フレンド リスト* がある場合があります。
3. 開始側のユーザーが Call クライアントを初期化し、リモート ユーザーに発信します。
4. 受け入れ側のユーザーには、Calling SDK を通じて通話の着信が通知されます。
5. ユーザーは音声とビデオを使用して互いに通話します。

### <a name="resources"></a>リソース
- **概念:** [Calling の概要](voice-video-calling/calling-sdk-features.md)
- **クイックスタート:** [音声通話をアプリに追加する](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- **クイックスタート:** [ビデオ通話をアプリに追加する](../quickstarts/voice-video-calling/get-started-with-video-calling.md)
- **ヒーロー サンプル:** [Web、iOS、および Android のグループ通話](../samples/calling-hero-sample.md)


## <a name="joining-a-user-created-group-call"></a>ユーザーが作成したグループ通話に参加する
ユーザーに、明示的な招待なしで通話に参加して欲しい場合があるかもしれません。 たとえば、通話が関連付けられた *ソーシャル スペース* があり、ユーザーは都合のよいときに通話に参加するというケースが考えられます。 この最初のデータフローでは、クライアントによって最初に作成される通話を示しています。

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_client_driven.svg" alt-text="アウトオブバンド シグナリングの通話の発信を行う Communication Services アーキテクチャを示す図。":::

### <a name="dataflows"></a>データフロー
1. 開始側のユーザーが Call クライアントを初期化し、グループ通話を作成します。
2. 開始側のユーザーは、グループ通話 ID を Call 管理サービスに共有します。
3. Call 管理サービスによって、他のユーザーに通話 ID が共有されます。 たとえば、アプリケーションがスケジュールされたイベントを中心にしている場合、グループ通話 ID は、スケジュールされたイベントのデータ モデルの属性である可能性があります。
4. 他のユーザーは、グループ通話 ID を使用して通話に参加します。
5. ユーザーは音声とビデオを使用して互いに通話します。


## <a name="joining-a-scheduled-teams-call"></a>スケジュールされた Teams の通話への参加
Azure Communication Service アプリケーションは、Teams の通話に参加できます。 これは、企業 - 消費者間のさまざまなシナリオに最適です。消費者はカスタム アプリケーションとカスタム ID を利用するのに対し、企業側では Teams が使用されます。

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_teams_driven.svg" alt-text="Teams の会議に参加するための Communication Services アーキテクチャを示す図。":::


### <a name="dataflows"></a>データフロー
1. Call 管理サービスは、[Graph API](/graph/api/resources/onlinemeeting?view=graph-rest-1.0) を使用してグループ通話を作成します。 別のパターンとしては、エンド ユーザーが [Bookings](https://www.microsoft.com/microsoft-365/business/scheduling-and-booking-app)、Outlook、Teams、または Microsoft 365 エコシステムの別のスケジュール エクスペリエンスを使用してグループ通話を作成するケースがあります。
2. Call 管理サービスは、Teams の通話の詳細を Azure Communication Service クライアントに共有します。
3. 通常、Teams ユーザーが必ず通話に参加し、外部ユーザーをロビー経由で参加できるようにする必要があります。 ただし、このエクスペリエンスは Teams のテナント構成および特定の会議設定の影響を受けます。
4. Azure Communication Service ユーザーは、手順 2 で受け取った詳細情報を使用して Call クライアントを初期化し、Teams の会議に参加します。
5. ユーザーは音声とビデオを使用して互いに通話します。

### <a name="resources"></a>リソース
- **概念:** [Teams の相互運用性](teams-interop.md)
- **クイックスタート:** [Teams の会議に参加する](../quickstarts/voice-video-calling/get-started-teams-interop.md)
