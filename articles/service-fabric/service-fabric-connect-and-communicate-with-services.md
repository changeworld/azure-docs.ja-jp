<properties
   pageTitle="Azure Service Fabric のサービスとの通信と接続 | Microsoft Azure"
   description="Service Fabric アプリケーションのサービスに接続して通信する方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# サービスとの通信
マイクロサービスの世界では、全体的なソリューションは各サービスが特殊なタスクを実行する多くの各種サービスで構成されます。これらのマイクロサービスは、相互に通信してエンド ツー エンドのワークフローを可能にします。また、サービスに接続して通信するクライアント アプリケーションもあります。このドキュメントでは、Azure Service Fabric で作成するサービスとの通信のセットアップが、Service Fabric によっていかに容易になるかを説明します。

## 主要な概念
以下は、サービスへの通信をセットアップする際に注意する必要がある主要な概念の一部です。

### 通信はクライアント サーバーとして表される
Service Fabric 通信 API は、同じクラスター内で動作する 2 つのサービス間の対話の場合でも、クライアント サーバー型の対話を表します。クライアントまたは別のサービスから接続されるターゲット サービスは、サーバーとして機能し、受信要求をリッスンします。クライアント (クラスター内の別のサービスに過ぎない場合がある) は、サーバーに接続して呼び出しを行います。

### サービスの移動
分散システムでは、実行するサービス インスタンスが、時間の経過と共に 1 台のコンピューターから別のコンピューターに移動することがあります。その理由はさまざまで、たとえばリソース分散のための負荷のメトリックを使用して構成する場合や、アップグレード、フェールオーバー、スケール アウト、その他のさまざまな状況が原因になります。この影響は、サービス インスタンスのエンドポイント アドレスが変更されることです。サービス インスタンスとの通信をセットアップするには、次のループを実行する必要があります。これらの詳細は、Service Fabric によって提供される通信 API を使用すると、抽象化されます。

* **解決**: Service Fabric 内のすべての名前付きサービス インスタンスは一意の URI を持ちます。たとえば、`"fabric:/MyApplication/MyService"` は名前付きサービスに対して固定されたままになります。各名前付きサービス インスタンスはエンドポイントも公開しますが、これは名前付きサービス インスタンスが移動されるときに変更される可能性があります。これは、URL は不変でも IP アドレスは変わることがある Web サイトと似ています。Web サイト URL を IP アドレスに解決する Web 上の DNS と同様に、Service Fabric には、URI をエンドポイントに解決する、名前付きサービスと呼ばれるシステム サービスがあります。この手順には、サービス インスタンスの URI をエンドポイントに解決することが含まれます。

* **接続**: 名前付きサービス URI がエンドポイントのアドレスに解決されると、その次の手順として、そのサービスとの接続が試行されます。サービスの移動でエンドポイントのアドレスが変わっていた場合、この接続は失敗する可能性があります。サービス移動の原因としては、コンピューター エラーやリソース分散があります。

* **再試行**: 再接続の試行が失敗した場合、先行する解決と接続の手順を再試行する必要があり、このサイクルは接続が確立されるまで繰り返されます。

## 通信 API のオプション
Service Fabric の一環として、通信 API 用にいくつかのオプションが用意されています。最適なオプションの決定は、プログラミング モデル、通信フレームワーク、およびサービスが作成されているプログラミング言語に応じて異なります。

### 高信頼アクターの通信
高信頼アクター API を使用して作成されたサービスでは、すべての通信の詳細は抽象化されています。通信は ActorProxy へのメソッド呼び出しとして発生するため、ここで読み取りを停止してもかまいません。

### Reliable Services の通信オプション
Reliable Services API を使用してサービスが作成された場合、いくつか異なるオプションがあります。通信プロトコルの選択により、使用する Service Fabric の通信 API が決まります。

* **特定のプロトコルがない場合**: 特定の通信フレームワークを選択していないが、すぐに実行する必要がある場合に理想的なオプションは、アクター通信モデルと似たモデルを利用できる[既定のスタック](service-fabric-reliable-services-communication-remoting.md)です。これは、サービスの通信を開始する最も簡単ですばやい方法です。これは、厳密に型指定された RPC 通信を提供し、ほとんどの通信の詳細を抽象化するため、コードでリモート サービスを呼び出すと、ローカル オブジェクトのインスタンスでメソッドを呼び出しているように見えます。これらのクラスは、解決、接続、再試行、およびエラー処理を抽象化しながら、通信チャネルをセットアップして、メソッド呼び出しに基づく通信モデルを可能にします。このため、`ServiceRemotingListener` クラスはサーバー側で使用され、`ServiceProxy` クラスは通信のクライアント側で使用されます。

* **HTTP**: HTTP ベースの通信が提供する柔軟性を活用するため、解決、接続、および再試行ロジックを抽象化したまま通信メカニズムを定義可能な Service Fabric 通信 API を使用できます。たとえば、Web API を使用して、通信メカニズムを指定し、[`ICommunicationClient` クラスと `ServicePartitionClient` クラス](service-fabric-reliable-services-communication.md) を利用し、通信をセットアップできます。
* **WCF**: 通信フレームワークとして WCF を使用する既存のコードがある場合、サーバー側で `WcfCommunicationListener` を使用し、クライアントで `WcfCommunicationClient` および `ServicePartitionClient` クラスを使用することができます。詳細については、[WCF ベースの通信スタックの実装](service-fabric-reliable-services-communication-wcf.md)に関するこの記事を参照してください。

* **カスタム プロトコルを含むその他の通信フレームワーク**: カスタムの通信プロトコルを含むその他の通信フレームワークの利用を計画している場合、検出と接続のすべての作業を抽象化したまま通信スタックをプラグインできる Service Fabric 通信 API があります。詳細については、[Reliable Services 通信モデル](service-fabric-reliable-services-communication.md)に関するこの記事を参照してください。

### 異なるプログラミング言語で記述されたサービスの間の通信
Service Fabric のすべての通信 API は現在 C# でのみ使用できます。これは、Java や Node.JS など、その他のプログラミング言語で記述されたサービスがある場合は、独自の通信メカニズムを作成する必要があることを意味します。

## 次のステップ
* [Reliable Services フレームワークによって提供される既定の通信スタック](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services 通信モデル](service-fabric-reliable-services-communication.md)
* [OWIN 自己ホストによる Microsoft Azure Service Fabric Web API サービスの概要](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services の WCF ベースの通信スタック](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0224_2016-->