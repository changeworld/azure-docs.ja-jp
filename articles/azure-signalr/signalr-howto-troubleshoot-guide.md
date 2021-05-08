---
title: Azure SignalR Service のトラブルシューティング ガイド
description: 一般的な問題をトラブルシューティングする方法を確認する
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: e26def56fbd03626c3efc660db57012ee1b767ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048206"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Azure SignalR Service の一般的な問題に関するトラブルシューティング ガイド

このガイダンスは、過去数年間に、お客様に発生して解決された一般的な問題に基づく便利なトラブルシューティング ガイドとなっています。

## <a name="access-token-too-long"></a>アクセス トークンが長すぎる

### <a name="possible-errors"></a>考えられるエラー

* クライアント側の `ERR_CONNECTION_`
* 414 URI が長すぎます
* 413 ペイロードが大きすぎます
* アクセス トークンは 4K より長くてはいけません。 413 要求のエンティティが大きすぎます

### <a name="root-cause"></a>根本原因

HTTP/2 の場合、1 つのヘッダーの最大長は **4 K** であるため、ブラウザーを使用して Azure サービスにアクセスする場合、この制限についてエラー `ERR_CONNECTION_` が発生します。

HTTP/1.1 の場合や C# クライアントの場合、URI の最大長は **12 K** で、最大ヘッダー長は **16 K** です。

SDK バージョン **v.1.0.6** 以降では、生成されたアクセス トークンが **4 K** よりも大きいと、`/negotiate` によって `413 Payload Too Large` がスローされます。

### <a name="solution"></a>解決策

既定では、`context.User.Claims` からの要求は、**ASRS** (**A** zure **S** ignal **R** **S** ervice) に対する JWT アクセス トークンの生成時に含められます。そのため、クライアントが `Hub` に接続するときに、要求は保持されていて、**ASRS** から `Hub` に渡すことができます。

場合によっては、アプリ サーバー用の大量の情報を格納するために `context.User.Claims` が使用されます。そのほとんどは、`Hub` では使用されず、その他のコンポーネントによって使用されます。

生成されたアクセス トークンはネットワーク経由で渡され、WebSocket/SSE 接続の場合、アクセス トークンはクエリ文字列を通して渡されます。 そのためベスト プラクティスとして、ハブが必要とするときに、**必要な** 要求だけを、クライアントから **ASRS** を介してアプリ サーバーに渡すことをお勧めします。

アクセス トークン内の **ASRS** に渡す要求をカスタマイズする場合は、`ClaimsProvider` があります。

ASP.NET Core の場合:

```csharp
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

ASP.NET の場合:

```csharp
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tls-12-required"></a>TLS 1.2 が必要

### <a name="possible-errors"></a>考えられるエラー

* ASP.NET の "利用可能なサーバーがありません" というエラー [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET の "接続がアクティブではありません。サービスにデータを送信できません。" というエラー [#324](https://github.com/Azure/azure-signalr/issues/324)
* "https://<API endpoint> に対する HTTP 要求の発行中にエラーが発生しました。 このエラーは、HTTPS の場合に HTTP.SYS でサーバー証明書が正しく構成されていないことが原因と考えられます。 このエラーは、クライアントとサーバーの間でセキュリティ バインドが一致していないことが原因の場合もあります。"

### <a name="root-cause"></a>根本原因

Azure サービスでは、セキュリティ上の懸念のため、TLS 1.2 のみがサポートされています。 .NET Framework の使用時には、TLS 1.2 が既定のプロトコルではない可能性があります。 結果として、ASRS へのサーバー接続を正常に確立できません。

### <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

1. このエラーをローカルで再現できる場合は、"*マイ コードのみ*" をオフにしてすべての CLR 例外をスローし、アプリ サーバーをローカルでデバッグして、どの例外がスローされるかを確認します。
    * "*マイ コードのみ*" をオフにします

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="&quot;マイ コードのみ&quot; をオフにします":::

    * CLR 例外をスローします

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="CLR 例外をスローします":::

    * アプリのサーバー側コードをデバッグするときの例外のスローを確認します。
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="例外のスロー":::

2. ASP.NET の場合は、次のコードを現在の `Startup.cs` に追加して詳細なトレースを有効にし、ログからエラーを確認することもできます。

    ```cs
    app.MapAzureSignalR(this.GetType().FullName);
    // Make sure this switch is called after MapAzureSignalR
    GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
    ```

### <a name="solution"></a>解決策

次のコードを現在の Startup に追加します。

```csharp
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="400-bad-request-returned-for-client-requests"></a>クライアント要求に対して 400 "無効な要求" が返された

### <a name="root-cause"></a>根本原因

クライアント要求に、複数の `hub` クエリ文字列があるかどうかを確認します。 `hub` は、保持されているクエリ パラメーターであり、サービスがクエリ内で複数の `hub` を検出した場合に 400 がスローされます。

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="401-unauthorized-returned-for-client-requests"></a>クライアント要求に対して未承認 401 が返された

### <a name="root-cause"></a>根本原因

現在、JWT トークンの有効期間の既定値は 1 時間です。

ASP.NET Core SignalR の場合、トランスポートの種類として WebSocket を使用していれば問題ありません。

ASP.NET Core SignalR のその他のトランスポートの種類、SSE、長時間ポーリングの場合、これは、接続は既定では最大 1 時間保持できることを意味します。

ASP.NET SignalR の場合、クライアントは時々 `/ping` KeepAlive 要求をサービスに送信します。`/ping` が失敗すると、クライアントは接続を **中止** し、再接続することはありません。 つまり、ASP.NET SignalR の場合、既定のトークンの有効期間によって、接続はすべてのトランスポートの種類について **最大** 1 時間維持されます。

### <a name="solution"></a>解決策

セキュリティ上の懸念のため、TTL の延長はお勧めできません。 そのような 401 が発生したときに、クライアントから接続を再開する再接続ロジックを追加することをお勧めします。 クライアントは、接続を再開するときに、アプリ サーバーとネゴシエートして JWT トークンを再度取得し、更新されたトークンを取得します。

クライアント接続を再開する方法については、[こちら](#restart_connection)を参照してください。

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-client-requests"></a>クライアント要求に対して 404 が返された

SignalR の永続的な接続の場合、まず Azure SignalR サービスに対して `/negotiate` を行い、次に Azure SignalR サービスへの実際の接続を確立します。

### <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

* [送信要求を表示する方法](#view_request)に従って、クライアントからサービスへの要求を取得します。
* 404 が発生したときの要求の URL を調べます。 URL のターゲットが Web アプリで、`{your_web_app}/hubs/{hubName}` に似ている場合は、クライアントの `SkipNegotiation` が `true` かどうかを確認します。 Azure SignalR の使用時には、クライアントは最初にアプリ サーバーとネゴシエートするときにリダイレクト URL を受け取ります。 Azure SignalR の使用時には、クライアントがネゴシエーションをスキップしては **いけません**。
* `/negotiate` の呼び出し後、接続要求の処理が **5** 秒より長くかかると、別の 404 が発生する可能性があります。 クライアント要求のタイムスタンプを調べて、サービスに対する要求の応答が遅くなっている場合は、Microsoft へのイシューを開きます。

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>ASP.NET SignalR の再接続要求に対して 404 が返された

ASP.NET SignalR の場合、[クライアント接続が切断される](#client_connection_drop)と、接続を停止する前に、同じ `connectionId` を使用して 3 回再接続されます。 `/reconnect` を使用して永続的な接続を正常に再確立できるネットワークの間欠的な問題が原因で接続が切断される場合は、`/reconnect` が役立ちます。 ルーティングされたサーバー接続が切断されたためにクライアント接続が切断された、SignalR Service でインスタンスの再起動/フェールオーバー/デプロイなどの内部エラーが発生したので接続が存在しなくなったなどのその他の状況では、`/reconnect` から `404` が返されます。 これは `/reconnect` の予期されている動作であり、3 回の試行後に接続が停止します。 接続停止時の[接続再開](#restart_connection)ロジックを実装することをお勧めします。

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="429-too-many-requests-returned-for-client-requests"></a>クライアント要求に対して 429 (要求が多すぎます) が返された

次の 2 つのケースがあります。

### <a name="concurrent-connection-count-exceeds-limit"></a>**コンカレント** 接続数が制限を超えている

**Free** インスタンスの場合、**コンカレント** 接続数の制限は 20 です。**Standard** インスタンスの場合、**コンカレント** 接続数の制限は **ユニットあたり** 1 K です。つまり、100 ユニットでは 100 K のコンカレント接続が許可されます。

接続には、クライアントとサーバーの両方の接続が含まれます。 接続がどのようにカウントされるかについては、[ここ](./signalr-concept-messages-and-connections.md#how-connections-are-counted)で確認してください。

### <a name="too-many-negotiate-requests-at-the-same-time"></a>同時のネゴシエート要求が多すぎる

再接続の前にランダム遅延を使用することをお勧めします。再試行のサンプルについては、[こちら](#restart_connection)を確認してください。

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500 "ネゴシエート時のエラー": Azure SignalR サービスはまだ接続されていません。後でもう一度試してください

### <a name="root-cause"></a>根本原因

このエラーは、接続された Azure SignalR Service へのサーバー接続がないときに報告されます。

### <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

サーバー側のトレースを有効にして、サーバーが Azure SignalR Service への接続を試みたときのエラーの詳細を確認します。

### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>ASP.NET Core SignalR に対するサーバー側のログ記録を有効にする

ASP.NET Core SignalR のサーバー側ログは、ASP.NET Core フレームワークで提供されている `ILogger` ベースの[ログ記録](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1&preserve-view=true)と統合されています。 サーバー側のログ記録は、`ConfigureLogging` を使用して有効にできます。以下に使用例を示します。

```csharp
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR のロガー カテゴリは常に `Microsoft.Azure.SignalR` で始まります。 Azure SignalR からの詳細なログを有効にするには、**appsettings.json** ファイルで次のように、先行するプレフィックスを `Debug` レベルに構成します。

```json
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>ASP.NET SignalR に対するサーバー側のトレースを有効にする

バージョンが `1.0.0` 以上の SDK を使用する場合、`web.config` に以下を追加すればトレースを有効にできます。([詳細](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))

```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-drops"></a>クライアント接続が切断される

クライアントが Azure SignalR に接続されているときに、クライアントと Azure SignalR の間の永続的な接続が、さまざまな理由で切断されることがあります。 このセクションでは、このような接続の切断を引き起こす可能性があるいくつかの原因について説明し、根本原因を特定する方法に関するガイダンスをいくつか示します。

### <a name="possible-errors-seen-from-the-client-side"></a>クライアント側から確認される可能性のあるエラー

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>根本原因

クライアント接続は、さまざまな状況で切断される可能性があります。
* 受信要求によって `Hub` が例外をスローする場合。
* クライアントのルーティング先であるサーバーの接続が切断される場合は、詳細について、[サーバー接続の切断](#server_connection_drop)に関する後のセクションを参照してください。
* クライアントと SignalR Service の間でネットワーク接続の問題が発生する場合。
* SignalR Service で、インスタンスの再起動、フェールオーバー、デプロイのような何らかの内部エラーが発生した場合。

### <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

1. アプリ サーバー側のログを開き、通常とは異なる何かが発生したかどうかを確認します
2. アプリ サーバー側のイベント ログで、アプリ サーバーが再起動されたかどうかを確認します
3. Microsoft へのイシューを作成して概算時間を提供し、リソース名をメールで送信します

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-increases-constantly"></a>クライアント接続が絶えず増加する

これは、クライアント接続が不適切に使用されていることが原因の可能性があります。 誰かが SignalR クライアントの停止や破棄を忘れた場合、接続は開いたままになります。

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Azure portal リソース メニューの [監視] セクションにある SignalR のメトリックから確認される可能性のあるエラー

Azure SignalR のメトリックで、クライアント接続が長期にわたって増加しています。

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="絶えず増加するクライアント接続":::

### <a name="root-cause"></a>根本原因

SignalR クライアント接続の `DisposeAsync` がまったく呼び出されておらず、接続が開いたままになっています。

### <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

SignalR クライアントが閉じたことが **ない** かどうかを確認します。

### <a name="solution"></a>解決策

接続が閉じるかどうかを調べます。 手動で `HubConnection.DisposeAsync()` を呼び出して、使用後に接続を停止します。

次に例を示します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>よくある不適切なクライアント接続の使用

#### <a name="azure-function-example"></a>Azure 関数の例 

この問題は、ユーザーが SignalR クライアント接続を、関数クラスの静的メンバーにするのではなく、Azure 関数のメソッドで確立するときによく発生します。 クライアント接続が 1 つだけ確立されると予期していても、Azure portal リソース メニューの [監視] セクションにある [メトリック] で、クライアント接続数が絶えず増加していることがわかります。これらの接続はすべて、Azure 関数または Azure SignalR サービスが再起動した後にのみ削除されます。 これは、**それぞれの** 要求に対して Azure 関数がクライアント接続を **1 つ** 作成するためです。関数のメソッドでクライアント接続を停止しないと、クライアントは Azure SignalR サービスへの接続を有効な状態に維持します。

#### <a name="solution"></a>解決策

* Azure 関数で SignalR クライアントを使用する場合や、SignalR クライアントをシングルトンとして使用する場合は、忘れずにクライアント接続を閉じてください。
* Azure 関数で SignalR クライアントを使用する代わりに、他の任意の場所に SignalR クライアントを作成し、[Azure SignalR Service 用の Azure Functions バインド](https://github.com/Azure/azure-functions-signalrservice-extension)を使用して、Azure SignalR に対してクライアントを[ネゴシエートする](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22)ことができます。 また、バインドを利用して[メッセージを送信する](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40)こともできます。 クライアントをネゴシエートしてメッセージを送信するサンプルは、[ここ](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples)にあります。 詳しくは、[こちら](https://github.com/Azure/azure-functions-signalrservice-extension)をご覧ください。
* Azure 関数で SignalR クライアントを使用するときには、実際のシナリオにより適したアーキテクチャがある可能性があります。 適切なサーバーレス アーキテクチャを設計しているかどうかを確認してください。 [Azure Functions で SignalR Service バインドを使用するリアルタイムのサーバーレス アプリケーション](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService)を参照できます。

<a name="server_connection_drop"></a>

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="server-connection-drops"></a>サーバー接続が切断される

アプリ サーバーが起動すると、バックグラウンドでは、Azure SDK によってリモートの Azure SignalR へのサーバー接続が開始されます。 「[Azure SignalR Service の内部構造](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)」で説明されているように、着信クライアント トラフィックは、Azure SignalR によってこれらのサーバー接続にルーティングされます。 サーバー接続が切断されると、それがサービスを提供しているすべてのクライアント接続も閉じられます。

アプリ サーバーと SignalR Service の間の接続は永続的な接続であるため、ネットワーク接続の問題が発生する可能性があります。 サーバー SDK では、サーバー接続に対して **常に再接続** の戦略が採用されています。 ベスト プラクティスとして、ランダムな遅延時間を利用する継続的再接続のロジックをクライアントに追加し、サーバーへの大量の同時要求を回避することもお勧めします。

定期的に、Azure SignalR Service の新しいバージョンがリリースされています。また、Azure 全体の OS のパッチ適用やアップグレードがある場合もあります。時折、依存サービスから中断されることもあります。 これらのために短期間サービスが中断されることもありますが、クライアント側に切断/再接続メカニズムがある限り、クライアント側で発生した切断/再接続と同様に、影響は最小限になります。

このセクションでは、サーバー接続の切断に至るいくつかの考えられる原因について説明し、根本原因を特定する方法に関するガイダンスをいくつか示します。

### <a name="possible-errors-seen-from-the-server-side"></a>サーバー側から確認される可能性のあるエラー

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>根本原因

サーバーとサービスの間の接続は、**ASRS** (**A** zure **S** ignal **R** **S** ervice) によって閉じられます。

ping タイムアウトの場合は、サーバー側での CPU 使用率が高いか、スレッド プールが枯渇したことが原因である可能性があります。

ASP.NET SignalR では、SDK 1.6.0 で既知の問題が修正されました。 お使いの SDK を最新バージョンにアップグレードしてください。

## <a name="thread-pool-starvation"></a>スレッド プールの枯渇

サーバーが逼迫している場合は、メッセージの処理を行っているスレッドがないことを意味します。 すべてのスレッドが特定のメソッドで応答していません。

通常、このシナリオは、 async over sync、または async メソッドの `Task.Result`/`Task.Wait()` によって発生します。

[ASP.NET Core パフォーマンスのベスト プラクティス](/aspnet/core/performance/performance-best-practices#avoid-blocking-calls)に関する記事を参照してください。

[スレッド プールの枯渇](https://docs.microsoft.com/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall)の詳細について確認してください。

### <a name="how-to-detect-thread-pool-starvation"></a>スレッド プールの枯渇を検出する方法

スレッド数を確認してください。 その時点でスパイクが発生していない場合は、これらの手順を実行します。
* Azure App Service を使用している場合は、メトリックでスレッド数を確認します。 `Max` の集計を確認します。
    
  :::image type="content" source="media/signalr-howto-troubleshoot-guide/metrics-thread-count.png" alt-text="Azure App Service の [最大スレッド数] ペインのスクリーンショット。":::

* .NET Framework を使用している場合は、サーバー VM のパフォーマンス モニターで[メトリック](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/performance-counters#lock-and-thread-performance-counters)を確認できます。
* コンテナーで .NET Core を使用している場合は、「[コンテナーでの診断の収集](https://docs.microsoft.com/dotnet/core/diagnostics/diagnostics-in-containers)」を参照してください。

コードを使用して、スレッド プールの枯渇を検出することもできます。

```csharp
public class ThreadPoolStarvationDetector : EventListener
{
    private const int EventIdForThreadPoolWorkerThreadAdjustmentAdjustment = 55;
    private const uint ReasonForStarvation = 6;

    private readonly ILogger<ThreadPoolStarvationDetector> _logger;

    public ThreadPoolStarvationDetector(ILogger<ThreadPoolStarvationDetector> logger)
    {
        _logger = logger;
    }

    protected override void OnEventSourceCreated(EventSource eventSource)
    {
        if (eventSource.Name == "Microsoft-Windows-DotNETRuntime")
        {
            EnableEvents(eventSource, EventLevel.Informational, EventKeywords.All);
        }
    }

    protected override void OnEventWritten(EventWrittenEventArgs eventData)
    {
        // See: https://docs.microsoft.com/en-us/dotnet/framework/performance/thread-pool-etw-events#threadpoolworkerthreadadjustmentadjustment
        if (eventData.EventId == EventIdForThreadPoolWorkerThreadAdjustmentAdjustment &&
            eventData.Payload[3] as uint? == ReasonForStarvation)
        {
            _logger.LogWarning("Thread pool starvation detected!");
        }
    }
}
```
    
それをサービスに追加します。
    
```csharp
service.AddSingleton<ThreadPoolStarvationDetector>();
```

その後、サーバー接続が ping タイムアウトによって切断されたときにログを確認します。

### <a name="how-to-find-the-root-cause-of-thread-pool-starvation"></a>スレッド プールの枯渇の根本原因を見つける方法

スレッド プールの枯渇の根本原因を見つけるには、次のようにします。

* メモリをダンプし、呼び出し履歴を分析します。 詳細については、[メモリ ダンプの収集と分析](https://devblogs.microsoft.com/dotnet/collecting-and-analyzing-memory-dumps/)に関するページを参照してください。
* スレッド プールの枯渇が検出されたときに、[clrmd](https://github.com/microsoft/clrmd) を使用してメモリをダンプします。 その後、呼び出し履歴をログに記録します。

### <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

1. アプリ サーバー側のログを開き、通常とは異なる何かが発生したかどうかを確認します。
2. アプリ サーバー側のイベント ログで、アプリ サーバーが再起動されたかどうかを確認します。
3. イシューを作成します。 概算時間を提供し、リソース名を Microsoft にメールで送信します。

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tips"></a>ヒント

<a name="view_request"></a>

* クライアントからの送信要求はどのように表示するのでしょうか。
たとえば、ASP.NET Core の例を見てみます (ASP.NET の例は同様です)。
    * ブラウザーから:

        例として Chrome を使用すると、**F12** キーを使用してコンソール ウィンドウを開き、 **[ネットワーク]** タブに切り替えることができます。まったく最初からネットワークをキャプチャするには、**F5** キーを使用して、ページを最新の情報に更新することが必要な場合があります。

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome でのネットワーク表示":::

    * C# クライアントから:

        [Fiddler](https://www.telerik.com/fiddler) を使用してローカル Web トラフィックを表示できます。 WebSocket トラフィックは、Fiddler 4.5 以降でサポートされています。

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Fiddler でのネットワークの表示" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* クライアント接続はどのように再開するのでしょうか。
    
    以下に、*常に再試行* の戦略を採用した接続再開ロジックを含む [サンプル コード](https://github.com/Azure/azure-signalr/tree/dev/samples)を示します。

    * [ASP.NET Core C# クライアント](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [ASP.NET Core JavaScript クライアント](https://github.com/Azure/azure-signalr/blob/release/1.0.0-preview1/samples/ChatSample/wwwroot/index.html#L164)

    * [ASP.NET C# クライアント](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [ASP.NET JavaScript クライアント](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

[トラブルシューティングに関する問題やフィードバックがある場合は、お知らせください。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>次のステップ

このガイドでは、一般的な問題に対処する方法について学習しました。 より包括的なトラブルシューティング方法についても学習できます。 

> [!div class="nextstepaction"]
> [接続とメッセージ配信に関する問題のトラブルシューティング方法](./signalr-howto-troubleshoot-method.md)
