<properties
   pageTitle="MongoDB を使用した Node.js アプリケーションのデプロイ |Microsoft Azure"
   description="複数のゲスト実行可能ファイルをパッケージ化して Azure Service Fabric クラスターにデプロイする方法に関するチュートリアル"
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
   ms.date="06/20/2016"
   ms.author="bscholl;mikhegn"/>


# 複数のゲスト実行可能ファイルのデプロイ

この記事では、Service Fabric パッケージ化ツールのプレビュー バージョンを使用して、複数のゲスト実行可能ファイルをパッケージ化し、Azure Service Fabric にデプロイする方法について説明します。このツールは [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool) で入手できます。

Service Fabric のパッケージを手動で作成する方法については、「[Service Fabric へのゲスト実行可能ファイルのデプロイ](service-fabric-deploy-existing-app.md)」方法を確認してください。

このチュートリアルは、データ ストアとして MongoDB を使用する Node.js フロントエンドを使用したアプリケーションをデプロイする方法を示していますが、この手順は、別のアプリケーションへの依存関係があるすべてのアプリケーションに適用できます。

## Node.js アプリケーションのパッケージ化

この記事は、Service Fabric クラスター内のノードに Node.js がインストールされていないことを前提としています。そのため、パッケージ化する前に、ノード アプリケーションのルート ディレクトリに Node.exe を追加する必要があります。(Express Web フレームワークと Jade テンプレート エンジンを使用する) Node.js アプリケーションのディレクトリ構造は、次のようになります。

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

- **/source** は、パッケージ化するアプリケーションのディレクトリを指しています。
- **/target** は、パッケージを作成するディレクトリを定義します。このディレクトリは、ソース ディレクトリとは別のディレクトリである必要があります。
- **/appname** は、既存のアプリケーションのアプリケーション名を定義します。この名前はマニフェスト内で、Service Fabric のアプリケーション名ではなく、サービス名に変換されることを理解しておくことが重要です。
- **/exe** は、Service Fabric が開始する実行可能ファイルを定義します (この例では `node.exe`)。
- **/ma** は、実行可能ファイルの起動に使用される引数を定義します。Node.js がインストールされていないため、Service Fabric は、`node.exe bin/www` を実行して Node.js Web サーバーを起動する必要があります。`/ma:'bin/www'` と定義することで、パッケージ化ツールは `bin/ma` を node.exe の引数として使用します。
- **/AppType** は、Service Fabric アプリケーションの種類の名前を定義します。

>[AZURE.NOTE] Visual Studio を使用して、アプリケーション プロジェクトの一部としてアプリケーション パッケージを作成することもできます。Visual Studio プロジェクトでソースをリンクした場合、Visual Studio ソリューションをビルドすると、アプリケーション パッケージは、ソースの変更に応じて常に最新の状態になります。[Visual Studio を使用して既存のアプリケーションをパッケージ化する](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-application)

/target パラメーターで指定したディレクトリを参照すると、次に示すように、完全に機能する Service Fabric パッケージがツールによって作成されていることを確認できます。

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
このサンプルでは、Node.js Web サーバーはポート 3000 をリッスンしているため、ServiceManifest.xml ファイルのエンドポイント情報を次に示すように更新する必要があります。

```xml
<Resources>
      <Endpoints>
     	<Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
これで Node.js アプリケーションをパッケージ化できたので、MongoDB のパッケージ化に進むことができます。既に説明したように、ここで実行する手順は Node.js と MongoDB に固有のものではありません。実際には、1 つの Service Fabric アプリケーションとしてまとめてパッケージ化する必要のあるすべてのアプリケーションに適用されます。

MongoDB をパッケージ化する際は、必ず Mongod.exe と Mongo.exe をパッケージ化してください。どちらのバイナリも、MongoDB のインストール ディレクトリの `bin` ディレクトリにあります。ディレクトリ構造は次のようになっています。

```
|-- MongoDB
	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric は次に示すようなコマンドを使用して MongoDB を開始する必要があるため、MongoDB をパッケージ化する際は `/ma` パラメーターを使用する必要があります。

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] ノードのローカル ディレクトリに MongoDB のデータ ディレクトリを配置すると、ノードに障害が発生した場合にデータが保持されません。データ損失を防ぐには、耐久性の高いストレージを使用するか、MongoDB レプリカ セットを実装する必要があります。

PowerShell またはコマンド シェルで、次のパラメーターを使用してパッケージ化ツールを実行します。

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

MongoDB を Service Fabric アプリケーション パッケージに追加するには、アプリケーション マニフェストと Node.js アプリケーションが配置されているのと同じディレクトリを /target パラメーターが指していることを確認する必要があります。また、同じ ApplicationType 名を使用しているかどうかも確認してください。

>[AZURE.NOTE] Visual Studio を使用して、アプリケーション プロジェクトの一部としてアプリケーション パッケージを作成することもできます。Visual Studio プロジェクトでソースをリンクした場合、Visual Studio ソリューションをビルドすると、アプリケーション パッケージは、ソースの変更に応じて常に最新の状態になります。[Visual Studio を使用して既存のアプリケーションをパッケージ化する](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-application)

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
ご覧のとおり、MongoDB バイナリが格納されたディレクトリに新しいフォルダーである MongoDB が追加されています。`ApplicationManifest.xml` ファイルを開くと、Node.js アプリケーションと MongoDB の両方がパッケージに含まれているのがわかります。次のコードは、アプリケーション マニフェストの内容を示しています。

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
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

>[AZURE.NOTE] Visual Studio を使用すると、デバッグ (F5) または発行ウィザードによって、ローカルでアプリケーションを発行できます。

アプリケーションがローカル クラスターに正常に発行されると、Node.js アプリケーションのサービス マニフェストに入力したポート (この例では http://localhost:3000) で Node.js アプリケーションにアクセスできます。

このチュートリアルでは、2 つの既存のアプリケーションを 1 つの Service Fabric アプリケーションとして簡単にパッケージ化する方法について説明しました。また、高可用性やヘルス システムとの統合など、Service Fabric の機能の一部を活用できるように、Service Fabric にデプロイする方法についても説明しました。

## 次のステップ

- [ゲスト アプリケーションを手動でパッケージ化する](service-fabric-deploy-existing-app.md)方法について学習します。

<!---HONumber=AcomDC_0622_2016-->