<properties
   pageTitle="Service Fabric アプリケーションに対する Application Insights の設定"
   description="Application Insights で、アプリケーションの Service Fabric のイベントを受信します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2015"
   ms.author="mattrow"/>

# Service Fabric アプリケーションに対する Application Insights の設定
 この記事では、Service Fabric アプリケーションに対して Application Insights を有効にする手順を説明します。

## 前提条件

この記事は、Visual Studio で Service Fabric アプリケーションが既に作成されていることを前提としています。方法については、[ここをクリック](service-fabric-reliable-services-quick-start.md)してください。

## NuGet パッケージのインストール
Nuget パッケージ Microsoft.ServiceFabric.Telemetry.ApplicationInsights のプレリリース版は、Service Fabric SDK の一部としてリリースされます。このパッケージは、Service Fabric EventSource イベントを Application Insights に結び付け、Service Fabric アプリケーションの自動インストルメンテーションを提供します。このパッケージは、アプリケーションによって自動的に生成される新しいイベントによって更新が続行されます。

パッケージをインストールするには、次の手順を実行します。

1. Service Fabric アプリケーション用の NuGet パッケージ マネージャーを開きます。これは、Visual Studio でプロジェクトを右クリックし、[NuGet パッケージの管理] を選択することで実行できます。
2. Service Fabric SDK に含まれるパッケージの一覧を表示するには、パッケージのソースとして [Microsoft Azure Service Fabric] を選択する必要があります。![VS2015 NuGet パッケージ マネージャー](media/service-fabric-diagnostics-application-insights-setup/AI-nuget-package-manager.jpg)
3. 左側の Microsoft.ServiceFabric.Telemetry.ApplicationInsights パッケージを選択します。
4. [インストール] をクリックして、インストール プロセスを開始します。
5. 使用許諾契約書を確認し、同意します。

## Service Fabric イベントの有効化
Application Insights 内で Service Fabric イベントを自動的に受信するには、リスナーを有効にする必要があります。次のコード行をアプリケーションに挿入することで、これを実行できます。

```csharp
    Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
```
 
### StatefulActor\\Program.cs の例:

```csharp
    public static void Main(string[] args)
    {
        Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(StatefulActor));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
```

高信頼アクターのランタイムから出力されたイベントについては[ここ](service-fabric-reliable-actors-diagnostics.md)で確認でき、Reliable Services のランタイムについては[ここ](service-fabric-reliable-services-diagnostics.md)で確認できます。

高信頼アクターのランタイム メソッドの呼び出しを取得するには、EventLevel.Verbose を使用する必要があることに注意してください (上の例を参照)。

## Application Insights の設定
インストルメンテーション キーは、Service Fabric アプリケーションと Application Insights リソースを結び付けるものです。「[Application Insight のガイド](../app-insights-create-new-resource.md#create-an-application-insights-resource)」に従うと、インストルメンテーション キーを取得する方法を学習できます。リソースを作成する場合は、アプリケーションの種類に [その他] を選択します。

![その他の AI アプリの種類を選択する](media/service-fabric-diagnostics-application-insights-setup/AI-app-type-other.JPG)

インストルメンテーション キーを取得したら、ApplicationInsights.config ファイルに次のように挿入できます。

```xml
    <InstrumentationKey>INSERT YOUR KEY HERE</InstrumentationKey>
```

## データの表示
ニーズに合うように [App Insights ブレードをカスタマイズ](../app-insights-metrics-explorer.md)できます。ほとんどの Service Fabric のイベントは 'カスタム イベント' として表示され、Fabric Actor メソッドの呼び出しとサービスの RunAsync() 呼び出しは要求として表示されます。これらのイベントを要求としてモデル化することで、グラフを作成するときに、'要求名' ディメンションと '要求の期間' メトリックを使用できます。新しいグラフ、メトリック、およびイベントは、引き続き追加して後で利用することができます。

## 次のステップ
Application Insights を使用して、Service Fabric アプリをインストルメント化することについて説明します。

- [Application Insights を使ってみる](../app-insights-get-started.md)
- [独自のカスタム イベントとメトリックを作成する方法を学習する](../app-insights-custom-events-metrics-api.md)
 

<!---HONumber=July15_HO2-->