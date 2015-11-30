<properties
    pageTitle="VS を使用したリモート クラスターへのアプリの発行 |Microsoft Azure"
    description="Visual Studio を使用してリモートの Service Fabric クラスターにアプリケーションを発行するために必要な手順について説明します。"
    services="service-fabric"
    documentationCenter="na"
    authors="cawa"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Visual Studio を使用してリモート クラスターにアプリケーションを発行する

Visual Studio Service Fabric Tools を使用すると、Service Fabric クラスターへのアプリケーションの発行を反復可能な方法で簡単に実行できるうえに、スクリプト化することもできます。これは、PowerShell のデプロイ スクリプトと共に発行プロファイルを使用することで実現されています。発行プロファイルとは、重要な発行情報が含まれた XML 形式のファイルです。また、ユーザーがアプリケーションをローカル クラスターまたはリモート クラスターに簡単に発行できるように、**[Service Fabric アプリケーションの発行]** ダイアログ ボックスも追加されています。[発行] ダイアログ ボックスで加えられた設定の変更は、発行プロファイルにキャプチャされます。これにより、手動で編集した発行設定を後から自動化プロセスで使用できます。

## Service Fabric クラスターにアプリケーションを発行するために必要なアーティファクト

### Deploy-FabricApplication.ps1

これは、Service Fabric アプリケーションを発行するためのパラメーターとして発行プロファイルのパスを使用する PowerShell スクリプトです。

### 発行プロファイル

**PublishProfiles** という Service Fabric アプリケーション プロジェクト内のフォルダーに、**Cloud.XML** ファイルと **Local.XML** ファイルが含まれています。これらは、Service Fabric クラスター接続パラメーター、アプリケーション パラメーター ファイルへのパス、アップグレード設定など、アプリケーションを発行するための重要な情報が格納された "発行プロファイル" です。

### アプリケーション パラメーター ファイル

**ApplicationParameters** という Service Fabric アプリケーション プロジェクト内のフォルダーに、ユーザーが指定したアプリケーション マニフェストのパラメーター値用の XML ファイルが含まれています。デプロイ設定に複数の値を使用できるように、アプリケーション マニフェスト ファイルをパラメーター化できます。

たとえば、各デプロイのさまざまな環境に合わせてパーティション数を変更できます。プロジェクトを作成すると、**TargetReplicaSetSize** や **PartitionCount** など、アプリケーション マニフェスト内の設定はパラメーターに変換されます。これらのパラメーターの既定値は、アプリケーション マニフェスト ファイル (ApplicationManifest.XML。Service Fabric アプリケーション プロジェクト内にあります) の **Parameters** セクションに指定されています。アプリケーション パラメーター ファイルに追加する値はすべて、アプリケーション マニフェスト ファイル内の既定値よりも優先されます。

>[AZURE.NOTE]アクター サービスの場合、マニフェスト ファイルにパラメーターを生成するには、まずプロジェクトをビルドする必要があります。

アプリケーション マニフェスト ファイルの例を次に示します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application2Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Actor1ActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="Actor1ActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="Actor1ActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Actor1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Actor1ActorService">
         <StatefulService ServiceTypeName="Actor1ActorServiceType" TargetReplicaSetSize="[Actor1ActorService_TargetReplicaSetSize]" MinReplicaSetSize="[Actor1ActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[Actor1ActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## [Service Fabric アプリケーションの発行] ダイアログ ボックスを使用して Service Fabric クラスターにアプリケーションを発行する

次の手順は、Visual Studio Service Fabric Tools によって提供される **[Service Fabric アプリケーションの発行]** ダイアログ ボックスを使用してアプリケーションを発行する方法を示しています。

1. Service Fabric アプリケーション プロジェクトのショートカット メニューで **[発行]** を選択すると、**[Service Fabric アプリケーションの発行]** ダイアログ ボックスが表示されます。

    ![][0]

    **[ターゲット プロファイル]** ボックスの一覧で選択したファイルには、**マニフェスト バージョン**を除くすべての設定が保存されます。既存のプロファイルを再利用するか、**[ターゲット プロファイル]** ボックスの一覧で **[プロファイルの管理]** を選択して新しいプロファイルを作成できます。発行プロファイルを選択すると、その内容がダイアログ ボックスの対応するフィールドに表示されます。**[プロファイルの保存]** リンクを選択すると、いつでも変更を保存できます。

1. **[接続エンドポイント]** セクションで、ローカルまたはリモートの Service Fabric クラスターの発行エンドポイントを指定できます。接続エンドポイントを追加または変更するには、**[選択]** をクリックします。**[Service Fabric クラスターの選択]** ダイアログ ボックスに、発行先として使用可能な Service Fabric クラスター接続エンドポイントが表示されます (まだ Azure サブスクリプションにログインしていない場合は、ログインするよう求められます)。 接続エンドポイントを選択します。

    ![][1]

    エンドポイントを選択すると、選択した Service Fabric クラスターへの接続が Visual Studio によって検証されます。クラスターがセキュリティで保護されていない場合、Visual Studio はすぐにエンドポイントに接続できます。しかし、クラスターがセキュリティで保護されている場合は、続行する前にローカル コンピューターに証明書をインストールする必要があります。詳細については、[セキュリティで保護された接続を構成する方法](service-fabric-visualstudio-configure-secure-connections.md)に関するページを参照してください。終了したら、**[OK]** をクリックします。選択したクラスターに、**[Service Fabric アプリケーションの発行]** ダイアログ ボックスが表示されます。

1. **[アプリケーション パラメーター ファイル]** ボックスの一覧を使用して、アプリケーション パラメーター ファイルに移動できます。アプリケーション パラメーター ファイルには、アプリケーション マニフェスト ファイル内のパラメーターにユーザーが指定した値が保持されています。パラメーターを追加または変更するには、**[編集]** をクリックします。**[パラメーター]** グリッドでパラメーターの値を入力または変更します。終了したら、**[保存]** を選択します。

    ![][2]

1. **[アプリケーションのアップグレード]** チェック ボックスを使用して、この発行操作がアップグレードであるかどうかを指定できます。アップグレードの発行操作は、通常の発行操作とは異なります。「[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)」を参照してください。アップグレードの設定を構成するには、**[アップグレード設定の構成]** リンクを選択します。アップグレード パラメーター エディターが表示されます。アップグレード パラメーターの詳細については、「[Service Fabric アプリケーションのアップグレードの構成](service-fabric-visualstudio-configure-upgrade.md)」を参照してください。

1. **[マニフェスト バージョン]** をクリックすると、**[バージョンの編集]** ダイアログが表示されます。更新を実行するには、アプリケーションとサービスのバージョンをアップグレードする必要があります。アプリケーションとサービス マニフェストのバージョンがアップグレード プロセスに及ぼす影響の詳細については、「[Service Fabric アプリケーションのアップグレード チュートリアル](service-fabric-application-upgrade-tutorial.md)」を参照してください。

    ![][3]

    アプリケーションとサービスのバージョンに 1.0.0 のようなセマンティック バージョンか 1.0.0.0 の形式の数値が使用されている場合は、**[アプリケーションとサービスのバージョンを自動的に更新する]** オプションをオンにします。このオプションをオンにすると、コード、構成、またはデータのパッケージ バージョンが更新されたときに、サービスとアプリケーションのバージョン番号が自動的に更新されます。バージョンを手動で編集する場合は、チェック ボックスをオフにして、この機能を無効にします。

    >[AZURE.NOTE]アクター プロジェクトにすべてのパッケージ エントリが表示されるには、最初に、サービス マニフェスト ファイルにエントリを生成するプロジェクトをビルドします。

1. 必要な設定をすべて指定し終えたら、**[発行]** をクリックし、選択した Service Fabric クラスターにアプリケーションを発行します。指定した設定が、発行プロセスに適用されます。

## 次のステップ

継続的な統合環境で発行プロセスを自動化する方法については、[Service Fabric による継続的インテグレーションの設定](service-fabric-set-up-continuous-integration.md)に関するページを参照してください。


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png

<!---HONumber=Nov15_HO4-->