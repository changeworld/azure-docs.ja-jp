<properties
   pageTitle="Azure Service Fabric へのカスタム アプリケーションのデプロイ | Microsoft Azure"
   description="既存のアプリケーションを Azure Service Fabric クラスターにデプロイできるようにパッケージ化する方法のチュートリアル"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>

# Service Fabric にカスタム アプリケーションをデプロイする

Node.js アプリケーション、Java アプリケーション、Service Fabric のネイティブなアプリケーションなど、あらゆる種類の既存のアプリケーションを実行できます。Service Fabric では、これらのアプリケーションをステートレスなサービスのように処理し、可用性などのメトリックに基づいてクラスター内のノードに配置します。この記事では、既存のアプリケーションを Service Fabric クラスターにデプロイしてパッケージ化する方法について説明します。

## Service Fabric でカスタム アプリケーションを実行するメリット

Service Fabric クラスターでアプリケーションを実行するメリットはいくつかあります。

- 高可用性: Service Fabric で実行されるアプリケーションは、追加設定しなくても高い可用性を実現します。Service Fabric によって、アプリケーションのインスタンスが常に 1 つ稼働している状態が維持されます。
- 正常性の監視: 標準装備の Service Fabric 正常性監視により、アプリケーションが稼働しているかどうかが検出され、障害が発生した場合は診断情報が提供されます。   
- アプリケーション ライフ サイクル管理: Service Fabric では、ダウンタイムのないアップグレードに加え、アップグレード中に問題が発生した場合に以前のバージョンにロールバックできます。    
- 密度: 各アプリケーションを専用のハードウェアで実行する必要がないクラスターで複数のアプリケーションを実行できます。

この記事では、既存のアプリケーションをパッケージ化し、Service Fabric にデプロイするための基本的な手順について説明します。


## アプリケーション マニフェスト ファイルとサービス マニフェスト ファイルの概要

既存アプリケーションのデプロイの詳細に入る前に、Service Fabric のパッケージ化とデプロイのモデルについて理解しておく必要があります。Service Fabric パッケージ化デプロイ モデルは、主に 2 つのファイルに依存しています。


* **アプリケーション マニフェスト**

  アプリケーション マニフェストを使用して、アプリケーションを記述します。また、そのアプリケーションを構成するサービスと、サービスのデプロイ方法の定義に使用されるパラメーター (インスタンス数など) も記載されます。Service Fabric では、アプリケーションは "アップグレード可能なユニット" です。アプリケーションは 1 つのユニットとしてアップグレードでき、このユニットで、潜在的な障害 (および潜在的なロールバック) がプラットフォームによって管理されます。これにより、アップグレード プロセスが完全に成功すること、また、失敗した場合でも、アプリケーションが不明/不安定な状態のままにならないことが保証されます。


  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationManifest ApplicationTypeName="actor2Application"
                       ApplicationTypeVersion="1.0.0.0"
                       xmlns="http://schemas.microsoft.com/2011/01/fabric"
                       xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

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

  </ApplicationManifest>
  ```

* **サービス マニフェスト**

  サービス マニフェストには、サービスのコンポーネントを記述します。このデータには、サービスの名前および種類 (Service Fabric がサービスを管理する際に使用する情報)、そのコード、構成およびデータ コンポーネントのほか、デプロイされたサービスの構成に使用できる追加パラメーターがいくつか含まれます。ここでは、サービス マニフェストで使用できるすべてのパラメーターについて詳しく説明するのではなく、既存のアプリケーションを Service Fabric で実行するのに必要なサブセットを取り上げます。


  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <ServiceManifest Name="actor2Pkg"
                   Version="1.0.0.0"
                   xmlns="http://schemas.microsoft.com/2011/01/fabric"
                   xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <ServiceTypes>
      <StatelessServiceType ServiceTypeName="actor2Type" />
    </ServiceTypes>

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
  </ServiceManifest>
  ```

## アプリケーション パッケージ ファイルの構造
Service Fabric にアプリケーションをデプロイするには、そのアプリケーションが次の定義済みディレクトリ構造に従っている必要があります。この構造の例を次に示します。

```
|-- AppplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|--Settings.xml
    |--data    
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

root には、アプリケーションを定義する ApplicationManifest.xm ファイルが含まれます。アプリケーションに含まれる各サービスのサブディレクトリには、サービスに必要なすべてのアーティファクト、つまり ServiceManifest.xml と、通常は次の 3 つのディレクトリが含まれています。

- *code*: サービス コードが含まれています。
- *config*: settings.xml ファイル (および、必要に応じてその他のファイル) が含まれています。実行時にサービスがこのファイルにアクセスし、特定の構成設定を取得します。
- *data*: 追加のローカル データが格納されている追加ディレクトリ。サービスでこのデータが必要になる場合があります。注: Data には一時的なデータのみを格納します。フェールオーバー中など、サービスの再配置が必要な場合、Service Fabric ではこのデータ ディレクトリには変更がコピー/レプリケートされません。

注: `config` ディレクトリと `data` ディレクトリは、必要がなければ作成する必要はありません。

## 既存のアプリをパッケージ化するプロセス

既存のアプリケーションをパッケージ化するプロセスは、次の手順に基づいています。

- パッケージ ディレクトリ構造を作成する
- アプリケーションのコードと構成ファイルを追加する
- サービス マニフェスト ファイルを更新する
- アプリケーション マニフェストを更新する

>[AZURE.NOTE]\: ApplicationPackage を自動的に作成できるパッケージ化ツールを用意しています。このツールは現在プレビューの段階です。[ここ](http://aka.ms/servicefabricpacktool)からダウンロードできます。

### パッケージ ディレクトリ構造を作成する
まず、次の説明に従ってディレクトリ構造を作成します。

### アプリケーションのコードと構成ファイルを追加する
ディレクトリ構造を作成したら、アプリケーションのコード ファイルと構成ファイルを、code ディレクトリおよび config ディレクトリに追加できます。code ディレクトリまたは config ディレクトリに、追加のディレクトリまたはサブ ディレクトリを作成することもできます。Service Fabric は、アプリケーションのルート ディレクトリのコンテンツに対して xcopy を実行します。したがって、2 つの上位ディレクトリである code および settings を作成する以外に、使用できる定義済みの構造はありません (ただし、必要に応じて別の名前を選択できます。詳細については、次のセクションを参照してください)。

>[AZURE.NOTE]アプリケーションに必要なすべてのファイルと依存関係を必ず含めるようにしてください。Service Fabric では、アプリケーション パッケージ のコンテンツが、アプリケーションのサービスがデプロイされるクラスター内のすべてのノードにコピーされます。パッケージには、アプリケーションを実行するのに必要なすべてのコードが含まれています。依存関係がインストール済みであることを想定するのはお勧めしません。

### サービス マニフェスト ファイルを編集する
次の手順では、サービス マニフェスト ファイルを編集して、次の情報を含めます。

- サービスの種類の名前。これは、Service Fabric がサービスを識別するために使用する "Id" です。
- アプリケーションの起動に使用するコマンド (ExeHost)
- アプリケーションをセットアップ/構成するために実行する必要のあるスクリプト (SetupEntrypoint)

`ServiceManifest.xml` の例を次に示します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

更新する必要のあるファイルの別の部分を見てみましょう。

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- `ServiceTypeName` には、任意の名前を指定できます。値は、サービスを特定するために `ApplicationManifest.xml` で使用されます。
- `UseImplicitHost="true"` を指定する必要があります。この属性は、サービスが自己完結型アプリに基づいているため、必要なのは、プロセスとして起動して、正常性を監視するだけであることを Service Fabric に伝えます。

### CodePackage
CodePackage は、サービスのコードの場所 (およびバージョン) を指定します。

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 要素では、サービスのコードが含まれるアプリケーション パッケージのディレクトリの名前を指定します。また、`CodePackage` にも `version` 属性があります。この属性を使用すると、コードのバージョンを指定できるほか、Service Fabric の ALM インフラストラクチャを使用してサービスのコードをアップグレードできる可能性もあります。
### SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntrypoint を使用して、サービスのコードが起動される前に実行する必要がある実行可能ファイルまたはバッチ ファイルを指定します。これはオプションの要素なので、必要な初期化/セットアップが存在しない場合は追加する必要はありません。SetupEntryPoint は、サービスを再起動するたびに実行されます。SetupEntrypoint は 1 つだけのため、アプリケーションのセットアップ/構成に複数のスクリプトが必要な場合は、セットアップ/構成スクリプトを 1 つのバッチ ファイルにバンドルする必要があります。Entrypoint要素と同様、SetupEntrypoint は、あらゆる種類のファイル (実行可能ファイル、バッチ ファイル、powershell コマンドレット) を実行できます。上記の例では、SetupEntrypoint は、Code ディレクトリの `scripts` サブディレクトリにあるバッチ ファイル launchConfig.cmd に基づいています (WorkingDirectory 要素が Code に設定されている場合)。

### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
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

### Endpoints

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
`Endpoint` 要素は、アプリケーションがリッスンできるエンドポイントを指定します。この例では、Node.js アプリケーションは、ポート 3000 でリッスンします。

## アプリケーション マニフェスト ファイル

`servicemanifest.xml` ファイルを構成したら、適切なサービスの種類と名前が使用されるように `ApplicationManifest.xml` ファイルを少し変更する必要があります。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

`ServiceManifestImport` では、アプリに追加する 1 つ以上のサービスを指定できます。サービスは、`ServiceManifest.xml` ファイルが配置されているディレクトリの名前を指定する `ServiceManifestName` で参照されます。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### ログ記録のセットアップ
既存のアプリケーションについては、アプリケーションと構成のスクリプトにエラーがないかどうかを確認するコンソールのログを表示できると非常に便利です。コンソールのリダイレクトは、`ServiceManifest.xml` ファイルで `ConsoleRedirection` 要素を使用して構成できます。

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection` を使用すると、コンソール出力 (stdout および stderr) を作業ディレクトリにリダイレクトして、Service Fabric クラスター内のアプリケーションのセットアップまたは実行中にエラーが発生していないことを確認できます。

	* `FileRetentionCount` によって、作業ディレクトリ内に保存されているファイルの数を判断できます。たとえば、値が 5 の場合は、これまでの 5 回の実行のログ ファイルが作業ディレクトリに格納されていることを意味します。
	* `FileMaxSizeInKb` では、ログ ファイルの最大サイズを指定します。

ログ ファイルは、サービスの作業ディレクトリのいずれかに保存されます。ファイルの場所を特定するには、Service Fabric エクスプローラーを使用して、サービスが実行されているノードと、現在使用されている作業ディレクトリを確認する必要があります。この方法については、この記事の後半で説明します。

### デプロイ
最後のステップは、アプリケーションのデプロイです。次の PowerShell スクリプトは、ローカル デプロイ用クラスターにアプリケーションをデプロイし、新しい Service Fabric サービスを開始する方法を示しています。

```Powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MulitpleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'Store\nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Service Fabric サービスは、さまざまな "構成" にデプロイできます。たとえば、単一または複数のインスタンスとしてデプロイしたり、Service Fabric クラスターの各ノードに 1 つのサービス インスタンスが配置されるようにデプロイしたりできます。

`New-ServiceFabricService` コマンドレットの `InstanceCount` パラメーターは、Service Fabric クラスターで開始するサービス インスタンスの数を指定するために使用します。`InstanceCount` 値は、デプロイするアプリケーションの種類に応じて設定できます。よく使用されるシナリオは 2 つあります。* `InstanCount = "1"`: 1 つのサービス インスタンスのみがクラスターにデプロイされます。サービスがデプロイされるノードは、Service Fabric スケジューラによって決まります。

* `InstanceCount ="-1"`: 1 つのサービス インスタンスが、Service Fabric クラスターのすべてのノードにデプロイされます。最終的には、1 つ (のみ) のサービス インスタンスがクラスターの各ノードに存在することになります。クライアント アプリケーションはクラスター内のいずれかのノードに "接続" するだけでエンドポイントを使用できるため、フロントエンド アプリケーション (REST エンドポイントなど) には便利な構成です。この構成は、たとえば、Service Fabric クラスターのすべてのノードがロード バランサーに接続され、クライアント トラフィックを、クラスター内のすべてのノードで実行されているサービスに分散できる場合にも使用できます。

### 実行中のアプリケーションの確認

Service Fabric エクスプローラーで、サービスが実行されているノードを特定します。この例では、Node1 で実行されています。

![running app](./media/service-fabric-deploy-existing-app/runningapplication.png)

ノードに移動してアプリケーションを参照した場合は、ディスク上の場所を含むノードの重要な情報が表示されます。

![location on disk](./media/service-fabric-deploy-existing-app/locationondisk.png)

サーバー エクスプローラーを使用してディレクトリを参照した場合は、次に示すように、作業ディレクトリとサービスのログ フォルダーを確認できます。

![location on disk](./media/service-fabric-deploy-existing-app/loglocation.png)


## 次のステップ
この記事では、既存のアプリケーションをパッケージ化し、Service Fabric にデプロイする方法について説明しました。次のステップとして、このトピックに関する他のコンテンツを確認できます。

- カスタム アプリケーションをパッケージ化し、デプロイするためのサンプルが [Github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Custom/SimpleApplication) で入手できます。これには、パッケージ化ツールのプレリリース版のリンクが含まれています。
- [複数のカスタム アプリケーションをデプロイする](service-fabric-deploy-multiple-apps.md)方法について学習します。
- [Visual Studio を使用して最初の Service Fabric アプリケーションを作成する方法](service-fabric-create-your-first-application-in-visual-studio.md)に関するページから作業を開始できます。

<!---HONumber=AcomDC_1125_2015-->