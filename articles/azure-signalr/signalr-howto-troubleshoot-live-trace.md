---
title: Azure SignalR サービスのライブ トレース ツールを使用する方法
description: Azure SignalR サービスのライブ トレース ツールを使用する方法について説明します
author: wanlwanl
ms.author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: 1f361b8a785f108f985dbae23290150c46170daa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705303"
---
# <a name="how-to-use-live-trace-tool-for-azure-signalr-service"></a>Azure SignalR サービスのライブ トレース ツールを使用する方法

ライブ トレース ツールは、Azure SignalR サービスでライブ トレースをキャプチャして表示する単一の Web アプリケーションです。 他のサービスに依存せずにリアルタイムでライブ トレースを収集できます。
ライブ トレース機能は 1 クリックで有効または無効にできます。 関心のある任意のログ カテゴリを選択することもできます。

> [!NOTE]
> ライブ トレースは送信メッセージとしてカウントされることにご注意ください。

## <a name="launch-the-live-trace-tool"></a>ライブ トレース ツールを起動する

1. Azure Portal にアクセスします。
2. **[ライブ トレースを有効にする]** をオンにします。
3. ツール バーの **[保存]** ボタンをクリックし、変更が有効になるまで待機します。
4. Azure Web PubSub サービス インスタンスの **[診断設定]** ページで、 **[Open Live Trace Tool]\(ライブ トレース ツールを開く\)** を選択します。 

    :::image type="content" source="media/signalr-howto-troubleshoot-live-trace/live-traces-with-live-trace-tool.png" alt-text="ライブ トレース ツールを起動するスクリーンショット。":::

## <a name="capture-live-traces"></a>ライブ トレースをキャプチャする

ライブ トレース ツールには、トラブルシューティング用のライブ トレースをキャプチャする際に役立ついくつかの基本的な機能が用意されています。

* **[キャプチャ]** : ライブ トレース ツールを使用して、Azure Web PubSub インスタンスからリアルタイム ライブ トレースのキャプチャを開始します。
* **[クリア]** : キャプチャしたリアルタイム ライブ トレースをクリアします。
* **[エクスポート]** : ライブ トレースをファイルにエクスポートします。 現在サポートされているファイル形式は CSV ファイルです。
* **[ログ フィルター]** : ライブ トレース ツールでは、キャプチャしたリアルタイム ライブ トレースを特定の 1 つのキー ワードでフィルター処理できます。 一般的な区切り記号 (たとえばスペースやコンマ、セミコロンなど) は、キー ワードの一部として扱われます。 
* **[Status]\(状態\)** : ライブ トレース ツールが特定のインスタンスと接続されているか、切断されているかの状態を示しています。

:::image type="content" source="./media/signalr-howto-troubleshoot-live-trace/live-trace-tool-capture.png" alt-text="ライブ トレース ツールを使用してライブ トレースをキャプチャするスクリーンショット。":::

ライブ トレース ツールによってキャプチャされたリアルタイム ライブ トレースには、トラブルシューティングのための詳細情報が含まれています。 

| 名前 | 説明 |
| ------------ |  ------------------------ | 
| Time | ログ イベント時間 |
| ログ レベル | ログ イベントのレベル (トレース/デバッグ/情報/警告/エラー) |
| イベント名 | イベントの操作名 |
| Message | ログ イベントの詳細なメッセージ |
| 例外 | Azure Web PubSub サービスの実行時の例外 |
| ハブ | ユーザー定義のハブ名 |
| 接続 ID | 接続の ID |
| 接続の種類 | 接続の種類。 使用できる値は、`Server` (サーバーとサービスの接続) と `Client` (クライアントとサービスの接続) です|
| User ID | ユーザーの ID |
| IP | クライアントの IP アドレス |
| サーバー スティッキー | クライアントのルーティング モード。 使用できる値は、`Disabled`、`Preferred`、`Required` です。 詳細については、「[ServerStickyMode](https://github.com/Azure/azure-signalr/blob/master/docs/run-asp-net-core.md#serverstickymode)」を参照してください |
| トランスポート | クライアントが HTTP 要求を送信するために使用できるトランスポート。 使用できる値は、`WebSockets`、`ServerSentEvents`、`LongPolling` です。 詳細については、「[HttpTransportType](/dotnet/api/microsoft.aspnetcore.http.connections.httptransporttype)」を参照してください |
| メッセージ トレース ID | メッセージの一意識別子 |
| ルート テンプレート | API のルート テンプレート |
| HTTP メソッド | HTTP メソッド (POST、GET、PUT、DELETE) |
| URL | ユニフォーム リソース ロケーター |
| トレース ID | 要求を表す一意識別子 |
| 状態コード | HTTP 応答コード |
| 期間 | 要求の受信から処理までの期間 |
| ヘッダー | HTTP 要求または応答を使用してクライアントとサーバーによって渡される追加情報 |
| 呼び出し ID | 呼び出しを表す一意識別子 (ASP.NET SignalR でのみ使用可能) |
| メッセージ型 | メッセージの種類 (BroadcastDataMessage、JoinGroupMessage、LeaveGroupMessage、...) |

## <a name="next-steps"></a>次の手順

このガイドでは、ライブ トレース ツールを使用する方法について学習しました。 一般的な問題の処理方法について学習することもできます。
* トラブルシューティング ガイド: ライブ トレースに基づいて一般的な問題のトラブルシューティングを行う方法については、[トラブルシューティング ガイド](./signalr-howto-troubleshoot-guide.md)をご覧ください。
* トラブルシューティング方法: 自己診断を行って、根本原因を直接見つけたり、問題を絞り込んだりするには、[トラブルシューティング方法の概要に関するページ](./signalr-howto-troubleshoot-method.md)をご覧ください。