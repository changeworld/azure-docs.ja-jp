<properties
   pageTitle="MongoDB を使用した Node.js アプリケーションのデプロイ |Microsoft Azure"
   description="複数のアプリケーションをパッケージ化して Azure Service Fabric クラスターにデプロイする方法に関するチュートリアル"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>


# 複数のカスタム アプリケーションをデプロイする

この記事では、Service Fabric パッケージ化ツールのプレビュー バージョンを使用して、複数のアプリケーションをパッケージ化し、Service Fabric にデプロイする方法について説明します。このツールは http://aka.ms/servicefabricpacktool で取得できます。

Service Fabric のパッケージを手動で作成する方法については、[Azure Service Fabric に既存のアプリケーションをデプロイする方法](service-fabric-deploy-existing-app.md)に関する記事を参照してください。

このチュートリアルは Node.js フロントエンドがデータ ストアとして MongoDB を使用するアプリケーションをデプロイする方法を示していますが、この手順は、別のアプリケーションへの依存関係があるすべてのアプリケーションに適用できます。

## Node.js アプリケーションのパッケージ化

この記事は、Service Fabric クラスター内のノードに Node.js がインストールされていないことを前提としています。そのため、パッケージ化する前に、ノード アプリケーションのルート ディレクトリに node.exe を追加する必要があります。(Express Web フレームワークと Jade テンプレート エンジンを使用する) Node.js アプリケーションのディレクトリ構造は、次のようになります。

```
|-- NodeApplication
	|-- bin
        |-- www
	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
	|-- public
        |-- images
        |-- etc.
	|-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

次の手順として、Node.js アプリケーションのアプリケーション パッケージを作成します。次のコードは、Node.js アプリケーションが含まれた Service Fabric アプリケーション パッケージを作成します。

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

使用されているパラメーターの説明を以下に示します。

- **/source**: パッケージ化するアプリケーションのディレクトリを指しています。
- **/target**: パッケージを作成するディレクトリを定義します。このディレクトリは、対象ディレクトリとは別のディレクトリである必要があります。
- **/appname**: 既存のアプリケーションのアプリケーション名を定義します。この名前はマニフェスト内で、Service Fabric のアプリケーション名ではなく、サービス名に変換されることを理解しておく必要があります。
- **/exe**: Service Fabric が開始する実行可能ファイルを定義します (この例では `node.exe`)。
- **/ma**: 実行可能ファイルの起動に使用される引数を定義します。Node.js がインストールされていないため、Service Fabric は、`node.exe bin/www` を実行して Node.js Web サーバーを起動する必要があります。`/ma:'bin/www'` と定義することで、パッケージ化ツールは `bin/ma` を node.exe の引数として使用します。
- **/AppType**: Service Fabric アプリケーションの種類の名前を定義します。Visual Studio が

/target パラメーターで指定されたディレクトリを参照すると、次に示すように、完全に機能する Service Fabric パッケージがツールによって作成されていることを確認できます。

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
		      |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
		      |--Settings.xml
	    |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
生成された ServiceManifest.xml には、次のコード スニペットに示すような、Node.js Web サーバーを起動する方法が記述されたセクションがあります。

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
このサンプルでは、Node.js Web サーバーはポート 3000 をリッスンしているため、ServiceManifest.xml のエンドポイント情報を次に示すように更新する必要があります。

```xml
<Resources>
      <Endpoints>
     	<Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
これで Node.js アプリケーションをパッケージ化できたので、MongoDB のパッケージ化に進むことができます。既に述べたように、ここで実施した手順は Node.js と MongoDB に固有のものではありません。事実、1 つの Service Fabric アプリケーションとしてパッケージ化することが想定されたすべてのアプリケーションに適用できます。

MongoDB をパッケージ化する際は、必ず mongod.exe と mongo.exe をパッケージ化してください。どちらのバイナリも、MongoDB のインストール ディレクトリ内の `bin` ディレクトリにあります。ディレクトリ構造は次のようになっています。

```
|-- MongoDB
	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- etc.
```
Service Fabric は次に示すようなコマンドを使用して MongoDB を開始する必要があるため、MongoDB をパッケージ化する際は `/ma` パラメーターを使用する必要があります。

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE]ノードのローカル ディレクトリに MongoDB のデータ ディレクトリを配置すると、ノードに障害が発生した場合にデータが保持されません。データ損失を防ぐには、耐久性の高いストレージを使用するか、MongoDB ReplicaSet を実装する必要があります。

PowerShell またはコマンド シェルで、次のパラメーターを使用してパッケージ化ツールを実行します。

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

MongoDB を Service Fabric アプリケーション パッケージに追加するには、アプリケーション マニフェストと Node.js アプリケーションが配置されているのと同じディレクトリを /target パラメーターが指していることと、同じ ApplicationType 名前を使用していることを確認する必要があります。

ディレクトリを参照して、ツールによって作成された内容を確認してみましょう。

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
		    |--Settings.xml
	    |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
ご覧のとおり、MongoDB バイナリが格納されたディレクトリに新しいフォルダー MongoDB が追加されています。`ApplicationManifest.xml` ファイルを開くと、Node.js アプリケーションと MongoDB の両方がパッケージに含まれているのがわかります。次のコードは、アプリケーション マニフェストの内容を示しています。

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

最後の手順として、次の PowerShell スクリプトを使用して、ローカル Service Fabric クラスターにアプリケーションを発行します。

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStore' -ApplicationPackagePathInImageStore 'Store\NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

アプリケーションがローカル クラスターに正常に発行されると、Node.js アプリケーションのサービス マニフェストに入力したポート (この例では http://localhost:3000) で Node.js アプリケーションにアクセスできます。

このチュートリアルでは、2 つの既存のアプリケーションを 1 つの Service Fabric アプリケーションとして簡単にパッケージ化し、Service Fabric にデプロイする方法を説明しました。これによって、高可用性や正常性システムとの統合など、Service Fabric 機能の一部を活用できることを紹介しました。

## 次のステップ

[1 つのアプリケーションを手動でパッケージ化する](service-fabric-deploy-existing-app.md)方法について学習します。

<!---HONumber=AcomDC_1125_2015-->