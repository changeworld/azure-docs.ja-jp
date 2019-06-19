---
title: Azure Application Insights におけるテレメトリ チャネル | Microsoft Docs
description: .NET/.NET Core 用の Azure Application Insights SDK でテレメトリ チャネルをカスタマイズする方法について説明します。
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66255807"
---
# <a name="telemetrychannel-in-application-insights"></a>Application Insights における TelemetryChannel

TelemetryChannel は、[Azure Application Insights SDK](../../azure-monitor/app/app-insights-overview.md) の不可欠な要素です｡ これにより、テレメトリのバッファー処理と Application Insights サービスへの送信が管理されます。 SDK の .NET バージョンと .NET Core バージョンには、`InMemoryChannel` と `ServerTelemetryChannel` の 2 つの TelemetryChannel が組み込まれています。 この記事では、ユーザーがチャネルの動作をカスタマイズする方法も含め、各チャネルの詳細を説明します。

## <a name="what-is-a-telemetrychannel"></a>TelemetryChannel とは何ですか?

`TelemetryChannel` は、バッファー処理と、Application Insights サービスへのテレメトリ項目の送信の役割を担います。テレメトリ項目は、クエリと解析の目的のために Application Insights サービスに保存されます。 インターフェイス [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) を実装するクラスはすべてこれに該当します。

TelemetryChannel の `Send(ITelemetry item)` メソッドは、`TelemetryInitializer` と `TelemetryProcessor` がすべて呼び出された後に呼び出されます。 つまり、`TelemetryProcessor` によって削除された項目はチャネルに到達しません。 `Send()` は通常、項目をすぐにバックエンドに送信することはありません。 通常はメモリ内でバッファー処理されてから、バッチで効率的に送信されます。

[LiveMetrics](live-stream.md) にも、テレメトリのライブ ストリーミングが可能なカスタム チャネルがあります。 このチャネルは通常のテレメトリ チャネルからは独立しているので、`LiveMetrics` によって使用されるチャネルにはこのドキュメントの内容が該当しません。

## <a name="built-in-telemetrychannels"></a>組み込みの TelemetryChannel

Application Insights の .NET/.NET Core SDK には、2 つのチャネルがあらかじめ組み込まれています。

* **InMemoryChannel**
`InMemoryChannel` は、項目が送信されるまでメモリ内にその項目をバッファー処理する軽量なチャネルです。 項目はメモリ内にバッファー処理され、30 秒ごとまたは 500 項目がバッファー処理されるごとにフラッシュされます。 このチャネルは、障害時にテレメトリの送信を再試行しないので、保証される信頼性は最小限です。 このチャネルでは項目がディスク上に保持されないので、未送信の項目はいずれも、アプリケーションのシャットダウン時に (正常にシャットダウンした場合でも、そうでない場合でも) 完全に失われます。 このチャネルによって実装されている `Flush()` メソッドを使用すると、メモリ内のすべてのテレメトリ項目を同期的に強制フラッシュできます。 これは、同期的にフラッシュすることが理想的な実行時間の短いアプリケーションに適しています。

    このチャネルは `Microsoft.ApplicationInsights` NuGet パッケージそのものの一部として提供され、他に何も構成されていないときに SDK が使用する既定のチャネルです。

* **ServerTelemetryChannel**
`ServerTelemetryChannel` は、再試行ポリシーとローカル ディスクへのデータ保存機能を備えたより高度なチャネルです。 一時的なエラーが発生した場合、このチャネルはテレメトリの送信を再試行します。 また、このチャネルは、ネットワーク障害時またはテレメトリが大量にある場合に、ローカル ディスク ストレージを使用してディスク上に項目を保持します。 このように再試行メカニズムとローカル ディスクへの保存機能を備えているため、このチャネルは信頼性が高いと考えられ、あらゆる運用環境のシナリオに推奨されています。 このチャネルは、リンクされている公式ドキュメントに従って構成された [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) および [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) アプリケーションにおいて既定となっています。このチャネルは、プロセスの実行時間が長いサーバーのシナリオに最適です。 このチャネルによって実装される [`Flush()`](#which-channel-should-i-use) メソッドは同期的ではありません。

    このチャネルは、NuGet パッケージ `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel` として入手できるほか、NuGet パッケージ `Microsoft.ApplicationInsights.Web` または `Microsoft.ApplicationInsights.AspNetCore` の使用時に自動的に提供されます。

## <a name="configuring-telemetrychannel"></a>TelemetryChannel の構成

テレメトリ チャネルは、アクティブな `TelemetryConfiguration` で必要な `TelemetryChannel` を設定することで構成できます。 Asp.Net アプリケーションの構成時は、`TelemetryConfiguration.Active` に `TelemetryChannel` を設定するか、`ApplicationInsights.config` を変更します。 ASP.NET Core アプリケーションの構成時は、必要なチャネルを依存関係挿入コンテナーに追加します。

次に、ユーザーがチャネルの `StorageFolder` を構成するときの例を示します。 `StorageFolder` は構成可能な設定の 1 つです。 構成設定の完全な一覧は、[設定に関するセクション](telemetry-channels.md#configurable-settings-in-channel)で説明しています。

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>ApplicationInsights.Config を使用した ASP.NET アプリケーション向けの設定

[ApplicationInsights.config](configuration-with-applicationinsights-config.md) の以下のセクションは、`StorageFolder` にカスタムの場所が構成された ServerTelemetryChannel を示しています。

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET アプリケーションのコードでの構成

次のコードは、ServerTelemetryChannel の `StorageFolder` にカスタムの場所を設定しています。 このコードはアプリケーションの先頭に追加する必要があり、通常は `Global.aspx.cs` の Application_Start() メソッドに追加されます。

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

## <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core アプリケーションのコードでの構成

`Startup.cs` クラスの `ConfigureServices` メソッドを以下のように変更します。

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!NOTE]
> ASP.NET Core アプリケーションでは、`TelemetryConfiguration.Active` を使用してチャネルを構成することはお勧めしませんのでご注意ください。

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>.NET/.NET Core コンソール アプリケーション向けのコードでの TelemetryChannel の構成

コンソール アプリについては、以下に示すとおり、コードが .NET および .NET Core の場合と同じです。

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel の操作の詳細

`ServerTelemetryChannel` はメモリ内バッファーに到着した項目をバッファー処理します。 シリアル化と圧縮処理の後、30 秒ごとまたは 500 項目がバッファー処理されるごとに `Transmission` インスタンスに保存されます。 1 つの `Transmission` インスタンスは最大 500 個の項目を保持することができ、Application Insights サービスへの 1 回の https 呼び出し経由で送信されるテレメトリのバッチを表します。 既定では、最大 10 件の `Transmission` を並列で送信できます。 これよりも早いペースでテレメトリが到着する場合またはネットワーク/Application Insights のバックエンドが低速な場合は、`Transmission` がメモリ内に保存されます。 このメモリ内転送バッファーの既定の容量は 5 MB です。 メモリの容量を超えると、`Transmission` は最大 50 MB までローカル ディスクに保存されます。 ネットワークの問題がある場合も、`Transmission` がローカル ディスクに保存されます。 アプリケーションがクラッシュしたときは、ローカル ディスクに保存されている項目のみが保持され、それらはアプリケーションがもう一度起動したときに送信されます。

## <a name="configurable-settings-in-channel"></a>チャネルの構成可能な設定

各チャネルの構成可能な設定の完全な一覧はこちらです:

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

`ServerTelemetryChannel` で最もよく使用される設定の一覧を次に示します。

1. `MaxTransmissionBufferCapacity` - メモリ内のバッファー転送のためにチャネルが使用するバイト単位での最大メモリ量。 この容量の上限に達すると、新しい項目がローカル ディスクに直接保存されます。 既定値は 5 MB です。 これよりも高い値を設定すると、ディスクの使用量が減少しますが、メモリ内の項目はアプリケーションのクラッシュ時に失われますので注意してください。

2. `MaxTransmissionSenderCapacity` - Application Insights に同時送信される `Transmission` の最大数。 既定値は 10 ですが、より多い値にすることもできます。 これは、きわめて大量のテレメトリが生成されるときに推奨されます。通常はロード テストのときや、サンプリングをオフにしたときが該当します。

3. `StorageFolder` - 必要に応じてディスクに項目を保存するために、チャネルによって使用されるフォルダー。 Windows では、明示的な構成の指定がない限り、%LocalAppData% または %Temp% が使用されます。 Windows 以外の環境では、ユーザーが**必ず**有効な場所を構成する必要があります。有効な場所を構成しないと、テレメトリがローカル ディスクに保存されません。

## <a name="which-channel-should-i-use"></a>どのチャネルを使用すればよいですか?

実行時間の長いアプリケーションを運用するシナリオのほとんどでは、`ServerTelemetryChannel` がお勧めです。 `ServerTelemetryChannel` の `Flush()` メソッドの実装は同期的ではなく、`Flush()` により保留中の全項目がメモリまたはディスクから送信される保証はありません。 アプリケーションがシャットダウンされそうな状況のシナリオでこのシナリオを使用する場合には、このチャネルで `Flush()` を呼び出した後、多少の遅延を発生させることが推奨されます。 必要な遅延時間の正確な数値は、メモリ内の項目数または `Transmissions` 数、ディスク内の数、バックエンドへの送信数、チャネルがエクスポネンシャル バックオフ シナリオの途中であるかどうかなどの要素に左右されるため、予測できません。 同期的なフラッシュの必要がある場合は、`InMemoryChannel` をお勧めします。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*ApplicationInsights チャネルでは、テレメトリの送信は保証されますか? 保証されない場合、テレメトリが失われる可能性のあるシナリオとは、どのようなものですか?*

* 簡潔に言うと、組み込みチャネルのいずれにおいても、バックエンドへのテレメトリ送信に関して、トランザクションのような保証はありません。 `ServerTelemetryChannel` は `InMemoryChannel` よりも高度であり、テレメトリ送信の信頼性が高いものの、テレメトリ送信がベストエフォートでもあるので、一部のシナリオではテレメトリが失われることがあります。 テレメトリが失われるいくつかの一般的なシナリオは次のとおりです。

1. アプリケーションがクラッシュすると、メモリ内の項目が失われます。
1. ネットワークの停止中や Application Insights バックエンドでの問題発生時には、テレメトリはローカル ディスクに保存されます。 ただし、24 時間が経過した項目は破棄されます。 そのため、ネットワークの問題が長びくと、テレメトリが失われます。
1. Windows でテレメトリが保存される既定のディスクの場所は、%LocalAppData% または %Temp% です。 これらは通常、マシンのローカルの場所です。 アプリケーションがある場所から別の場所に物理的に移行した場合、この場所に保存されているテレメトリは失われます。
1. Azure Web Apps (Windows) では、既定のディスクの場所は "D:\local\LocalAppData" です。 この場所は永続化されず、アプリの再起動やスケールアウトなどによって消去されるので、これらの場所に保存されているテレメトリは失われます。 ユーザーはストレージを "D:\home" などの永続化された場所にオーバーライドできますが、これらの永続化された場所はリモート ストレージによって提供されているため、低速になる場合があります。

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*ServerTelemetryChannel は Windows 以外のシステムで動作しますか?*

* パッケージ/名前空間の名前は WindowsServer ですが、このチャネルは、次の例外を除き、Windows 以外のシステムでもサポートされます。 Windows 以外の場合、チャネルが既定でローカル ストレージ フォルダーを作成することはありません。 ユーザーは、ローカル ストレージ フォルダーを作成し、それを使用するようにチャネルを構成する必要があります。 ローカル ストレージの構成を行えば、チャネルは Windows および Windows 以外のシステムで同じように動作します。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*SDK では一時的なローカル ストレージが作成されますか? データはストレージで暗号化されますか?*

* SDK は、ネットワークの問題の発生中またはスロットリング中に、テレメトリの項目をローカル ストレージに保存します。 このデータはローカルでは暗号化されません。
Windows システムの場合、SDK は一時的なローカル フォルダーを自動的に TEMP または APPDATA ディレクトリに作成し、アクセスを管理者と現在のユーザーのみに制限します。
Windows 以外のシステムの場合、SDK がローカル ストレージを自動的に作成することはないので、既定ではデータがローカルに保存されません。 ユーザーは、自分自身でストレージ ディレクトリを作成し、それを使用するようにチャネルを構成できます。 この場合、このディレクトリのセキュリティ保護については、ユーザーが責任を負います。
[データ保護とプライバシー](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)の詳細をご確認ください。

## <a name="open-source-sdk"></a>オープンソース SDK
すべての Application Insights SDK と同じく、チャネルもオープン ソースです。 コードの閲覧、投稿、問題のレポートは[公式の GitHub リポジトリ](https://github.com/Microsoft/ApplicationInsights-dotnet)で行ってください。

## <a name="next-steps"></a>次の手順

* [サンプリング](../../azure-monitor/app/sampling.md)
* [SDK のトラブルシューティング](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
