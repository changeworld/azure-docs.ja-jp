---
title: Azure Web PubSub サービス診断ログを使用してトラブルシューティングを行う方法
description: 診断ログを取得してトラブルシューティングを行う方法について説明します
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/22/2021
ms.openlocfilehash: 119591c4a337cf622b95fa4ed70a7508d8acb99e
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963094"
---
# <a name="how-to-troubleshoot-with-diagnostic-logs"></a>診断ログを使用したトラブルシューティングの方法

この攻略ガイドでは、診断ログを取得するためのオプションと、それらを使用してトラブルシューティングを行う方法について説明します。

## <a name="whats-the-diagnostic-logs"></a>診断ログとは

診断ログでは、Azure Web PubSub サービス インスタンスに対する接続とメッセージング情報の豊富なビューが提供されます。 これは、問題の識別、接続の追跡、メッセージのトレース、分析に使用できます。

ログには、接続ログとメッセージング ログの 2 種類があります。

### <a name="connectivity-logs"></a>接続ログ

接続ログでは、Azure Web PubSub ハブ接続に関する詳細情報が提供されます。 たとえば、基本情報 (ユーザー ID、接続 ID など) やイベント情報 (接続、切断、中止イベントなど) です。 そのため、接続ログは接続に関連する問題のトラブルシューティングに役立ちます。 

### <a name="messaging-logs"></a>メッセージング ログ

メッセージング ログでは、Azure Web PubSub サービス経由で送受信される Azure Web PubSub ハブ メッセージのトレース情報が提供されます。 たとえば、メッセージのトレース ID やメッセージの種類などです。 メッセージは通常、サービスへの到着時またはサービスからの発信時に記録されます。 メッセージング ログは、メッセージに関連する問題のトラブルシューティングに役立ちます。 

## <a name="capture-diagnostic-logs-with-azure-web-pubsub-service-live-trace-tool"></a>Azure Web PubSub サービス ライブ トレース ツールを使用した診断ログのキャプチャ 

Azure Web PubSub サービス ライブ トレース ツールは、リアルタイムで診断ログを収集する機能を備えており、お客様の開発環境でのトレースに役立ちます。 ライブ トレース ツールでは、接続ログとメッセージング ログの両方をキャプチャできます。

> [!NOTE]
> ライブ トレース ツールによってキャプチャされたリアルタイム診断ログは、メッセージ (送信トラフィック) として課金されます。

> [!NOTE]
> Free レベルとして作成された Azure Web PubSub サービス インスタンスには、メッセージ (送信トラフィック) に 1 日当たりの制限があります。

### <a name="launch-the-live-trace-tool"></a>ライブ トレース ツールを起動する

1. Azure Portal にアクセスします。 
1. Azure Web PubSub サービス インスタンスの **[診断設定]** ページで、 **[Open Live Trace Tool]\(ライブ トレース ツールを開く\)** を選択します。 

    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-logs-with-live-trace-tool.png" alt-text="ライブ トレース ツールを起動します。":::

### <a name="capture-the-diagnostic-logs"></a>診断ログをキャプチャする

ライブ トレース ツールでは、トラブルシューティングのために診断ログをキャプチャするのに役立ついくつかの基本的な機能が提供されます。

* **[Capture]\(キャプチャ\)** : ライブ トレース ツールを使用して、Azure Web PubSub インスタンスからリアルタイム診断ログのキャプチャを開始します。
* **[Clear]\(クリア\)** : キャプチャしたリアルタイム診断ログをクリアします。
* **[Log filter]\(ログ フィルター\)** : ライブ トレース ツールを使用すると、キャプチャしたリアルタイム診断ログを特定の 1 つのキー ワードでフィルター処理できます。 一般的な区切り記号 (たとえばスペースやコンマ、セミコロンなど) は、キー ワードの一部として扱われます。 
* **[Status]\(状態\)** : ライブ トレース ツールが特定のインスタンスと接続されているか、切断されているかの状態を示しています。

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/live-trace-tool-capture.png" alt-text="ライブ トレース ツールを使用して診断ログをキャプチャします。":::

ライブ トレース ツールによってキャプチャされたリアルタイム診断ログには、トラブルシューティングのための詳細情報が含まれています。 

| 名前 | 説明 |
| ------------ |  ------------------------ | 
| Time | ログ イベント時間 |
| ログ レベル | ログ イベントのレベル (トレース/デバッグ/情報/警告/エラー) |
| イベント名 | イベントの操作名 |
| Message | ログ イベントの詳細なメッセージ |
| 例外 | Azure Web PubSub サービスの実行時の例外 |
| ハブ | ユーザー定義のハブ名 |
| 接続 ID | 接続の ID |
| User ID | ユーザーの ID |
| IP | クライアントの IP アドレス | 

Azure Web PubSub サービスが GA になった後には、ライブ トレース ツールでログを特定の形式でエクスポートして、トラブルシューティングのために他のユーザーと共有することもできるようになります。 

## <a name="capture-diagnostic-logs-with-azure-monitor"></a>Azure Monitor を使用して診断ログをキャプチャする

[Azure Monitor](https://azure.microsoft.com/services/monitor/)、[Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)、および [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) との統合による診断ログのキャプチャは、パブリック プレビューではサポートされていません。

## <a name="troubleshoot-with-the-diagnostic-logs"></a>診断ログを使用したトラブルシューティング

接続が予期せず増加した、または切断された状況が発生した場合は、診断ログを利用してトラブルシューティングできます。 多くの場合、一般的な問題は予期しない接続数の変化、接続数の接続上限への到達、承認エラーに関するものです。

### <a name="unexpected-connection-number-changes"></a>予期しない接続数の変化

#### <a name="unexpected-connection-dropping"></a>予期しない接続の削除

接続が切断されると、診断ログによって `operationName` を `ConnectionAborted` または `ConnectionEnded` として、その切断イベントが記録されます。

`ConnectionAborted` と `ConnectionEnded` の違いは、`ConnectionEnded` はクライアントまたはサーバー側によってトリガーされた予期された切断であるという点です。 一方、`ConnectionAborted` は多くの場合、予期しない接続の削除イベントであり、中断の理由が `message` に示されます。

次の表に、中断の理由を示します。

| 理由 | 説明 |
| ------- | ------- |
| 接続数が上限に達している | 接続数が現在の価格レベルの上限に達しています。 サービス ユニットのスケールアップを検討してください
| サービスの再読み込み、再接続 | Azure Web PubSub サービスを再度読み込みしています。 Azure Web PubSub サービスへの独自の再接続メカニズムを実装するか、手動で再接続する必要があります |
| 内部サーバーの一時的なエラー | Azure Web PubSub サービスで一時的なエラーが発生しています。自動的に復旧します

#### <a name="unexpected-connection-growing"></a>予期せずに接続が増加している

予期しない接続の増加に関するトラブルシューティングを行うには、最初に余計な接続をフィルターで除外する必要があります。 テスト クライアント接続に、一意のテスト ユーザー ID を追加できます。 次に、診断ログを使用してそれを検証します。複数のクライアント接続に同じテスト ユーザー ID または IP があることがわかった場合は、クライアント側では想定よりも多くの接続が作成され確立される可能性があります。 クライアント側を確認します。

### <a name="authorization-failure"></a>承認エラー

クライアント要求に対して未承認 401 が返された場合は、診断ログを確認します。 `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` が見つかった場合、アクセス トークン内のすべての対象ユーザーが無効であることを意味します。 ログ上に提示されている有効なユーザーを使用してみてください。

### <a name="throttling"></a>Throttling

Azure Web PubSub サービスへのクライアント接続を確立できないことがわかった場合は、診断ログを確認してください。 診断ログ上で `Connection count reaches limit` が見つかった場合は、確立している Azure Web PubSub サービスへの接続が多すぎて、接続数の上限に到達しています。 Azure Web PubSub サービス インスタンスのスケール アップを検討してください。 診断ログ上で `Message count reaches limit` が見つかった場合は、Free レベルを利用していて、メッセージのクォータを最大まで使用していることを意味します。 さらにメッセージを送信する場合は、Azure Web PubSub サービス インスタンスを Standard レベルに変更することでさらに多くのメッセージを送信できるようにする検討を行ってください。 詳細については、[Azure Web PubSub サービスの価格](https://azure.microsoft.com/pricing/details/web-pubsub/)に関するページを参照してください。