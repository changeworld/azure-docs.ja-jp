<properties
   pageTitle="Reliable Actors の概要 | Microsoft Azure"
   description="このチュートリアルでは、Service Fabric Reliable Actors を使用して標準的な HelloWorld の作成、デバッグ、およびデプロイを行う手順について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Reliable Actors: 正規の HelloWorld のチュートリアル シナリオ
ここでは、Azure Service Fabric Reliable Actors の基本と、Visual Studio で簡易な HelloWorld アプリケーションの作成、デバッグ、およびデプロイの手順について説明します。

## インストールとセットアップ
開始する前に、マシン上に Service Fabric 開発環境がセットアップされていることを確認します。セットアップする必要がある場合は、[開発環境のセットアップ方法](service-fabric-get-started.md)に関する詳細な手順を参照してください。

## 基本的な概念
Reliable Actors の使用を開始するには、4 つの基本的な概念を理解する必要があります。

* **アクター サービス**。Reliable Actors は、Service Fabric インフラストラクチャにデプロイできるサービスにパッケージ化されます。サービスは、1 つまたは複数のアクターをホストできます。サービスごとの単一のアクターと複数のアクターのトレードオフについては、後で詳細に説明します。ここでは、1 つのアクターのみを実装する必要があると仮定します。
* **アクター インターフェイス**。アクター インターフェイスは、アクターのパブリック インターフェイスを定義するために使用されます。Reliable Actor モデルの用語では、アクター インターフェイスに、アクターが理解し、処理できるメッセージの種類が定義されています。アクター インターフェイスは、他のアクターとクライアント アプリケーションがメッセージをアクターに (非同期に) "送信" するために使用されます。Reliable Actors は複数のインターフェイスを実装できます。後述のように、HelloWorld アクターは IHelloWorld インターフェイスを実装できますが、異なるメッセージや機能を定義する ILogging インターフェイスも実装できます。
* **アクター登録**。Reliable Actors サービスでは、アクターの種類を登録する必要があります。登録すると、Service Fabric は新しい種類を認識し、新しいアクターの作成に使用できるようになります。
* **ActorProxy クラス**。ActorProxy クラスは、アクターにバインドし、そのインターフェイスを介して公開されるメソッドを呼び出すために使用されます。ActorProxy クラスは、次の 2 つの重要な機能を提供します。
	* 名を解決します。クラスター内のアクターを特定できます (ホストされているクラスターのノードを検索できます)。
	* エラーを処理します。たとえば、アクターをクラスター内の別のノードに再配置する必要がある障害が発生した後に、メソッドの呼び出しを再試行し、アクターの場所を改めて特定することができます。

## Visual Studio での新しいプロジェクトの作成
Visual Studio 用の Service Fabric ツールをインストールした後、新しいプロジェクトの種類を作成できます。新しいプロジェクトの種類は、**[新しいプロジェクト]** ダイアログの **[クラウド]** カテゴリにあります。


![Visual Studio 用の Service Fabric ツール - 新しいプロジェクト][1]

次のダイアログ ボックスで、作成するプロジェクトの種類を選択できます。

![Service Fabric プロジェクトのテンプレート][5]

HelloWorld プロジェクトで、Service Fabric Reliable Actors サービスを使用してみましょう。

ソリューションの作成後は、次の構造を確認します。

![Service Fabric プロジェクトの構造][2]

## Reliable Actors の基本的な構成要素

通常の Reliable Actors ソリューションは、次の 3 つのプロジェクトで構成されます。

* **アプリケーション プロジェクト (HelloWorldApplication)**。デプロイメントのためにすべてのサービスを一緒にパッケージ化するプロジェクトです。アプリケーションを管理するための **ApplicationManifest.xml** および PowerShell スクリプトが含まれています。

* **インターフェイス プロジェクト (HelloWorld.Interfaces)**。アクターのインターフェイス定義が含まれているプロジェクトです。HelloWorld.Interfaces プロジェクトでは、アクターによってソリューションで使用されるインターフェイスを定義できます。

```csharp

namespace MyActor.Interfaces
{
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;

    public interface IMyActor : IActor
    {
        Task<string> HelloWorld();
    }
}

```

* **サービス プロジェクト (HelloWorld)**。アクターをホストする Service Fabric サービスの定義に使用されるプロジェクトです。ほとんどの場合は編集する必要のない定型コード (ServiceHost.cs)、およびアクターの実装が含まれています。アクターを実装するには、基本型 (アクター) から派生するクラスを実装する必要があります。また、HelloWorld.Interfaces プロジェクトに定義されているインターフェイスも実装します。

```csharp

namespace MyActor
{
    using System;
    using System.Threading.Tasks;
    using Interfaces;
    using Microsoft.ServiceFabric.Actors;

    internal class MyActor : StatelessActor, IMyActor
    {
        public Task<string> HelloWorld()
        {
            throw new NotImplementedException();
        }
    }
}

```

Reliable Actors サービス プロジェクトには、Service Fabric サービスを作成するコードが含まれています。サービス定義には、1 つ以上のアクターの種類が登録されており、新しいアクターのインスタンス化に使用できます。

```csharp

namespace MyActor
{
    using System;
    using System.Fabric;
    using System.Threading;
    using Microsoft.ServiceFabric.Actors;

    internal static class Program
    {
        private static void Main()
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {
                    fabricRuntime.RegisterActor<MyActor>();

                    Thread.Sleep(Timeout.Infinite);  // Prevents this host process from terminating so services keeps running.
                }
            }
            catch (Exception e)
            {
                ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}

```

Visual Studio で新しいプロジェクトから開始する場合に、アクター定義が 1 つだけであれば、Visual Studio によって生成されるコードには既定で登録が含まれます。サービスで他のアクターを定義する場合は、次のコードを使用してアクター登録を追加する必要があります。

```csharp

fabricRuntime.RegisterActor<MyActor>();


```

## デバッグ

Visual Studio 用の Service Fabric ツールは、ローカル マシンでのデバッグをサポートします。デバッグ セッションを開始するには、F5 キーを押します。(必要に応じて) Visual Studio でパッケージをビルドします。また、ローカルの Service Fabric クラスターにアプリケーションをデプロイし、デバッガーをアタッチします。エクスペリエンスは、ASP.NET アプリケーションのデバッグに似ています。

デプロイメント プロセス中には、**[出力]** ウィンドウで進行状況を確認できます。

![Service Fabric デバッグ出力ウィンドウ][3]


## 次のステップ

- [Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)
- [Actors API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
- [コード サンプル](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=AcomDC_0121_2016-->