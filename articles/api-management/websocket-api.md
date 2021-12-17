---
title: Azure portal を使用して WebSocket API をインポートする | Microsoft Docs
titleSuffix: ''
description: API Management における WebSocket をサポートする方法、WebSocket API の追加、WebSocket の制限について説明します。
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.date: 11/2/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 027a87a7502f551b7fb97d52a732a90bc0b8aa45
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065686"
---
# <a name="import-a-websocket-api"></a>Websocket API のインポート

API Management の WebSocket API ソリューションを使用すると、API Management を使用して WebSocket API と REST API の両方を管理、保護、監視、公開し、すべての API を検出して使用するための中央ハブを提供できます。 API 発行元は、API Management で以下を介して WebSocket API を素早く追加できます。
* Azure portal での単純なジェスチャ、および、 
* Management API と Azure Resource Manager。 

[JWT 検証](./api-management-access-restriction-policies.md#ValidateJWT)など、既存のアクセス制御ポリシーを適用することで、WebSocket API をセキュリティで保護できます。 Azure portal と開発者ポータルの両方で API テスト コンソールを使用して WebSocket API をテストすることもできます。 既存の監視機能を基に構築された API Management により、WebSocket API の監視とトラブルシューティングを行うメトリックとログが得られます。 

この記事では、次のことについて説明します。
> [!div class="checklist"]
> * WebSocket パススルー フローについて説明します。
> * API Management インスタンスに WebSocket API を追加します。
> * WebSocket API をテストします。
> * WebSocket API のメトリックとログを表示します。
> * WebSocket API の制限事項について説明します。

## <a name="prerequisites"></a>前提条件

- 既存の API Management インスタンスがある。 [まだない場合は、作成してください](get-started-create-service-instance.md)。
- WebSocket API。 

## <a name="websocket-passthrough"></a>WebSocket パススルー

API Management により WebSocket パススルーがサポートされています。 

:::image type="content" source="./media/websocket-api/websocket-api-passthrough.png" alt-text="WebSocket パススルー フローの視覚的な図":::

WebSocket パススルー中に、クライアント アプリケーションは API Management ゲートウェイとの WebSocket 接続を確立し、対応するバックエンド サービスとの接続を確立します。 次に API Management で、WebSocket クライアントとサーバーのメッセージがプロキシ処理されます。

1. クライアント アプリケーションにより WebSocket ハンドシェイク要求が APIM ゲートウェイに送信され、onHandshake 操作が呼び出されます。
1. APIM ゲートウェイで、WebSocket ハンドシェイク要求が対応するバックエンド サービスに送信されます。
1. バックエンド サービスで、WebSocket への接続がアップグレードされます。
1. APIM ゲートウェイで、対応する WebSocket への接続がアップグレードされます。
1. 接続のペアが確立されると、APIM によりクライアントアプリケーションとバックエンドサービスの間でメッセージが仲介されます。
1. クライアント アプリケーションで、APIM ゲートウェイにメッセージが送信されます。
1. APIM ゲートウェイで、メッセージがバックエンド サービスに転送されます。
1. バックエンドサービスで、APIM ゲートウェイにメッセージが送信されます。
1. APIM ゲートウェイで、メッセージがクライアント アプリケーションに転送されます。
1. どちらかのサイドが切断されると、APIM により対応する接続が終了されます。

> [!NOTE]
> クライアント側とバックエンド側の接続は、一対一のマッピングで構成されます。 

## <a name="onhandshake-operation"></a>onHandshake 操作

[WebSocket プロトコル](https://tools.ietf.org/html/rfc6455)ごとに、クライアント アプリケーションでバックエンド サービスとの WebSocket 接続の確立が試行されると、最初に、[開始ハンドシェイク要求](https://tools.ietf.org/html/rfc6455#page-6)が送信されます。 API Management 内の各 WebSocket API には、onHandshake 操作があります。 onHandshake は、変更不可で解除できない、自動的に作成されるシステム操作です。 onHandshake 操作を使用すると、API パブリッシャーでこれらのハンドシェイク要求がインターセプトされ、API Management ポリシーをそれらに適用できます。

:::image type="content" source="./media/websocket-api/onhandshake-screen.png" alt-text="onHandshake 画面の例":::

## <a name="add-a-websocket-api"></a>WebSocket API の追加

1. API Management インスタンスに移動します。
1. サイド ナビゲーション メニューの **[API]** セクションで、 **[API]** を選択します。
1. **[Define a new API]\(新しい API の定義\)** で、**WebSocket** アイコンを選択します。
1. ダイアログ ボックスで **[完全]** を選択し、必要なフォーム フィールドを入力します。

    | フィールド | 説明 |
    |----------------|-------|
    | 表示名 | WebSocket API を表示するときに使用する名前。 |
    | 名前 | WebSocket API の未加工の名前。 表示名を入力すると自動的に設定されます。 |
    | WebSocket URL | Websocket 名のベース URL。 例: *ws://example.com/your-socket-name* |
    | URL スキーム | 既定値を受け入れます |
    | API URL サフィックス| この API Management インスタンスでこの特定の API を識別するための URL サフィックスを追加します。 この APIM インスタンス内で一意である必要があります。 |
    | 製品 | WebSocket API を製品に関連付け、発行します。 |
    | ゲートウェイ | WebSocket API を既存のゲートウェイに関連付けます。 |
 
1. **[作成]** をクリックします。

## <a name="test-your-websocket-api"></a>WebSocket API をテストする

1. ご自身の WebSocket API に移動します。
1. WebSocket API 内で、onHandshake 操作を選択します。
1. **[テスト]** タブを選択して、テスト コンソールにアクセスします。 
1. 必要に応じて、WebSocket ハンドシェイクに必要なクエリ文字列パラメーターを指定します。

    :::image type="content" source="./media/websocket-api/test-websocket-api.png" alt-text="API テストの例":::

1. **[Connect]** をクリックします。
1. **[出力]** に接続状態を表示します。
1. **[ペイロード]** に値を入力します。 
1. **[送信]** をクリックします。
1. **[出力]** に受信メッセージを表示します。
1. 前の手順を繰り返して、さまざまなペイロードをテストします。
1. テストが完了したら、 **[切断]** を選択します。

## <a name="view-metrics-and-logs"></a>メトリックとログを表示する

API Management と Azure Monitor の標準機能を使用して、WebSocket API を[監視](api-management-howto-use-azure-monitor.md)します。

* Azure Monitor の API メトリックを表示する
* 必要に応じて、診断設定を有効にし、API Management のゲートウェイ ログを収集して表示します。これには、WebSocket API の操作が含まれます

たとえば、次のスクリーンショットは、**ApiManagementGatewayLogs** テーブルからのコード `101` が含まれる最近の WebSocket API の応答を示したものです。 これらの結果では、TCP から WebSocket プロトコルへの要求の切り替えが正常に行われたことが示されています。

:::image type="content" source="./media/websocket-api/query-gateway-logs.png" alt-text="WebSocket API 要求のクエリ ログ":::

## <a name="limitations"></a>制限事項

以下に示すのは、API Management での WebSocket サポートの現在の制限です。

* WebSocket API は、従量課金レベルではまだサポートされていません。
* WebSocket API は、[セルフホステッド ゲートウェイ](./self-hosted-gateway-overview.md)ではまだサポートされていません。
* Azure CLI、PowerShell、SDK では、現在 WebSocket API の管理操作はサポートされていません。

### <a name="unsupported-policies"></a>サポートされていないポリシー

次のポリシーは onHandshake 操作でサポートされておらず、適用することはできません。
*  モック応答
* キャッシュからの取得
* キャッシュへの格納
* クロスドメイン呼び出しの許可
* CORS
* JSONP
*  要求メソッドを設定する
* 本文の設定
* XML から JSON への変換
* JSON から XML への変換
* XSLT を使用した XML の変換
* コンテンツの検証
* パラメーターを検証する:
* ヘッダーを検証する
* 状態コードを検証する

> [!NOTE]
> より高いスコープ (グローバルまたは製品) でポリシーを適用し、ポリシーを介して WebSocket API に継承された場合、実行時にスキップされます。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [公開された API の変換と保護](transform-api.md)
