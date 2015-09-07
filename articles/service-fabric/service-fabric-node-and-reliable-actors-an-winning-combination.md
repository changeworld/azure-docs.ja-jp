<properties
   pageTitle="node.js と信頼性の高いアクター | Microsoft Azure"
	description="信頼性の高いアクターを使用し、Azure Service Fabric プラットフォームの上で実行される node.js express アプリケーションを構築する方法についてのチュートリアル。"
	services="service-fabric"
	documentationCenter=".net"
	authors="clca"
	manager="timlt"
	editor=""/>

<tags
   ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="NA"
	ms.date="08/17/2015"
	ms.author="claudioc"/>


# node.js と信頼性の高いアクター: 優先順位の高い組み合わせ
この記事では、node.js と信頼性の高いアクターを使用するアプリケーションの構築方法について概要を説明します。最終的なソリューションは、主にアプリケーション (Web/REST API) のフロントエンド側を提供するために使用される Javascript コードと、複雑な計算に有効な C# との組み合わせになります。Service Fabric プログラミング モデルを活用することにより、追加設定なしで、スケーラブルで信頼性の高いアプリケーションを提供できます。プロセスは、次の手順に基づいています。

1. Service Fabric tools for Visual Studio を使用して、Service Fabric ステートレスまたはステートフル アクター プロジェクトを新規に作成する
2. node.js プロジェクトを、たとえば、[Visual Studio 向け node.js ツール](https://github.com/Microsoft/nodejstools/releases/tag/v1.1.RC)を使用して作成する 
3. node.js プロジェクト (たとえば、基本的な　express 4 アプリケーション) を Service Fabric ソリューションに追加する。ソリューション「既存のプロジェクトの追加」を使用すれば、node.js プロジェクトを簡単に含めることができます。 
4. VS Tools for Service Fabric を使用してデプロイできるように node.js アプリケーションをパッケージ化する。

## node.js プロジェクトの作成
node.js プロジェクトを作成し、依存関係を追加したら、プロジェクトのディレクトリ構造を変更します。変更方法としては、Service Fabric Tools for Visual Studio が、その他の Service Fabric サービスと同様にアプリケーションをパッケージ化してデプロイするために必要としたディレクトリ構造にプロジェクトが従うようにします。目標は、Visual Studio のデプロイメント プロセスを使用して node.js アプリケーションとその他のサービスを一緒にソリューションにデプロイすることができるように、ディレクトリ構造に 1 回だけ変更を加えることです。ディレクトリ構造に対して必要な変更は次のとおりです。

1. PackageRoot ディレクトリを作成する
2. PackageRoot の下に Code ディレクトリを作成する
3. ファイルとディレクトリをすべて、Code ディレクトリに移動する

変更が完了すると、Visual Studio でのプロジェクト構造は次のようになります。

![][8]

これでわかるように、node.js コード全体が PackageRoot/Code ディレクトリに置かれています。Service Fabric では、アプリケーションがデプロイされるノード上にインストール済みのアプリケーション/ライブラリが存在することを想定していないので、すべての依存関係を含める必要があります。node.js の場合は、Service Fabric がコードを実行できるように node.exe を含める必要があります (node.exe は program files\\nodejs の下に置かれています)。

![][3]

## servicemanifest.xml メタデータ ファイルの追加
node.js アプリケーションをデプロイするには、アプリケーションのデプロイおよび起動の方法を決定するために Service Fabric で使用されるいくつかのプロパティを指定するのに必要なメタデータ ファイルを追加する必要があります。

servicemanifest.xml の内容の例を以下に示します。更新を必要とする重要な要素の詳細については、[この記事](service-fabric-deploy-existing-app.md) を参照してください。ただし、通常、更新する必要がある要素は次のとおりです。

* Name (ServiceManifest 要素)
* ServiceTypeName (StatelessServiceType 要素)
* Arguments (ExeHost 要素)。アプリケーションを起動するときに使用される js ファイルを指定します。


```xml

<?xml version="1.0" encoding="utf-8"?>

<ServiceManifest Name="NodejsFrontendPkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="NodejsFrontendType" UseImplicitHost="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0.0" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

> [AZURE.NOTE]servicemanifest.xml オプションのいずれかが node.js で動作しない場合があるので注意してください。場合によっては、`ConsoleRedirection` は、node.js モジュール (たとえば、Express) が stdout と stderr の fd を 1 と 2 と想定したならば、動作しません。

`servicemanifest.xml` ファイルが node.js プロジェクトに追加されると、プロジェクトの構造は次のようになります。

![][4]

## Service Fabric バイナリの追加
次の手順は、Service Fabric クラスターで実行されているアクターとの接続に使用する Service Fabric バイナリを追加することです。Edge.js サンプルを見ればわかるように、アセンブリへの参照がいくつかあります。それらのアセンブリを edge.js で使用できるようにするには、アセンブリを node.js コードと一緒にコピーする必要があります。既存のプロジェクトからバイナリをコピーするのが最も簡単な方法です。コード ディレクトリに含める必要があるファイル (edge.js で使用される) は次のとおりです。

```
Microsoft.ServiceFabric.Actors.dll
Microsoft.ServiceFabric.Collections.dll
Microsoft.ServiceFabric.Data.dll
Microsoft.ServiceFabric.Data.Log.dll
Microsoft.ServiceFabric.ReplicatedStore.dll
Microsoft.ServiceFabric.Replicator.dll
Microsoft.ServiceFabric.Services.dll
ServiceFabricServiceModel.dll
System.Fabric.Common.Internal.dll
System.Fabric.Common.Internal.Strings.resources.dll
System.Fabric.dll
```

バイナリをプロジェクトに追加すると、プロジェクトの構造は次のようになります。

![][5]

## applicationmanifest ファイルに node.js プロジェクトへの参照を追加
次の手順は、Visual Studio Tools for Service Fabric を使用してプロジェクトをデプロイできるように、アプリケーション マニフェストに node.js サービスを追加することです。Service Fabric Tools for Visual Studio には node.js プロジェクトとの統合がなく、手動で追加しなければならないため、この手順が必要となります。

`applicationmanifest.xml` ファイルでは、次の要素を追加する必要があります。

* `ServiceManifestImport`
* `Service`

> [AZURE.NOTE]`servicemanifest.xml` で `ServiceName` と `ServiceTypeName` を指定するために使用したのと同じ名前を必ず使用します。`applicationmanifest.xml` ファイルは、次のようになります。

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeServiceFabricSampleApplication" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeServiceFabricSamplePkg" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="VisualObjectsNodejsWebServicePkg" ServiceManifestVersion="1.0.0.0" />
       </ServiceManifestImport>
   <DefaultServices>
      <Service Name="NodeServiceFabricSampleActorService">
         <StatelessService ServiceTypeName="NodeServiceFabricSampleActorServiceType">
            <UniformInt64Partition PartitionCount="9" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatelessService>
      </Service>
     <Service Name="NodejsFrontendPkg">
       <StatelessService ServiceTypeName="NodejsFrontendType">
         <SingletonPartition />
       </StatelessService>
     </Service>
   </DefaultServices>
</ApplicationManifest>

```

## node.js アプリケーションでの信頼性の高いアクターの使用
プロジェクトの構造が設定されたので、今度は、.NET アプリケーションの場合と同様に node.js アプリケーションをクラスター内の信頼できるアクターに接続するためのコードに焦点を当てます。使用するシナリオは、クライアント アプリケーションのフロント エンド/ゲートウェイとして機能する node.js アプリケーションを構築し、さらに、クライアント アプリケーションで使用できる Web ベースのアプリケーションまたは一連の REST API を公開するというものです。node.js はアプリケーションのフロント エンドを提供します。一方、信頼性の高いアクターは、アプリケーションにおけるスケーラブルで信頼性のある「アプリケーション サーバー」レイヤーを提供します。Service Fabric では、[ActorProxy クラス](service-fabric-reliable-actors-introduction.md#actor-communication) を使用して信頼性の高いアクターを呼び出すことができます。幸いに、[Edge.js](https://github.com/tjanczuk/edge) という .NET コードを呼び出すのに使用できる優れた node.js モジュールがあります。コンピューターへの edge のインストール方法については、Github リポジトリに関する手順説明を参照してください。

 
![][2]

Visual Studio で NPM または NPM UI のいずれかを使用して edge をインストールしたら、新規にインストールしたモジュールを、Code サブディレクトリの下にある node\_modules ディレクトリに移動する必要があります (すでに node.js プロジェクトの構造を変更し、すべてのコードを PackageRoot/Code ディレクトリの下に移動してあります)。edge を 使用して .NET コードを呼び出す方法については、Github の edge.js リポジトリによい例があります。ActorProxy クラスを使用して信頼性の高いアクターを呼び出す必要がある場合の簡単なコード例を次に示します。

```javascript

var getActorStatus = edge.func(function () {

/*

    #r "Microsoft.ServiceFabric.Actors.dll"
    #r "System.Globalization.dll"
    #r "System.dll"
    #r  "visualobjects.interfaces.dll"
    
    using Microsoft.ServiceFabric.Actors;
    using System.Globalization;
    using VisualObjects.Interfaces;
    using System.Threading.Tasks;
    using System; 


public class Startup
    {
         public async Task<object> Invoke(dynamic input)
        {

            var objectId = (string) input.actorId;
            var serviceUri = (string) input.serviceUri;
            var actId = new ActorId(objectId);
            var serId = new Uri(serviceUri);
            var actorProxy = ActorProxy.Create<IVisualObjectActor>(actId, serId);
            var buffer = await actorProxy.GetStateAsJsonAsync();
            if (!string.IsNullOrEmpty(buffer))
                return buffer;
                else 
                return "null";
         }
  }
 
 */ 

});

```

> [AZURE.NOTE]ActorProxy では、Actor インターフェイスを使用して、どのアクターに接続する必要があるかを把握します。node.js プロセスで実行される管理対象コードがそのクラスをインスタンス化できるようにするには、Actor インターフェイス アセンブリをその他の Service Fabric の dll と同様に Code ディレクトリにコピーする必要があります。注: インターフェイス内のメソッド/パラメーターに変更を加える場合は毎回、dll をコピー (または VS でビルド後のアクションを設定) することが必要になります。

![][6]

## デプロイメント
この時点では、Service Fabric Tools for Visual Studio ツールを使用してプロジェクトをデプロイすることができます。プロセスの最後の手順は、Service Fabric アプリケーション プロジェクトのプロジェクトを参照することで、それをアプリケーション パッケージに取り込み、クラスターにデプロイできるようにすることです。

![][9]
 
たとえば、ソリューション コンテキスト メニューを使用して、アプリケーション (node.js アプリケーションが取り込まれる) をデプロイすることができます。

![][10]

## 次のステップ
* [信頼性の高いアクター](service-fabric-reliable-actors-introduction.md)の詳細
* Service Fabric [アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)の詳細
* [Service Fabric アプリケーション](service-fabric-application-upgrade.md)のアップグレードの詳細 

<!-- images -->
[1]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure.PNG
[2]: ./media/service-fabric-node-and-reliable-actors-app/edge-js.PNG
[3]: ./media/service-fabric-node-and-reliable-actors-app/node-exe.PNG
[4]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-manifest.PNG
[5]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-dlls.PNG
[6]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-interface-dll.PNG
[7]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-config.PNG
[8]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure1.PNG
[9]: ./media/service-fabric-node-and-reliable-actors-app/application-project-reference.PNG
[10]: ./media/service-fabric-node-and-reliable-actors-app/solution-deploy.PNG

<!---HONumber=August15_HO9-->