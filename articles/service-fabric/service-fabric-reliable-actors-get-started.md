<properties
   pageTitle="信頼性の高いアクターの概要 | Microsoft Azure"
   description="このチュートリアルでは、Service Fabric の信頼性の高いアクターを使用して標準的な HelloWorld の作成、デバッグ、およびデプロイを行う手順について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/17/2015"
   ms.author="claudioc"/>

# 信頼性の高いアクター: 正規の HelloWorld のチュートリアル シナリオ
ここでは、Service Fabric の信頼性の高いアクターの基本と、Visual Studio で簡易な HelloWorld アプリケーションの作成、デバッグ、およびデプロイの手順について説明します。

## インストールとセットアップ
開始する前に、マシン上に Service Fabric 開発環境がセットアップされていることを確認します。開発環境をセットアップする方法の詳細については、[ここ](service-fabric-get-started.md)を参照してください。

## 基本的な概念
信頼性の高いアクターの使用を開始するには、4 つの基本的な概念を理解する必要があります。

* **アクター サービス**。信頼性の高いアクターは、Service Fabric インフラストラクチャに配置できるサービスにパッケージ化されます。サービスは、1 つまたは複数のアクターをホストできます。サービスごとの単一のアクターと複数のアクターのトレードオフについては、後で詳細に説明します。ここでは、1 つのアクターのみを実装する必要があると仮定します。
* **アクター インターフェイス**。アクター インターフェイスは、アクターのパブリック インターフェイスを定義するために使用されます。アクター モデルの用語では、アクターがプロセスを理解できるメッセージの種類を定義します。アクター インターフェイスは、メッセージをアクターに (非同期に)「送信」するために、他のアクターまたはクライアント アプリケーションによって使用されます。信頼性の高いアクターは、複数のインターフェイスを実装できます。以下に示すように、HelloWorld アクターは IHelloWorld インターフェイスを実装できますが、別のメッセージ/機能を定義する ILogging インターフェイスも実装できます。
* **サーバー登録**。アクター サービスで、Service Fabric が新しい型を認識し、新しいアクターの作成に使用できるように、アクター型を登録する必要があります。
* **ActorProxy クラス**。ActorProxy クラスは、アクターにバインドし、そのインターフェイスを介して公開されるメソッドを呼び出すために使用されます。ActorProxy クラスは、次の 2 つの重要な機能を提供します。
	* 名前解決: クラスター内のアクターを特定する (ホスト先であるクラスター内のノードを特定する) ことができます。
	* エラーの処理: メソッドの呼び出しを再試行し、たとえば、アクターをクラスター内の別のノードに再配置する必要を生じさせる障害の後に、アクターの場所を再度決定することができます。

## Visual Studio での新しいプロジェクトの作成
Visual Studio 用の Service Fabric ツールをインストールした後、新しいプロジェクトの種類を作成できます。新しいプロジェクトの種類は、[新しいプロジェクト] ダイアログの [クラウド] カテゴリにあります。


![][1]


HelloWorld プロジェクトで、Service Fabric アクター サービスを使用してみます。

ソリューションを作成したら、次の構造が表示されます。

![][2]

## 信頼性の高いアクターの基本的な構成要素

通常の信頼性の高いアクターのソリューションは、次の 3 つのプロジェクトで構成されます。

* アプリケーション プロジェクト (HelloWorldApplication)。配置のためにすべてのサービスを一緒にパッケージ化するプロジェクトです。アプリケーションを管理するための ApplicationManifest.xml および PowerShell スクリプトが含まれています。

* インターフェイス プロジェクト (HelloWorld.Interfaces)。アクターのインターフェイス定義が含まれているプロジェクトです。インターフェイス プロジェクトでは、アクターによってソリューションで使用されるインターフェイスを定義できます。

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld.Interfaces
{
    public interface IHelloWorld : IActor
    {
        Task<string> SayHello(string greeting);
    }
}

```

* サービス プロジェクト (HelloWorld)。アクターをホストする Service Fabric サービスの定義に使用されるプロジェクトです。ほとんどの場合は編集する必要のない定型コード (ServiceHost.cs)、およびアクターの実装が含まれています。アクターの実装には、ベース型 (アクター) から派生し、.Interfaces プロジェクトで定義されたインターフェイスを実装するクラスの実装が含まれます。

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using HelloWorld.Interfaces;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld
{
    public class HelloWorld : Actor, IHelloWorld
    {
        public Task<string> SayHello(string greeting)
        {
            return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
        }
    }
}

```

アクター サービス プロジェクトには、Service Fabric サービスを作成するコードが含まれています。サービス定義には、新しいアクターのインスタンス化に使用できるようにアクター型が登録されます。

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(HelloWorld));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
}  

```

Visual Studio で新しいプロジェクトから開始する場合に、アクター定義が 1 つだけであれば、Visual Studio によって生成されるコードには既定で登録が含まれます。サービスで他のアクターを定義する場合は、次のコードを使用してアクター登録を追加する必要があります。

```csharp

fabricRuntime.RegisterActor(typeof(MyNewActor));


```

## デバッグ

Visual Studio 用の Service Fabric ツールは、ローカル マシンでのデバッグをサポートします。デバッグ セッションを開始するには、F5 キーを押します。Visual Studio は、ローカル Service Fabric クラスター上でアプリケーションを構築 (必要な場合)、パッケージ化、および配置し、デバッガーをアタッチします。エクスペリエンスは、ASP.NET アプリケーションのデバッグに似ています。配置プロセス中には、出力ウィンドウで進行状況を確認できます。

![][3]

## アプリケーションのデプロイ
Visual Studio から、Service Fabric アプリケーション プロジェクトを選択して右クリックすることで、デバッガーを起動しなくても、ローカル クラスターでアプリケーションをパッケージ化して配置することもできます。

![][4]

* [**配置**]: アプリをパッケージ化し、配置プロセスを開始します。
* [**配置の削除**]: ローカル クラスターからアプリケーションを削除するために使用できます。
* [**パッケージ**]: アプリケーションをパッケージ化します。このアクションは、アプリケーションを Azure などの別のクラスター上に配置する準備をするのに役立ちます。

## 次のステップ

[Service Fabric アクターの概要](service-fabric-reliable-actors-introduction.md)



<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
 

<!---HONumber=July15_HO2-->