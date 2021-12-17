---
title: Azure Web PubSub サービス リソース ログを使用してトラブルシューティングを行う方法
description: リソース ログを取得してトラブルシューティングを行う方法について説明します
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/22/2021
ms.openlocfilehash: 7e8d4c725c8e205f015774b8b5b01e26b5f89271
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477846"
---
# <a name="how-to-troubleshoot-with-resource-logs"></a>リソース ログを使用してトラブルシューティングを行う方法

この攻略ガイドでは、リソース ログを取得するためのオプションと、それらを使用してトラブルシューティングを行う方法について説明します。

## <a name="whats-the-resource-logs"></a>リソース ログとは何ですか。

リソース ログでは、Azure Web PubSub サービス インスタンスに対する接続、メッセージング、HTTP 要求情報の豊富なビューが提供されます。 これは、問題の識別、接続の追跡、メッセージのトレース、HTTP 要求のトレースと分析に使用できます。

ログには、接続ログ、メッセージング ログ、HTTP 要求ログの 3 種類があります。

### <a name="connectivity-logs"></a>接続ログ

接続ログでは、Azure Web PubSub ハブ接続に関する詳細情報が提供されます。 たとえば、基本情報 (ユーザー ID、接続 ID など) やイベント情報 (接続、切断、中止イベントなど) です。 そのため、接続ログは接続に関連する問題のトラブルシューティングに役立ちます。

### <a name="messaging-logs"></a>メッセージング ログ

メッセージング ログでは、Azure Web PubSub サービス経由で送受信される Azure Web PubSub ハブ メッセージのトレース情報が提供されます。 たとえば、メッセージのトレース ID やメッセージの種類などです。 メッセージは通常、サービスへの到着時またはサービスからの発信時に記録されます。 メッセージング ログは、メッセージに関連する問題のトラブルシューティングに役立ちます。

### <a name="http-request-logs"></a>HTTP 要求ログ

Http 要求ログは、Azure Web PubSub サービスに対する HTTP 要求のトレース情報を提供します。 たとえば、HTTP メソッドと状態コードです。 HTTP 要求は通常、サービスへの到着時またはサービスからの発信時に記録されます。 そのため、HTTP 要求ログは、要求に関連する問題のトラブルシューティングに役立ちます。

## <a name="capture-resource-logs-with-live-trace-tool"></a>ライブ トレース ツールを使用したリソース ログのキャプチャ 

Azure Web PubSub サービス ライブ トレース ツールは、リアルタイムでリソース ログを収集する機能を備えており、お客様の開発環境でのトレースに役立ちます。 ライブ トレース ツールは、接続ログ、メッセージング ログ、および HTTP 要求ログをキャプチャできます。

> [!NOTE]
> ライブ トレース ツールによってキャプチャされたリアルタイム リソース ログは、メッセージ (送信トラフィック) として課金されます。

> [!NOTE]
> Free レベルとして作成された Azure Web PubSub サービス インスタンスには、メッセージ (送信トラフィック) に 1 日当たりの制限があります。

### <a name="launch-the-live-trace-tool"></a>ライブ トレース ツールを起動する

1. Azure Portal にアクセスします。 
2. Azure Web PubSub サービス インスタンスの **[ライブ トレースの設定]** ページで、 **[ライブ トレースの有効化]** が無効かどうかを確認します。
3. 必要なログ カテゴリを確認します。
4. **[保存]** ボタンをクリックし、設定が有効になるまで待ちます。
5. *[ライブ トレース ツールを開く]* をクリックします。

    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-logs-with-live-trace-tool.png" alt-text="ライブ トレース ツールを起動するスクリーンショット。":::

### <a name="capture-the-resource-logs"></a>リソース ログをキャプチャする

ライブ トレース ツールでは、トラブルシューティングのためにリソース ログをキャプチャするのに役立ついくつかの基本的な機能が提供されます。

* **[Capture]\(キャプチャ\)** : ライブ トレース ツールを使用して、Azure Web PubSub インスタンスからリアルタイム リソース ログのキャプチャを開始します。
* **[クリア]** : キャプチャしたリアルタイム リソース ログをクリアします。
* **[Log filter]\(ログ フィルター\)** : ライブ トレース ツールを使用すると、キャプチャしたリアルタイム リソース ログを特定の 1 つのキー ワードでフィルター処理できます。 一般的な区切り記号 (たとえばスペースやコンマ、セミコロンなど) は、キー ワードの一部として扱われます。 
* **[Status]\(状態\)** : ライブ トレース ツールが特定のインスタンスと接続されているか、切断されているかの状態を示しています。

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/live-trace-tool-capture.png" alt-text="ライブ トレース ツールを使用してリソース ログをキャプチャするスクリーンショット。":::

ライブ トレース ツールによってキャプチャされたリアルタイム リソース ログには、トラブルシューティングのための詳細情報が含まれています。 

| Name | 説明 |
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
| ルート テンプレート | API のルート テンプレート |
| HTTP メソッド | Http メソッド (POST/GET/PUT/DELETE) |
| URL | 「Uniform Resource Locator」を参照してください。 |
| トレース ID | 呼び出しに対する一意の識別子 |
| 状態コード | HTTP 応答コード |
| 期間 | 要求の受信と処理の間の期間 |
| ヘッダー | HTTP 要求または応答を使用してクライアントとサーバーによって渡される追加情報 |

Azure Web PubSub サービスが GA になった後には、ライブ トレース ツールでログを特定の形式でエクスポートして、トラブルシューティングのために他のユーザーと共有することもできるようになります。 

## <a name="capture-resource-logs-with-azure-monitor"></a>Azure Monitor を使用したリソース ログのキャプチャ

### <a name="how-to-enable-resource-logs"></a>リソース ログを有効にする方法

現在、Azure Web PubSub は [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) との統合をサポートしています。

1. Azure portal にアクセスします。
2. Azure Web PubSub サービス インスタンスの **[診断設定]** ページで、 **[+ 診断設定を追加する]** リンクをクリックします。
3. **[診断設定の名前]** に設定名を入力します。
4. **[カテゴリの詳細]** で、必要なログ カテゴリを選択します。
5. **[宛先の詳細]** で、 **[ストレージ アカウントへのアーカイブ]** をオンにします。
6. 診断設定を構成したら、 **[保存]** ボタンをクリックします。

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-list.png" alt-text="診断設定を表示して新しい設定を作成するスクリーンショット":::

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-details.png" alt-text="診断設定の詳細の構成のスクリーンショット":::

> [!NOTE]
> ストレージ アカウントは、Azure Web PubSub サービスと同じリージョンである必要があります。

### <a name="archive-to-a-storage-account"></a>ストレージ アカウントへのアーカイブ

ログは、 **[診断設定]** ペインで構成したストレージ アカウントに格納されます。 リソース ログを格納するために、`insights-logs-<CATEGORY_NAME>` という名前のコンテナーが自動的に作成されます。 コンテナー内では、ログはファイル `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` に格納されます。 基本的に、パスは `resource ID` と `Date Time` によって連結されています。 ログ ファイルは `hour` 別に分割されています。 そのため、分数は常に `m=00` になります。

すべてのログは、JavaScript Object Notation (JSON) 形式で格納されます。 各エントリには、以下のセクションで説明している形式を使用する文字列フィールドがあります。

アーカイブ ログの JSON 文字列には、次の表に示す要素が含まれます。

**形式**

名前 | 説明
------- | -------
time | ログ イベント時間
level | ログ イベント レベル
resourceId | Azure SignalR サービスのリソース ID
location | Azure SignalR サービスの場所
category | ログ イベントのカテゴリ
operationName | イベントの操作名
callerIpAddress | サーバー/クライアントの IP アドレス
properties | このログ イベントに関連する詳細なプロパティ。 詳細については、次のプロパティの表を参照してください。

**プロパティの表**

名前 | 説明
------- | -------
collection | ログ イベントのコレクション。 使用できる値: `Connection`、`Authorization`、および `Throttling`
connectionId | 接続の ID
userId | ユーザーの ID
message | ログ イベントの詳細なメッセージ
ハブ | ユーザー定義のハブ名 |
routeTemplate | API のルート テンプレート |
httpMethod | Http メソッド (POST/GET/PUT/DELETE) |
url | 「Uniform Resource Locator」を参照してください。 |
traceId | 呼び出しに対する一意の識別子 |
statusCode | HTTP 応答コード |
duration | 要求の受信と処理の間の期間 |
headers | HTTP 要求または応答を使用してクライアントとサーバーによって渡される追加情報 |

次のコードは、アーカイブ ログの JSON 文字列の例です。

```json
{
  "properties": {
    "message": "Connection started",
    "collection": "Connection",
    "connectionId": "LW61bMG2VQLIMYIVBMmyXgb3c418200",
    "userId": null
  },
  "operationName": "ConnectionStarted",
  "category": "ConnectivityLogs",
  "level": "Informational",
  "callerIpAddress": "167.220.255.79",
  "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYGROUP/PROVIDERS/MICROSOFT.SIGNALRSERVICE/WEBPUBSUB/MYWEBPUBSUB",
  "time": "2021-09-17T05:25:05Z",
  "location": "westus"
}
```

## <a name="troubleshoot-with-the-resource-logs"></a>リソース ログを使用したトラブルシューティング

接続が予期せず増加または削除される状況がわかった場合は、リソース ログを利用してトラブルシューティングできます。 多くの場合、一般的な問題は予期しない接続数の変化、接続数の接続上限への到達、承認エラーに関するものです。

### <a name="unexpected-connection-number-changes"></a>予期しない接続数の変化

#### <a name="unexpected-connection-dropping"></a>予期しない接続の削除

接続が切断されると、リソース ログによって `operationName` を `ConnectionAborted` または `ConnectionEnded` として、その切断イベントが記録されます。

`ConnectionAborted` と `ConnectionEnded` の違いは、`ConnectionEnded` はクライアントまたはサーバー側によってトリガーされた予期された切断であるという点です。 一方、`ConnectionAborted` は多くの場合、予期しない接続の削除イベントであり、中断の理由が `message` に示されます。

次の表に、中断の理由を示します。

| 理由 | 説明 |
| ------- | ------- |
| 接続数が上限に達している | 接続数が現在の価格レベルの上限に達しています。 サービス ユニットのスケールアップを検討してください
| サービスの再読み込み、再接続 | Azure Web PubSub サービスを再度読み込みしています。 Azure Web PubSub サービスへの独自の再接続メカニズムを実装するか、手動で再接続する必要があります |
| 内部サーバーの一時的なエラー | Azure Web PubSub サービスで一時的なエラーが発生しています。自動的に復旧します

#### <a name="unexpected-connection-growing"></a>予期せずに接続が増加している

予期しない接続の増加に関するトラブルシューティングを行うには、最初に余計な接続をフィルターで除外する必要があります。 テスト クライアント接続に、一意のテスト ユーザー ID を追加できます。 次に、リソース ログを使用してそれを検証します。複数のクライアント接続に同じテスト ユーザー ID または IP があることがわかった場合は、クライアント側では想定よりも多くの接続が作成され確立される可能性があります。 クライアント側を確認します。

### <a name="authorization-failure"></a>承認エラー

クライアント要求に対して未承認 401 が返された場合は、リソース ログを確認します。 `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` が見つかった場合、アクセス トークン内のすべての対象ユーザーが無効であることを意味します。 ログ上に提示されている有効なユーザーを使用してみてください。

### <a name="throttling"></a>Throttling

Azure Web PubSub サービスへのクライアント接続を確立できないことがわかった場合は、リソース ログを確認してください。 リソース ログ上で `Connection count reaches limit` が見つかった場合は、確立している Azure Web PubSub サービスへの接続が多すぎて、接続数の上限に到達しています。 Azure Web PubSub サービス インスタンスのスケール アップを検討してください。 リソース ログ上で `Message count reaches limit` が発生した場合は、Free レベルを利用しており、メッセージのクォータを最大まで使用していることを意味します。 さらにメッセージを送信する場合は、Azure Web PubSub サービス インスタンスを Standard レベルに変更することでさらに多くのメッセージを送信できるようにする検討を行ってください。 詳細については、[Azure Web PubSub サービスの価格](https://azure.microsoft.com/pricing/details/web-pubsub/)に関するページを参照してください。
