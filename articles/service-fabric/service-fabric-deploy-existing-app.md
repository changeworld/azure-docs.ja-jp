<properties
   pageTitle="Azure Service Fabric への既存の実行可能ファイルのデプロイ | Microsoft Azure"
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
   ms.date="05/17/2016"
   ms.author="bscholl"/>

# Service Fabric へのゲスト実行可能ファイルのデプロイ

Node.js アプリケーション、Java アプリケーション、Azure Service Fabric のネイティブなアプリケーションなど、あらゆる種類のアプリケーションを実行できます。Service Fabric では、この種のアプリケーションをゲスト実行可能ファイルと呼びます。Service Fabric は、ゲスト実行可能ファイルをステートレス サービスと同様に扱います。その結果、ゲスト実行可能ファイルは、可用性とその他のメトリックに基づいて、クラスター内のノードに配置されます。この記事では、ゲスト実行可能ファイルをパッケージ化して Service Fabric クラスターにデプロイする方法について説明します。

## Service Fabric でゲスト実行可能ファイルを実行するメリット

Service Fabric クラスターでゲスト実行可能ファイルを実行するメリットはいくつかあります。

- 高可用性:Service Fabric で実行されるアプリケーションは、追加設定しなくても高い可用性を実現します。Service Fabric によって、アプリケーションのインスタンスが常に 1 つ稼動している状態が維持されます。
- 正常性の監視:標準装備の Service Fabric 正常性監視により、アプリケーションが稼動しているかどうかが検出され、障害が発生した場合は診断情報が提供されます。   
- アプリケーション ライフサイクル管理:Service Fabric を使用すると、ダウンタイムなしでアップグレードを実行できるだけでなく、アップグレード中に問題が発生した場合は前のバージョンにロールバックすることもできます。    
- 密度:各アプリケーションを専用のハードウェアで実行する必要がないクラスターで複数のアプリケーションを実行できます。

この記事では、ゲスト実行可能ファイルをパッケージ化し、Service Fabric にデプロイするための基本的な手順について説明します。


## アプリケーション マニフェスト ファイルとサービス マニフェスト ファイルの概要

ゲスト実行可能ファイルのデプロイの詳細に入る前に、Service Fabric のパッケージ化とデプロイメントのモデルについて理解しておく必要があります。Service Fabric パッケージ化デプロイ モデルは、主に 2 つのファイルに依存しています。アプリケーション マニフェストとサービス マニフェストです。ApplicationManifest.xml と ServiceManifest.xml ファイルのスキーマ定義は、Service Fabric SDK およびツールと共に *C:\\Program Files\\Microsoft SDKs\\Service Fabric\\schemas\\ServiceFabricServiceModel.xsd* にインストールされます。


* **アプリケーション マニフェスト**

  アプリケーション マニフェストは、アプリケーションの説明に使用されます。アプリケーション マニフェストには、含まれるサービスや、サービスのデプロイ方法 (インスタンス数など) の定義に使用されるその他のパラメーターが列挙されます。

  Service Fabric では、アプリケーションは "アップグレード可能なユニット" です。 アプリケーションは 1 つのユニットとしてアップグレードすることができます。エラー (およびロールバック) が発生した場合でも、このプラットフォームで 1 つのユニットとして管理されます。プラットフォームはアップグレード プロセスの正常完了を保証します。プロセスが失敗しても、アプリケーションは不明または不安定な状態になりません。


* **サービス マニフェスト**

  サービス マニフェストには、サービスのコンポーネントを記述します。マニフェストには、サービスの名前や種類 (Service Fabric がサービスの管理に使用する情報)、そのコード、構成、データ コンポーネントなどのデータが含まれます。また、サービスのデプロイ後の構成に使用できるその他のパラメーターも含まれます。

  ここでは、サービス マニフェストで使用できるすべてのパラメーターについて詳しく説明しません。Service Fabric でゲスト実行可能ファイルを実行できるようにするために必要なパラメーターの一部について説明します。


## アプリケーション パッケージ ファイルの構造
Service Fabric にアプリケーションをデプロイするには、そのアプリケーションが次の定義済みディレクトリ構造に従っている必要があります。この構造の例を次に示します。

```
|-- ApplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|--Settings.xml
    |--data    
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

root には、アプリケーションを定義する ApplicationManifest.xm ファイルが含まれます。アプリケーションに含まれる各サービスのサブディレクトリには、サービスに必要なすべてのアーティファクト、つまり ServiceManifest.xml と、通常は次の 3 つのディレクトリがあります。

- *Code*:このディレクトリにはサービス コードが含まれています。
- *Config*:settings.xml ファイル (および、必要に応じてその他のファイル) が含まれています。実行時にサービスがこのファイルにアクセスし、特定の構成設定を取得します。
- *Data*:追加のローカル データが格納されている追加ディレクトリ。サービスでこのデータが必要になる場合があります。注: データは、一時的なデータのみを格納します。フェールオーバー中など、サービスの再配置が必要な場合、Service Fabric ではこのデータ ディレクトリには変更がコピー/レプリケートされません。

注: `config` ディレクトリと `data` ディレクトリは、必要がなければ作成する必要はありません。

## 既存のアプリをパッケージ化するプロセス

ゲスト実行可能ファイルをパッケージ化するプロセスは、次の手順に基づいています。

1. パッケージ ディレクトリ構造を作成します。
2. アプリケーションのコードと構成ファイルを追加します。
3. サービス マニフェスト ファイルを編集します。
4. アプリケーション マニフェスト ファイルを編集します。

>[AZURE.NOTE] ApplicationPackage を自動的に作成できるパッケージ化ツールを用意しています。このツールは現在プレビューの段階です。[ここ](http://aka.ms/servicefabricpacktool)からダウンロードできます。

### パッケージ ディレクトリ構造を作成する
まず、次の説明に従ってディレクトリ構造を作成します。

### アプリケーションのコードと構成ファイルを追加する
ディレクトリ構造を作成したら、アプリケーションのコード ファイルと構成ファイルを、code ディレクトリおよび config ディレクトリに追加できます。code ディレクトリまたは config ディレクトリに、追加のディレクトリまたはサブディレクトリを作成することもできます。

Service Fabric では、アプリケーション ルート ディレクトリの内容が xcopy されるので、2 つの上位ディレクトリ、コード、設定を作成する以外に使用する定義済みの構造はありません(必要に応じて別の名前を選択できます。詳細については、次のセクションで説明します)。

>[AZURE.NOTE] アプリケーションに必要なすべてのファイルと依存関係を必ず含めるようにしてください。Service Fabric では、アプリケーション パッケージ のコンテンツが、アプリケーションのサービスがデプロイされるクラスター内のすべてのノードにコピーされます。パッケージには、アプリケーションを実行するのに必要なすべてのコードが含まれています。依存関係がインストール済みであることを想定するのはお勧めしません。

### サービス マニフェスト ファイルを編集する
次の手順では、サービス マニフェスト ファイルを編集して、次の情報を含めます。

- サービスの種類の名前。これは、Service Fabric がサービスを識別するために使用する ID です。
- アプリケーションの起動に使用するコマンド (ExeHost)。
- アプリケーションをセットアップ/構成するために実行する必要のあるスクリプト (SetupEntrypoint)。

`ServiceManifest.xml` ファイルの例を次に示します。

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

- `ServiceTypeName` には、任意の名前を選択できます。この値は、サービスを識別するために `ApplicationManifest.xml` ファイルで使用されます。
- `UseImplicitHost="true"` を指定する必要があります。サービスは自己完結型アプリに基づいているため、プロセスとして起動し、正常性を監視する処理のみが必要であることを、この属性で Service Fabric に伝えます。

### CodePackage
CodePackage 要素には、サービスのコードの場所 (およびバージョン) を指定します。

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 要素は、サービスのコードが含まれるアプリケーション パッケージ内のディレクトリ名を指定するために使用されます。`CodePackage` にも `version` 属性があります。この属性を使用すると、コードのバージョンを指定できます。また、Service Fabric のアプリケーション ライフサイクル管理インフラストラクチャを使用してサービスのコードをアップグレードするためにも使用できます。
### SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntrypoint 要素を使用して、サービスのコードが起動される前に実行する必要がある実行可能ファイルまたはバッチ ファイルを指定します。これはオプションの要素なので、必要な初期化/セットアップが存在しない場合は追加する必要はありません。SetupEntryPoint は、サービスを再起動するたびに実行されます。

SetupEntrypoint は 1 つだけのため、アプリケーションのセットアップ/構成に複数のスクリプトが必要な場合は、セットアップ/構成スクリプトを 1 つのバッチ ファイルにバンドルする必要があります。Entrypoint 要素と同様に、SetupEntrypoint は、あらゆる種類のファイル (実行可能ファイル、バッチ ファイル、PowerShell コマンドレット) を実行できます。上記の例では、SetupEntrypoint は、Code ディレクトリの `scripts` サブディレクトリにあるバッチ ファイル LaunchConfig.cmd に基づいています (WorkingDirectory 要素が Code に設定されている場合)。

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

- `Program` には、サービスを開始するために実行する必要がある実行可能ファイルの名前を指定します。
- `Arguments` には、実行可能ファイルに渡す引数を指定します。引数を含むパラメーターの一覧を指定することもできます。
- `WorkingFolder` には、開始するプロセスの作業ディレクトリを指定します。次の 2 つの値を指定できます。
	- `CodeBase` には、作業ディレクトリをアプリケーション パッケージ内の Code ディレクトリに設定することを指定します (次に示す構造の `Code` ディレクトリ)。
	- `CodePackage` には、作業ディレクトリをアプリケーション パッケージのルートに設定することを指定します (`MyServicePkg`)。
- `WorkingDirectory` は、アプリケーション スクリプトまたは初期化スクリプトのいずれかが相対パスを使用できるように、正しい作業ディレクトリを設定するのに便利です。

### エンドポイント

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
`Endpoint` 要素には、アプリケーションがリッスンできるエンドポイントを指定します。この例では、Node.js アプリケーションは、ポート 3000 でリッスンします。

## アプリケーション マニフェスト ファイルを編集する

`Servicemanifest.xml` ファイルを構成したら、適切なサービスの種類と名前が使用されるように `ApplicationManifest.xml` ファイルを少し変更する必要があります。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

`ServiceManifestImport` 要素では、アプリに追加する 1 つ以上のサービスを指定できます。サービスは、`ServiceManifest.xml` ファイルが配置されているディレクトリの名前を指定する `ServiceManifestName` で参照されます。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### ログを設定する
ゲスト実行可能ファイルについては、アプリケーションと構成のスクリプトにエラーがあるかどうかを確認するコンソールのログを表示できると非常に便利です。コンソールのリダイレクトは、`ServiceManifest.xml` ファイルで `ConsoleRedirection` 要素を使用して構成できます。

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

ログ ファイルは、サービスの作業ディレクトリのいずれかに保存されます。ファイルの場所を特定するには、Service Fabric Explorer を使用して、サービスが実行中で、作業ディレクトリが使用されているノードを確認します。この方法については、この記事の後半で説明します。

### デプロイ
最後のステップは、アプリケーションのデプロイです。次の PowerShell スクリプトは、ローカル デプロイ用クラスターにアプリケーションをデプロイし、新しい Service Fabric サービスを開始する方法を示しています。

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Service Fabric サービスは、さまざまな "構成" にデプロイできます。 たとえば、単一または複数のインスタンスとしてデプロイしたり、Service Fabric クラスターの各ノードに 1 つのサービス インスタンスが配置されるようにデプロイしたりできます。

`New-ServiceFabricService` コマンドレットの `InstanceCount` パラメーターは、Service Fabric クラスターで開始するサービス インスタンスの数を指定するために使用します。`InstanceCount` 値は、デプロイするアプリケーションの種類に応じて設定できます。よく使用されるシナリオは 2 つあります。

* `InstanceCount = "1"`1 つのサービス インスタンスのみがクラスターにデプロイされます。サービスがデプロイされるノードは、Service Fabric スケジューラによって決まります。

* `InstanceCount ="-1"`1 つのサービス インスタンスが、Service Fabric クラスターのすべてのノードにデプロイされます。最終的には、1 つ (のみ) のサービス インスタンスがクラスターの各ノードに存在することになります。

クライアント アプリケーションはクラスター内のいずれかのノードに "接続" するだけでエンドポイントを使用できるため、フロントエンド アプリケーション (REST エンドポイントなど) には便利な構成です。この構成は、たとえば、Service Fabric クラスターのすべてのノードがロード バランサーに接続され、クライアント トラフィックを、クラスター内のすべてのノードで実行されているサービスに分散できる場合にも使用できます。

### 実行中のアプリケーションの確認

Service Fabric エクスプローラーで、サービスが実行されているノードを特定します。この例では、Node1 で実行されています。

![サービスが実行されているノード](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

ノードに移動してアプリケーションを参照した場合は、ディスク上の場所を含むノードの重要な情報が表示されます。

![ディスク上の場所](./media/service-fabric-deploy-existing-app/locationondisk2.png)

サーバー エクスプローラーを使用してディレクトリを参照した場合は、次に示すように、作業ディレクトリとサービスのログ フォルダーを確認できます。

![ログの場所](./media/service-fabric-deploy-existing-app/loglocation.png)


## 次のステップ
この記事では、ゲスト実行可能ファイルをパッケージ化し、Service Fabric にデプロイする方法について説明しました。次のステップとして、このトピックに関する他のコンテンツを確認できます。

- [ゲスト実行可能ファイルをパッケージ化し、デプロイするためのサンプルが GitHub で入手できます。](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication)これには、パッケージ化ツールのプレリリース版のリンクが含まれています。
- [複数のゲスト実行可能ファイルのデプロイ](service-fabric-deploy-multiple-apps.md)
- [Visual Studio で最初の Service Fabric アプリケーションを作成する](service-fabric-create-your-first-application-in-visual-studio.md)

<!---HONumber=AcomDC_0518_2016-->