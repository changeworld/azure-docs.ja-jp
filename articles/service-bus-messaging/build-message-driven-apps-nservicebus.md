---
title: NServiceBus と Azure Service Bus を使用してメッセージ駆動型アプリケーションを構築する
description: Azure Service Bus 上の分散システムに関する複雑な問題を、NServiceBus フレームワークを使用して解決する方法について説明します。
author: kbaley
ms.author: spelluru
ms.service: service-bus-messaging
ms.topic: how-to
ms.date: 07/26/2021
ms.custom: template-how-to
ms.openlocfilehash: c9f503cb600c87e1dac590dcbbe7edf46bc7be76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778817"
---
# <a name="build-message-driven-business-applications-with-nservicebus-and-azure-service-bus"></a>NServiceBus と Azure Service Bus を使用してメッセージ駆動型ビジネス アプリケーションを構築する
NServiceBus は、Particular Software によって提供されている商用メッセージング フレームワークです。 これは、Azure Service Bus 上に構築されており、インフラストラクチャの懸念事項を抽出することにより開発者がビジネス ロジックに集中できるように支援します。 このガイドでは、2 つのサービス間でメッセージを交換するソリューションを構築します。 また、失敗したメッセージを自動的に再試行する方法と、Azure でこれらのサービスをホストするためのオプションについても説明します。

> [!NOTE]
> このチュートリアルのコードは、[Particular Software Docs Web サイト](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/)で入手できます。 

## <a name="prerequisites"></a>前提条件

このサンプルでは、[Azure Service Bus 名前空間が作成済み](service-bus-create-namespace-portal.md)であることを前提としています。

> [!IMPORTANT]
> NServiceBus には、少なくとも Standard レベルが必要です。 Basic レベルでは機能しません。

## <a name="download-and-prepare-the-solution"></a>ソリューションをダウンロードし準備する
1. コードを [Particular Software Docs Web サイト](https://docs.particular.net/samples/azure-service-bus-netstandard/send-receive-with-nservicebus/)からダウンロードします。 ソリューション `SendReceiveWithNservicebus.sln` は、次の 3 つのプロジェクトで構成されています。

    - **Sender**: メッセージを送信するコンソール アプリケーション
    - **Receiver**: 送信者からメッセージを受信して返信するコンソール アプリケーション
    - **Shared**: 送信者と受信者の間で共有されるメッセージ コントラクトを含むクラス ライブラリ
    
    Particular Software の視覚化およびデバッグ ツールである [ServiceInsight](https://particular.net/serviceinsight) によって生成された次の図で、メッセージ フローを示します。
    
    :::image type="content" source="./media/nservicebus/sequence-diagram.png" alt-text="シーケンス図を示す画像":::    
1. 使い慣れたコード エディター (Visual Studio 2019 など) で `SendReceiveWithNservicebus.sln` を開きます。 
1. Receiver プロジェクトと Sender プロジェクトの両方の `appsettings.json` を開き、`AzureServiceBusConnectionString` を Azure Service Bus 名前空間の接続文字列に設定します。



## <a name="define-the-shared-message-contracts"></a>共有メッセージ コントラクトを定義する

Shared クラス ライブラリで、メッセージを送信するのに使用するコントラクトを定義します。 このライブラリには `NServiceBus` NuGet パッケージへの参照が含まれており、そのパッケージにはメッセージの識別に使用できるインターフェイスが含まれています。 このインターフェイスは必要ではありませんが、それらを使用することで NServiceBus から追加の検証を行い、コードを自己文書化することができます。

まず、`Ping.cs` クラスを確認します。

```csharp
public class Ping : NServiceBus.ICommand
{
    public int Round { get; set; }
}
```

`Ping` クラスは、Sender が Receiver に送信するメッセージを定義します。 これは、NServiceBus パッケージのインターフェイス `NServiceBus.ICommand` を実装する単純な C# クラスです。 このメッセージは、閲覧者および NServiceBus に対してそれがコマンドであることを示すシグナルです。ただし、[インターフェイスを使用せずに](https://docs.particular.net/nservicebus/messaging/conventions)、別の方法でメッセージを識別することもできます。

Shared プロジェクトに含まれるもう 1 つのメッセージ クラスは、`Pong.cs` です。

```csharp
public class Pong : NServiceBus.IMessage
{
    public string Acknowledgement { get; set; }
}
```

`Pong` は `NServiceBus.IMessage` を実装しますが、これも単純な C# オブジェクトです。 `IMessage` インターフェイスは、コマンドでもイベントでもなく、応答に一般的に使用される汎用メッセージを表します。 このサンプルでは、メッセージを受信したことを示すために Receiver が Sender に返信する応答です。

`Ping` および `Pong` は、使用する 2 つのメッセージの種類です。 次の手順では、Azure Service Bus を使用して `Ping` メッセージを送信するように Sender を構成します。

## <a name="set-up-the-sender"></a>送信者を設定する

Sender は、`Ping` メッセージを送信するエンドポイントです。 ここでは、トランスポート メカニズムとして Azure Service Bus を使用するように Sender を構成し、次に `Ping` インスタンスを構築して送信します。

`Program.cs` の `Main` メソッドで、次のように Sender エンドポイントを構成します。

```csharp
var host = Host.CreateDefaultBuilder(args)
    // Configure a host for the endpoint
    .ConfigureLogging((context, logging) =>
    {
        logging.AddConfiguration(context.Configuration.GetSection("Logging"));

        logging.AddConsole();
    })
    .UseConsoleLifetime()
    .UseNServiceBus(context =>
    {
        // Configure the NServiceBus endpoint
        var endpointConfiguration = new EndpointConfiguration("Sender");

        var transport = endpointConfiguration.UseTransport<AzureServiceBusTransport>();
        var connectionString = context.Configuration.GetConnectionString("AzureServiceBusConnectionString");
        transport.ConnectionString(connectionString);

        transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

        endpointConfiguration.EnableInstallers();
        endpointConfiguration.AuditProcessedMessagesTo("audit");

        return endpointConfiguration;
    })
    .ConfigureServices(services => services.AddHostedService<SenderWorker>())
    .Build();

await host.RunAsync();
```

説明すべきことが多いので、ステップ バイ ステップで確認します。

### <a name="configure-a-host-for-the-endpoint"></a>エンドポイントのホストを構成する

ホスティングとログ記録は、標準の [Microsoft 汎用ホスト オプション](/dotnet/core/extensions/generic-host)を使用して構成されます。 ここでは、エンドポイントはコンソール アプリケーションとして実行するように構成されますが、最小限の変更で Azure Functions で実行するように変更できます。これについては、この記事で後ほど説明します。

### <a name="configure-the-nservicebus-endpoint"></a>NServiceBus エンドポイントを構成する

次に、`.UseNServiceBus(…)` 拡張メソッドを使用して、NServiceBus を使用するようにホストに命令します。 このメソッドは、ホストの実行時に開始されるエンドポイントを返すコールバック関数を受け取ります。

エンドポイントの構成で、転送用に `AzureServiceBus` を指定し、`appsettings.json` からの接続文字列を指定します。 次に、種類が `Ping` のメッセージが "Receiver" という名前のエンドポイントに送信されるように、ルーティングを設定します。 これにより、NServiceBus はその送信先にメッセージをディスパッチするプロセスを自動化でき、受信者のアドレスは必要ではなくなります。

`EnableInstallers` への呼び出しにより、エンドポイントの起動時に Azure Service Bus 名前空間にトポロジが設定され、必要に応じて必要なキューが作成されます。 運用環境では、[運用スクリプト](https://docs.particular.net/transports/azure-service-bus/operational-scripting)でもトポロジを作成できます。

### <a name="set-up-background-service-to-send-messages"></a>メッセージを送信するバックグラウンド サービスを設定する

送信者の最後の部分は、`SenderWorker` です。これは、`Ping` メッセージを毎秒送信するように構成されているバックグラウンド サービスです。

```csharp
public class SenderWorker : BackgroundService
{
    private readonly IMessageSession messageSession;
    private readonly ILogger<SenderWorker> logger;

    public SenderWorker(IMessageSession messageSession, ILogger<SenderWorker> logger)
    {
        this.messageSession = messageSession;
        this.logger = logger;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        try
        {
            var round = 0;
            while (!stoppingToken.IsCancellationRequested)
            {
                await messageSession.Send(new Ping { Round = round++ })
                    .ConfigureAwait(false);

                logger.LogInformation($"Message #{round}");

                await Task.Delay(1_000, stoppingToken)
                    .ConfigureAwait(false);
            }
        }
        catch (OperationCanceledException)
        {
            // graceful shutdown
        }
    }
}
```

`ExecuteAsync` で使用される `IMessageSession` は、`SenderWorker` に挿入され、メッセージ ハンドラー外で NServiceBus を使用してメッセージを送信できるようになります。 `Sender` で構成したルーティングにより、`Ping` メッセージの送信先が指定されます。 これにより、システムのトポロジ (どのメッセージがどのアドレスにルーティングされるか) が、ビジネス コードとは別の懸念事項として保持されます。

Sender アプリケーションには `PongHandler` も含まれています。 これについては、次で Receiver について説明した後に説明します。

## <a name="set-up-the-receiver"></a>受信者を設定する

Receiver は、`Ping` メッセージをリッスンし、メッセージの受信時にログを記録し、送信者に返信するエンドポイントです。 このセクションでは、Sender と似ているこのエンドポイントの構成をすばやく確認してから、メッセージ ハンドラーについて説明します。

送信者と同様に受信者も、Microsoft 汎用ホストを使用してコンソール アプリケーションとして設定します。 これには、同一のログ記録およびエンドポイントの構成が (メッセージ トランスポートとして Azure Service Bus を使用して) 使用されますが、送信者と区別するために別の名前を指定します。

```csharp
var endpointConfiguration = new EndpointConfiguration("Receiver");
```

このエンドポイントはその発信者にのみ応答し、新しい会話を開始しないので、ルーティング構成は必要ありません。 また、メッセージを受信したときに応答するだけなので、Sender の場合のようなバックグラウンド ワーカーも必要ありません。

### <a name="the-ping-message-handler"></a>Ping メッセージ ハンドラー

Receiver プロジェクトには、`PingHandler` という名前の _メッセージ ハンドラー_ が含まれています。

```csharp
public class PingHandler : NServiceBus.IHandleMessages<Ping>
{
    private readonly ILogger<PingHandler> logger;

    public PingHandler(ILogger<PingHandler> logger)
    {
        this.logger = logger;
    }

    public async Task Handle(Ping message, IMessageHandlerContext context)
    {
        logger.LogInformation($"Processing Ping message #{message.Round}");

        // throw new Exception("BOOM");

        var reply = new Pong { Acknowledgement = $"Ping #{message.Round} processed at {DateTimeOffset.UtcNow:s}" };

        await context.Reply(reply);
    }
}
```

コメント化されたコードについてはここでは無視し、後で障害からの復旧について説明するときに説明します。

このクラスは、`IHandleMessages<Ping>` を実装します。これは、`Handle` という 1 つのメソッドを定義します。 このインターフェイスは、種類が `Ping` のメッセージをエンドポイントが受信したときに、このハンドラーの `Handle` メソッドでそのメッセージを処理するように NServiceBus に命令します。 `Handle` メソッドは、メッセージ自体をパラメーターとして受け取り、応答、コマンドの送信、イベントの発行など、さらにメッセージング操作を行うことを可能にする `IMessageHandlerContext` を受け取ります。

`PingHandler` は単純です。`Ping` メッセージが受信されると、そのメッセージの詳細をログに記録し、新しい `Pong` メッセージで送信者に応答を返します。

> [!NOTE]
> Sender の構成で、`Ping` メッセージが Receiver にルーティングされるように指定しました。 NServiceBus は、特にメッセージの発信元を示すメタデータをそのメッセージに追加します。 このため、`Pong` 応答メッセージに対してユーザーがルーティング データを指定する必要はありません。メッセージは、その発信元である Sender に自動的にルーティングされます。
>

これで Sender と Receiver が両方とも正しく構成されたので、ソリューションを実行できます。

## <a name="run-the-solution"></a>ソリューションを実行する

ソリューションを起動するには、Sender と Receiver の両方を実行する必要があります。 Visual Studio Code を使用している場合は、"すべてをデバッグ" 構成を起動します。 Visual Studio を使用している場合は、Sender プロジェクトと Receiver プロジェクトの両方を起動するようにソリューションを構成します。

1. ソリューション エクスプローラーでソリューションを右クリックする
1. [スタートアップ プロジェクトの設定...] をクリックする
1. **[マルチ スタートアップ プロジェクト]** を選択する
1. Sender と Receiver の両方について、ドロップダウン リストで [開始] を選択する

ソリューションを起動します。 コンソール アプリケーションが 2 つ表示されます。1 つは Sender 用、もう 1 つは Receiver 用です。

# <a name="sender"></a>[送信者](#tab/Sender)

:::image type="content" source="./media/nservicebus/sender.png" alt-text="Receiver に対して Ping メッセージを送信するエンドポイントを示す画像":::

# <a name="receiver"></a>[受信者](#tab/Receiver)

:::image type="content" source="./media/nservicebus/receiver.png" alt-text="Sender から Ping メッセージを受信するエンドポイントを示す画像":::

---

`SenderWorker` バックグラウンド ジョブにより、Sender では `Ping` メッセージが毎秒ディスパッチされているのが確認できます。 Receiver では受信する各 `Ping` メッセージの詳細を表示し、Sender ではその応答で受信する各 `Pong` メッセージの詳細をログに記録します。

すべて動作することを確認できたので、それを壊してみましょう。

## <a name="resilience-in-action"></a>動作時の回復性

エラーは、ソフトウェア システムに付きものです。 コードの失敗は避けられず、ネットワーク障害、データベース ロック、サードパーティ API の変更、単純な古いコーディング エラーなど、さまざまな理由でエラーが発生します。

NServiceBus には、障害を処理する堅牢な回復機能があります。 メッセージ ハンドラーが失敗すると、メッセージは定義済みのポリシーに基づいて自動的に再試行されます。 再試行ポリシーには、即時再試行と遅延再試行の 2 種類があります。 これらがどのように動作するかを説明するには、実際の動作を確認するのが最善です。 Receiver エンドポイントに再試行ポリシーを追加してみましょう。

1. Sender プロジェクトで `Program.cs` を開きます。
1. `.EnableInstallers` という行の後に、次のコードを追加します。

```csharp
endpointConfiguration.SendFailedMessagesTo("error");
var recoverability = endpointConfiguration.Recoverability();
recoverability.Immediate(
    immediate =>
    {
        immediate.NumberOfRetries(3);
    });
recoverability.Delayed(
    delayed =>
    {
        delayed.NumberOfRetries(2);
        delayed.TimeIncrease(TimeSpan.FromSeconds(5));
    });
```

このポリシーの動作について説明する前に、実際の動作を見てみましょう。 回復性ポリシーをテストする前に、エラーをシミュレートする必要があります。 Receiver プロジェクトで `PingHandler` コードを開き、次の行をコメント解除します。

```csharp
throw new Exception("BOOM");
```

これで、Receiver が `Ping` メッセージを処理すると失敗します。 ソリューションをもう一度起動して、Receiver で何が起こるかを見てみましょう。 

信頼性の低い `PingHandler` では、すべてのメッセージが失敗します。 それらのメッセージに対して再試行ポリシーが開始されていることを確認できます。 メッセージは、最初に失敗すると、すぐに最大 3 回再試行されます。

:::image type="content" source="./media/nservicebus/immediate-retries.png" alt-text="メッセージを最大 3 回再試行する即時再試行ポリシーを示す画像":::

もちろん、メッセージは引き続き失敗します。そのため、3 回の即時再試行がすべて実行されると、遅延再試行ポリシーが開始され、メッセージは 5 秒間遅延されます。

:::image type="content" source="./media/nservicebus/delayed-retries.png" alt-text="即時再試行をもう一度試行する前に 5 秒の増分でメッセージを遅延する遅延再試行ポリシーを示す画像":::

 5 秒経過すると、メッセージはさらに 3 回再試行されます (つまり、即時再試行ポリシーが再び反復処理されます)。 これがまた失敗すると、NServiceBus はメッセージを再び遅延します。今度は、再試行が開始されるまで 10 秒間遅延されます。

再試行ポリシーを完全に実行しても `PingHandler` が成功しない場合、メッセージは `SendFailedMessagesTo` への呼び出しによって定義されている通り、`error` という名前の _集中管理_ エラー キューに配置されます。 

:::image type="content" source="./media/nservicebus/failed-message.png" alt-text="失敗したメッセージを示す画像":::

集中管理エラー キューの概念は、処理キューごとに配信不能キューがある Azure Service Bus の配信不能メカニズムとは異なります。 NServiceBus では、Azure Service Bus の配信不能キューは真の有害メッセージ キューとして機能するのに対して、最終的に集中管理エラー キューに配置されるメッセージは、必要に応じて後で再処理できます。

この再試行ポリシーは、本質的に一時的または半一時的な[いくつかの種類のエラー](https://particular.net/blog/but-all-my-errors-are-severe)に対処するのに役立ちます。 つまり、一時的なエラーに過ぎず、短時間遅延してからメッセージを再処理するだけで解消することが多いエラーに対処するのに役立ちます。 これには、ネットワーク障害、データベース ロック、サードパーティ API の停止などがあります。

メッセージがエラー キューに配置されれば、使い慣れたツールでそのメッセージの詳細を確認し、対処方法を決定できます。 たとえば、Particular Software の監視ツールである [ServicePulse](https://particular.net/servicepulse) を使用すると、メッセージの詳細とエラーの原因を確認できます。

:::image type="content" source="./media/nservicebus/servicepulse.png" alt-text="Particular Software の ServicePulse を示す図":::

詳細を確認した後、メッセージを元のキューに送り返して処理できます。 この操作を行う前に、メッセージを編集することもできます。 エラー キューに複数のメッセージがあり、それらが同じ理由で失敗した場合は、すべてのメッセージを元の送信先にバッチとして送り返すことができます。

次に、ソリューションを Azure のどこにデプロイすべきかを説明します。

## <a name="where-to-host-the-services-in-azure"></a>Azure におけるサービスのホスト場所

このサンプルでは、Sender エンドポイントと Receiver エンドポイントはコンソール アプリケーションとして実行されるように構成されています。 これらは、Azure Functions、Azure App Services、Azure Container Instances、Azure Kubernetes Service、Azure VM などのさまざまな Azure サービスでホストすることもできます。 たとえば、Azure 関数として実行されるように Sender エンドポイントを構成する方法を次に示します。

```csharp
[assembly: FunctionsStartup(typeof(Startup))]
[assembly: NServiceBusEndpointName("Sender")]

public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.UseNServiceBus(() =>
        {
            var configuration = new ServiceBusTriggeredEndpointConfiguration("Sender");
            var transport = configuration.AdvancedConfiguration.Transport;
            transport.Routing().RouteToEndpoint(typeof(Ping), "Receiver");

            return configuration;
        });
    }
}
```

Functions で NServiceBus を使用する方法の詳細については、NServiceBus ドキュメントの「[Azure Functions with Azure Service Bus](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus)」(Azure Service Bus と Azure Functions) を参照してください。

## <a name="next-steps"></a>次のステップ

Azure サービスで NServiceBus を使用する方法の詳細については、次の記事を参照してください。

- [Azure Service Bus の送信/応答サンプル](https://docs.particular.net/samples/azure-service-bus-netstandard/send-reply/)
- [Azure Functions での NServiceBus の使用](https://docs.particular.net/nservicebus/hosting/azure-functions/service-bus)
- [NServiceBus での Azure Service Bus 転送](https://docs.particular.net/transports/azure-service-bus/)
- [NServiceBus と Azure](https://docs.particular.net/nservicebus/azure/)
- [NServiceBus](https://particular.net/nservicebus)
- [NServiceBus のクイック スタート チュートリアル](https://docs.particular.net/tutorials/quickstart)
