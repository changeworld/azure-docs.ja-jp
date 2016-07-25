<properties
   pageTitle="Service Fabric Reliable Actors の使用 | Microsoft Azure"
   description="このチュートリアルでは、Service Fabric Reliable Actors を使用して簡単なアクターベースのサービスを作成、デバッグ、デプロイする手順について説明します。"
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
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# Reliable Actors の使用
ここでは、Azure Service Fabric Reliable Actors の基本と、Visual Studio で簡単な Reliable Actors アプリケーションを作成、デバッグ、デプロイする手順について説明します。

## インストールとセットアップ
開始する前に、マシン上に Service Fabric 開発環境がセットアップされていることを確認します。セットアップする必要がある場合は、[開発環境のセットアップ方法](service-fabric-get-started.md)に関する詳細な手順を参照してください。

## 基本的な概念
Reliable Actors の使用を開始するには、4 つの基本的な概念を理解する必要があります。

* **アクター サービス**。Reliable Actors は、Service Fabric インフラストラクチャにデプロイできる Reliable Services にパッケージ化されます。サービスは、1 つまたは複数のアクターをホストできます。サービスごとの単一のアクターと複数のアクターのトレードオフについては、後で詳細に説明します。ここでは、1 つのアクターのみを実装する必要があると仮定します。
* **アクター インターフェイス**。アクター インターフェイスは、アクターの厳密に型指定されたパブリック インターフェイスを定義するために使用されます。Reliable Actor モデルの用語では、アクター インターフェイスに、アクターが理解し、処理できるメッセージの種類が定義されています。アクター インターフェイスは、他のアクターとクライアント アプリケーションがメッセージをアクターに (非同期に) "送信" するために使用されます。Reliable Actors は複数のインターフェイスを実装できます。後述のように、HelloWorld アクターは IHelloWorld インターフェイスを実装できますが、異なるメッセージや機能を定義する ILogging インターフェイスも実装できます。
* **アクター登録**。Reliable Actors サービスでは、アクターの種類を登録する必要があります。登録すると、Service Fabric は新しい種類を認識し、新しいアクターの作成に使用できるようになります。
* **ActorProxy クラス**。ActorProxy クラスは、インターフェイスを介して公開されるメソッドを呼び出すためにクライアント アプリケーションで使用されます。ActorProxy クラスは、次の 2 つの重要な機能を提供します。
	* 名を解決します。クラスター内のアクターを特定できます (ホストされているクラスターのノードを検索できます)。
	* エラーを処理します。たとえば、アクターをクラスター内の別のノードに再配置する必要がある障害が発生した後に、メソッドの呼び出しを再試行し、アクターの場所を改めて特定することができます。

アクター インターフェイスに関する次の規則に注意する必要があります。

- アクター インターフェイス メソッドはオーバー ロードできません。
- アクター インターフェイス メソッドには、out、ref、optional パラメーターを使用できません。
- ジェネリック インターフェイスはサポートされません。

## Visual Studio での新しいプロジェクトの作成
Visual Studio 用の Service Fabric ツールをインストールした後、新しいプロジェクトの種類を作成できます。新しいプロジェクトの種類は、**[新しいプロジェクト]** ダイアログ ボックスの **[クラウド]** カテゴリにあります。


![Visual Studio 用の Service Fabric ツール - 新しいプロジェクト][1]

次のダイアログ ボックスで、作成するプロジェクトの種類を選択できます。

![Service Fabric プロジェクトのテンプレート][5]

HelloWorld プロジェクトで、Service Fabric Reliable Actors サービスを使用してみましょう。

ソリューションの作成後は、次の構造を確認します。

![Service Fabric プロジェクトの構造][2]

## Reliable Actors の基本的な構成要素

通常の Reliable Actors ソリューションは、次の 3 つのプロジェクトで構成されます。

* **アプリケーション プロジェクト (MyActorApplication)**。デプロイメントのためにすべてのサービスを一緒にパッケージ化するプロジェクトです。アプリケーションを管理するための *ApplicationManifest.xml* と PowerShell スクリプトが含まれています。

* **インターフェイス プロジェクト (MyActor.Interfaces)**。アクターのインターフェイス定義が含まれているプロジェクトです。MyActor.Interfaces プロジェクトでは、ソリューションでアクターによって使用されるインターフェイスを定義できます。アクター インターフェイスは、任意の名前でどのプロジェクトでも定義できます。ただし、このインターフェイスは、アクター実装とアクターを呼び出すクライアントが共有するアクター コントラクトを定義するため、通常は、他の複数のプロジェクトで共有できる、アクター実装とは別のアセンブリで定義します。

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **アクター サービス プロジェクト (MyActor)**。アクターをホストする Service Fabric サービスの定義に使用されるプロジェクトです。アクターの実装が含まれています。アクター実装は、基本型 `Actor` から派生し、MyActor.Interfaces プロジェクトで定義されたインターフェイスを実装するクラスです。

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class MyActor : Actor, IMyActor
{
    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

アクター サービスは、Service Fabric ランタイムのサービスの種類に登録する必要があります。アクター サービスでアクター インスタンスを実行するには、アクター型もアクター サービスに登録する必要があります。`ActorRuntime` 登録メソッドは、アクターに対してこの処理を実行します。

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Visual Studio で新しいプロジェクトから開始する場合に、アクター定義が 1 つだけであれば、Visual Studio によって生成されるコードには既定で登録が含まれます。サービスで他のアクターを定義する場合は、次のコードを使用してアクター登録を追加する必要があります。

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [AZURE.TIP] Service Fabric Actors ランタイムは、[アクター メソッドに関連する一部のイベントとパフォーマンス カウンター](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)を出力します。これらは、診断やパフォーマンスの監視に役立ちます。


## デバッグ

Visual Studio 用の Service Fabric ツールは、ローカル マシンでのデバッグをサポートします。デバッグ セッションを開始するには、F5 キーを押します。(必要に応じて) Visual Studio でパッケージをビルドします。また、ローカルの Service Fabric クラスターにアプリケーションをデプロイし、デバッガーをアタッチします。

デプロイ プロセス中は、**[出力]** ウィンドウで進行状況を確認できます。

![Service Fabric デバッグ出力ウィンドウ][3]


## 次のステップ
 - [Reliable Actors の Service Fabric プラットフォームの使用方法](service-fabric-reliable-actors-platform.md)
 - [アクターの状態管理](service-fabric-reliable-actors-state-management.md)
 - [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
 - [Actor API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [コード サンプル](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=AcomDC_0713_2016-->