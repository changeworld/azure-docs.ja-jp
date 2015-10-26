<properties
   pageTitle="Azure Service Fabric への既存のアプリケーションのデプロイ | Microsoft Azure"
   description="既存のアプリケーションを Azure Service Fabric クラスターにデプロイできるようにパッケージ化する方法のチュートリアル"
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
   ms.date="09/09/2015"
   ms.author="claudioc"/>

# Azure Service Fabric への既存のアプリケーションのデプロイ

Azure Service Fabric を使用して既存のアプリケーションをデプロイし、正常性の監視およびアプリケーション ライフサイクル管理 (ALM) を利用できます。

このチュートリアルでは、既存のアプリケーションの取得および Service Fabric クラスターへのデプロイメントのためのパッケージ化に関連するプロセスと基本的な概念について説明します。


## アプリケーション マニフェスト ファイルとサービス マニフェスト ファイルの概要

既存アプリケーションのデプロイの詳細に入る前に、Service Fabric デプロイメント モデルについて理解しておく必要があります。Service Fabric デプロイメント モデルは、主に 2 つのファイルに依存しています。


* **アプリケーション マニフェスト**

  アプリケーション マニフェストは、アプリケーションを記述する際に使用します。また、そのアプリケーションを構成するサービスと、サービスのデプロイ方法の定義に使用される他のパラメーター (インスタンス数など) をリストします。Service Fabric の世界では、アプリケーションは "アップグレード可能なユニット" です。アプリケーションは 1 つのユニットとしてアップグレードでき、このユニットで、潜在的な障害 (および潜在的なロールバック) がプラットフォームによって管理されます。これにより、アップグレード プロセスが完全に成功すること、また、失敗した場合でも、アプリケーションが不明/不安定な状態のままにならないことが保証されます。

  アプリケーション マニフェストの例を次に示します。

  ```xml <?xml version="1.0" encoding="utf-8"?> <ApplicationManifest ApplicationTypeName="actor2Application" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="actor2Pkg" ServiceManifestVersion="1.0.0.0" />
      <ConfigOverrides />
    </ServiceManifestImport>

    <DefaultServices>
      <Service Name="actor2">
        <StatelessService ServiceTypeName="actor2Type">
          <SingletonPartition />
        </StatelessService>
      </Service>
    </DefaultServices>

  </ApplicationManifest>```

* **サービス マニフェスト**

  サービス マニフェストは、サービスのコンポーネントを記述します。このデータには、サービスの名前および種類 (Service Fabric がサービスを管理する際に使用する情報)、そのコード、構成およびデータ コンポーネントのほか、デプロイされたサービスの構成に使用できる追加パラメーターがいくつか含まれます。ここでは、サービス マニフェストで使用できるすべてのパラメーターについて詳しく説明するのではなく、既存のアプリケーションを Service Fabric で実行するのに必要なサブセットを取り上げます。

  サービス マニフェストの例を次に示します

  ```xml <?xml version="1.0" encoding="utf-8"?> <ServiceManifest Name="actor2Pkg" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"> <ServiceTypes> <StatelessServiceType ServiceTypeName="actor2Type" /> </ServiceTypes>

    <CodePackage Name="Code" Version="1.0.0.0">
      <EntryPoint>
        <ExeHost>
          <Program>actor2.exe</Program>
        </ExeHost>
      </EntryPoint>
    </CodePackage>

    <ConfigPackage Name="Config" Version="1.0.0.0" />

    <Resources>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" />
      </Endpoints>
    </Resources>
  </ServiceManifest>```

## アプリケーション パッケージ ファイルの構造
powershell コマンドレットなどを使用してアプリケーションをデプロイするには、そのアプリケーションは、次の定義済みディレクトリ構造に従っている必要があります。

```
\applicationmanifest.xml
\MyServicePkg
    \servicemanifest.xml
    \code
    \config
    \data
```

root には、アプリケーションを定義する applicationmanifest.xml ファイルが含まれます。アプリケーションに含まれる各サービスのサブディレクトリには、サービスに必要なすべてのアーティファクト、つまり servicemanifest.xml と、通常は次の 3 つのディレクトリが含まれています。

- *code*: サービス コードが含まれています。
- *config*: settings.xml ファイル (および、必要に応じてその他のファイル) が含まれています。実行時にサービスがこのファイルにアクセスし、特定の構成設定を取得します。
- *data*: 追加のローカル データが格納されている追加ディレクトリ。サービスでこのデータが必要になる場合があります。注: Data には一時的なデータのみを格納します。フェールオーバー中など、サービスの再配置が必要な場合、Service Fabric ではこのデータ ディレクトリには変更がコピー/レプリケートされません。

注: Code、Config、および Data には任意のディレクトリ名を使用できます。ApplicationManifest ファイルでは必ず同じ値を使用する必要があります。

## 既存のアプリをパッケージ化するプロセス

既存のアプリケーションをパッケージ化するプロセスは、次の手順に基づいています。

- パッケージ ディレクトリ構造を作成する
- アプリケーションのコードと構成ファイルを追加する
- サービス マニフェスト ファイルを更新する
- アプリケーション マニフェストを更新する


### パッケージ ディレクトリ構造を作成する
最初に、上記の説明に従ってディレクトリ構造を作成します。ディレクトリを作成し、アプリケーション マニフェストとサービス マニフェストを、Visual Studio で以前作成した既存のプロジェクトからコピーしました。

![][1] ![][2]


### アプリケーションのコードと構成ファイルを追加する
ディレクトリ構造を作成したら、アプリケーションのコードと構成ファイルを、Code ディレクトリおよび Config ディレクトリの下に追加できます。Code ディレクトリまたは Config ディレクトリに、追加のディレクトリまたはサブ ディレクトリを作成することもできます。Service Fabric は、アプリケーション root ディレクトリのコンテンツの xcopy を実行します。したがって、2 つの上位ディレクトリである Code および Settings を作成する以外に、使用できる定義済みの構造はありません (ただし、必要に応じて別の名前を選択できます。詳細については、次のセクションを参照してください)。

>[AZURE.NOTE]アプリケーションに必要なすべてのファイルと依存関係を必ず含めるようにしてください。Service Fabric では、アプリケーション パッケージ のコンテンツが、アプリケーションのサービスがデプロイされるクラスター内のすべてのノードにコピーされます。パッケージには、アプリケーションを実行するのに必要なすべてのコードが含まれています。依存関係がインストール済みであることを想定するのはお勧めしません。

### サービス マニフェスト ファイルを編集する
次の手順では、サービス マニフェスト ファイルを編集して、次の情報を含めます。

- サービスの種類の名前。これは、Service Fabric がサービスを識別するために使用する "Id" です。
- アプリケーションの起動に使用するコマンド (ExeHost)
- アプリケーションをセットアップ/構成するために実行する必要のあるスクリプト (SetupEntrypoint)

次に示すのは node.js アプリケーションを "パッケージ化" する `servicemanifest.xnml` ファイルの例です。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VisualObjectsNodejsWebServicePkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
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

  <ConfigPackage Name="Config" Version="1.0.0.0"/>

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>

</ServiceManifest>
```

更新する必要のあるファイルの別の部分を見てみましょう。

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
</ServiceTypes>
```

- `ServiceTypeName` には、任意の名前を指定できます。値は、サービスを特定するために `applicationmanifest.xml` で使用されます。
- `UserImplicitHost = "true"` を指定する必要があります。この属性は、サービスが自己完結型アプリに基づいているため、必要なのは、プロセスとして起動して、正常性を監視するだけであることを Service Fabric に伝えます。

### CodePackage
CodePackage は、サービスのコードの場所 (およびバージョン) を指定します。

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 要素では、サービスのコードが含まれるアプリケーション パッケージのディレクトリの名前を指定します。また、`CodePackage` にも `version` 属性があります。この属性を使用すると、コードのバージョンを指定できるほか、Service Fabric の ALM インフラストラクチャを使用してサービスのコードをアップグレードできる可能性もあります。


### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```


サービス マニフェスト ファイルの `Entrypoint` 要素では、サービスの起動方法を指定します。`ExeHost` 要素は、サービスの起動に使用する実行可能ファイル (および引数) を指定します。

- `Program`: サービスを開始するために実行する必要がある実行可能ファイルの名前を指定します。
- `Arguments`: 実行可能ファイルに渡される引数を指定します。引数を持つパラメーターの一覧を使用できます。
- `WorkingFolder`: 開始するプロセスの作業ディレクトリを指定します。次の 2 つの値を指定できます。
	- `CodeBase`: 作業ディレクトリは、アプリケーション パッケージ内の Code ディレクトリに設定されます (次に示す構造の `Code` ディレクトリ)
	- `CodePackage`: 作業ディレクトリは、アプリケーション パッケージ (`MyServicePkg`) の root に設定されます
- `WorkingDirectory` 要素は、アプリケーション スクリプトまたは初期化スクリプトのいずれかが相対パスを使用できるように、正しい作業ディレクトリを設定するのに便利です。

`WorkingFolder` 要素に対して指定できる別の値もありますが (`Work`)、既存のアプリケーションを使用するシナリオにはあまり適していません。


```
\applicationmanifest.xml
\MyServicePkg
	\servicemanifest.xml
	\code
		 \bin
			  \ ...
	\config
	\data
		\...
```


#### セットアップのエントリ ポイント

```xml
<SetupEntryPoint>
  <ExeHost>
    <Program>scripts\myAppsetup.cmd</Program>
  </ExeHost>
</SetupEntryPoint>
```

`SetupEntrypoint` を使用して、サービスのコードが起動される前に実行する必要がある実行可能ファイルまたはバッチ ファイルを指定します。これはオプションの要素なので、必要な初期化/セットアップが存在しない場合は追加する必要はありません。Entrypoint は、サービスを再起動するたびに実行されます。SetupEntrypoint は 1 つだけのため、アプリケーションのセットアップ/構成に複数のスクリプトが必要な場合は、セットアップ/構成スクリプトを 1 つのバッチ ファイルにバンドルする必要があります。`Entrypoint` 要素と同様、`SetupEntrypoint` は、すべての種類のファイル (実行可能ファイル、バッチ ファイル、powershell コマンドレット) を実行できます。上記の例では、`SetupEntrypoint` は、Code ディレクトリの scripts サブディレクトリにあるバッチ ファイル myAppsetup.cmd に基づいています (`WorkingDirectory` 要素が `Code` に設定されている場合)。

## アプリケーション マニフェスト ファイル

`servicemanifest.xml` ファイルを構成したら、適切なサービスの種類と名前が使用されるように `applicationmanifest.xml` ファイルを少し変更する必要があります。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount = "1">
    </StatelessService>
  </Service>
</DefaultServices>
```

### ServiceManifestImport

`ServiceManifestImport` では、アプリに追加する 1 つ以上のサービスを指定できます。サービスは、`servicemanifest.xml` ファイルが配置されているディレクトリの名前を指定する `ServiceManifestName` で参照されます。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### DefaultServices

アプリケーション マニフェスト ファイルの `DefaultServices` 要素は、一部のサービス プロパティを定義するときに使用されます。

```xml
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
    </StatelessService>
  </Service>
</DefaultServices>
```

* `ServiceTypeName` は、サービスの 'ID' として使用されます。既存のアプリケーションを移行するときに、`ServiceTypeName` はサービスの一意の ID である必要があります。
* `StatelessService`: Service Fabric では、ステートレスとステートフルの 2 種類のサービスがサポートされます。既存のアプリケーションを移行する場合、サービスはステートレス サービスです。したがって、必ず `StatelessService` を使用する必要があります。

Service Fabric サービスは、さまざまな "構成" にデプロイできます。たとえば、単一または複数のインスタンスとしてデプロイしたり、Service Fabric クラスターの各ノードに 1 つのサービス インスタンスが配置されるようにデプロイしたりできます。`applicationmanifest.xml` ファイルでは、アプリケーションをデプロイする方法を指定できます。

* `InstanceCount`: Service Fabric クラスターで開始するサービス インスタンスの数を指定します。`InstanceCount` 値は、デプロイするアプリケーションの種類に応じて設定できます。よく使用されるシナリオは 2 つあります。

	* `InstanCount = "1"`: 1 つのサービス インスタンスのみがクラスターにデプロイされます。サービスがデプロイされるノードは、Service Fabric スケジューラによって決まります。アプリケーションが複数のインスタンスで実行され、そのアプリケーションに異なる構成が必要な場合は、インスタンス数が 1 でも意味をなします。その場合は、複数のサービスを同じアプリケーション マニフェスト ファイルで定義して、`InstanceCount = "1"` を使用するほうが簡単です。最終的には、同じサービスに複数インスタンスが存在することになり、それぞれが特別に構成されます。`InstanceCount` に 1 を超える値を設定する意味があるのは、まったく同じ構成で複数のインスタンスを使用するのが目的の場合のみです。

	* `InstanceCount ="-1"`: 1 つのサービス インスタンスが、Service Fabric クラスターのすべてのノードにデプロイされます。最終的には、1 つ (のみ) のサービス インスタンスがクラスターの各ノードに存在することになります。クライアント アプリケーションはクラスター内のいずれかのノードに "接続" するだけでエンドポイントを使用できるため、フロントエンド アプリケーション (REST エンドポイントなど) には便利な構成です。この構成は、たとえば、Service Fabric クラスターのすべてのノードがロード バランサーに接続され、クライアント トラフィックを、クラスター内のすべてのノードで実行されているサービスに分散できる場合にも使用できます。


### テスト
既存のアプリケーションについては、アプリケーションと構成のスクリプトにエラーがないかどうかを確認するコンソールのログを表示できると非常に便利です。コンソールのリダイレクトは、`servicemanifest.xml` ファイルで `ConsoleRedirection` 要素を使用して構成できます。

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder></WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection` を使用すると、コンソール出力 (stdout および stderr) を作業ディレクトリにリダイレクトして、Service Fabric クラスター内のアプリケーションのセットアップまたは実行中にエラーが発生していないことを確認できます。

	* `FileRetentionCount` によって、作業ディレクトリ内に保存されているファイルの数を判断できます。たとえば、値が 5 の場合は、これまでの 5 回の実行のログ ファイルが作業ディレクトリに格納されていることを意味します。
	* `FileMaxSizeInKb` では、ログ ファイルの最大サイズを指定します。

ログ ファイルは、サービスの作業ディレクトリのいずれかに保存されます。ファイルの場所を特定するには、Service Fabric エクスプローラーを使用して、サービスが実行されているノードと、現在使用されている作業ディレクトリを確認する必要があります。

Service Fabric エクスプローラーで、サービスが実行されているノードを特定します。

![][3]

サービスが実行されているノードがわかったら、使用されている作業ディレクトリを見つけることができます。

![][4]

サービスの名前を選択すると、右側のパネルで、サービス コードと設定が格納されている場所を確認できます。

![][5]

"ディスクの場所" フィールドでリンクをクリックすると、サービスが実行されているディレクトリにアクセスできます。

![][6]

ログ ディレクトリには、すべてのログ ファイルが含まれています。

注: ここでは、クラスターで実行されているサービスの単一インスタンスの例を示しています。複数のインスタンスがある場合は、サービスが実行されているすべてのノードのログ ファイルをチェックする必要があります。


## 次の手順
現在、アプリのディレクトリ構造の root で既存のアプリケーションを指定するだけで、そのアプリケーションをパッケージ化できるツールに取り組んでいます。このツールでは、マニフェスト ファイルを生成し、Service Fabric サービス内のアプリケーションを "変換" するのに必要な基本設定を構成します。

従来の Service Fabric アプリケーションを開発する方法の詳細については、[ここ](service-fabric-develop-your-service-index.md)をご覧ください。

[1]: ./media/service-fabric-deploy-existing-app/directory-structure-1.png
[2]: ./media/service-fabric-deploy-existing-app/directory-structure-2.png
[3]: ./media/service-fabric-deploy-existing-app/service-node-1.png
[4]: ./media/service-fabric-deploy-existing-app/service-node-2.png
[5]: ./media/service-fabric-deploy-existing-app/service-node-3.png
[6]: ./media/service-fabric-deploy-existing-app/service-node-4.png

<!---HONumber=Oct15_HO3-->