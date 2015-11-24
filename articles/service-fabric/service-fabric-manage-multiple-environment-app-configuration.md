<properties
   pageTitle="Service Fabric での複数の環境の管理 |Microsoft Azure"
   description="Service Fabric アプリケーションは、1 台 ～ 数千台のコンピューターで構成されたさまざまな規模のクラスターで実行できます。場合によっては、このようなさまざまな環境に合わせて異なる方法でアプリケーションを構成したい場合があります。この記事では、環境ごとに異なるアプリケーション パラメーターを定義する方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="seanmck"/>

# 複数の環境のアプリケーション パラメーターを管理する

Service Fabric クラスターは、1 台～ 数千台のコンピューターで形成することができます。アプリケーション バイナリは、変更を行わなくても、このようなさまざまな環境で実行することができます。しかし、アプリケーションをデプロイするコンピューターの台数に応じて異なる方法でアプリケーションを構成したいということもしばしばあります。

簡単な例として、ステートレス サービスの `InstanceCount` について検討します。Azure での実行時には、通常、このパラメーターを特殊な値である -1 に設定し、クラスター内のどのノードでもサービスが確実に実行されるようにします。ただし、この構成はワンボックス クラスターには適していません。理由は、1 台のコンピューター上の同じエンドポイントで複数のプロセスをリッスン状態にしておくことができないからです。そこで、通常は、`InstanceCount` を 1 に設定します。

## 環境固有のパラメーターを指定する

この課題に対するソリューションとして、パラメーター化された既定のサービス セットと、特定の環境のパラメーター値を含むアプリケーション パラメーター ファイルがあります。

### 既定のサービス

Service Fabric アプリケーションは、サービス インスタンスのコレクションで構成されています。空のアプリケーションを作成してからすべてのサービス インスタンスを動的に作成することができますが、ほとんどのアプリケーションには、アプリケーションのインスタンス化の際に必ず作成されるコア サービス セットがあります。これらは、"既定のサービス" と呼ばれ、アプリケーション マニフェスト内に指定されます。なお、環境ごとの構成のプレース ホルダーは角かっこで囲まれます。

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

それぞれの名前付きパラメーターは、アプリケーション マニフェストの Parameters 要素内に定義する必要があります。

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

DefaultValue 属性では、特定の環境に対して具体的なパラメーターがない場合に使用される値が指定されます。

>[AZURE.NOTE]すべてのサービス インスタンス パラメーターが環境ごとの構成に適しているわけではありません。上記の例で、サービスのパーティション分割構成の LowKey 値と HighKey 値は、サービスのすべてのインスタンスに対して明示的に定義されています。これは、パーティションの範囲が、環境ではなくデータ ドメインに応じて決まるからです。　　

### 環境ごとのサービス構成の設定

[Service Fabric アプリケーション モデル](service-fabric-application-model.md)では、実行時に読み取り可能なカスタム キー値ペアを含む構成パッケージをサービスに含めることができます。このような設定の値は環境ごとに区別することもできます。そのためには、アプリケーション マニフェストに `ConfigOverride` を指定します。

`Stateful1` サービスのサービス マニフェスト内に次の設定があると仮定します。


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

特定のアプリケーション/環境ペアのこの値をオーバーライドするには、アプリケーション マニフェストにサービス マニフェストをインポートするときに `ConfigOverride` を作成します。

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

このパラメーターは上記のように環境ごとに構成することができます。そのためには、アプリケーション マニフェストのパラメーター セクションでこのパラメーターを宣言し、アプリケーション パラメーター ファイルに環境固有の値を指定します。

>[AZURE.NOTE]サービス構成設定の場合は、キーの値を設定できる場所が 3 箇所あります。サービス構成パッケージ、アプリケーション マニフェスト、およびアプリケーション パラメーター ファイルの 3 つです。Service Fabric は常に、まずアプリケーション パラメーター ファイル (指定されている場合) から選択し、次にアプリケーション マニフェストから選択し、最後に構成パッケージから選択します。

### アプリケーション パラメーター ファイル

Service Fabric アプリケーション プロジェクトには、1 つまたは複数のアプリケーション パラメーター ファイルを含めることができます。各ファイルでは、アプリケーション マニフェストで定義されているパラメーターに対して特定の値を定義します。

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

新しいアプリケーションには、既定では、Local.xml および Cloud.xml という名前の 2 つのパラメーター ファイルが含まれています。

![ソリューション エクスプ ローラーでのアプリケーション パラメーター ファイル][app-parameters-solution-explorer]

新しいパラメーター ファイルを作成するには、既存のパラメーター ファイルをコピーし、それに新しい名前を付けるだけです。

## デプロイ中に環境固有のパラメーターを識別する

デプロイ時には、アプリケーションで適用する適切なパラメーター ファイルを選択する必要があります。この操作は、Visual Studio の [発行] ダイアログ ボックスで、または PowerShell で行うことができます。

### Visual Studio からのデプロイ

Visual Studio でアプリケーションを発行する場合は、使用可能なパラメーター ファイルの一覧から選択することができます。

![[発行] ダイアログ ボックスでパラメーター ファイルを選択する][publishdialog]

### PowerShell からのデプロイ

`DeployCreate-FabricApplication.ps1` PowerShell スクリプトでは、パラメーター ファイルをパラメーターとして受け取ります。

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## 次のステップ

このトピックで説明したいくつかのコア概念の詳細については、「[Service Fabric の技術概要](service-fabric-technical-overview.md)」を参照してください。Visual Studio で使用可能な他のアプリケーション管理機能の詳細については、「[Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)」を参照してください。

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

<!---HONumber=Nov15_HO4-->