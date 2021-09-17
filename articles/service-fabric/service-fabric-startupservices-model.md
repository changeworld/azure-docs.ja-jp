---
title: Service Fabric アプリケーションの StartupServices.xml でサービス構成を定義する
description: StartupServices.xml を使用して、サービス レベルの構成を ApplicationManifest.xml から分離する方法について説明します。
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 69ec795bab910f8f2b030ab5758698d3fdbae824
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681379"
---
# <a name="introducing-startupservicesxml-in-service-fabric-application"></a>Service Fabric アプリケーションの StartupServices.xml について
この機能により、Service Fabric アプリケーション設計で StartupServices.xml が導入されます。 このファイルは、ApplicationManifest.xml の DefaultServices セクションをホストします。 この実装により、DefaultServices とサービス定義関連のパラメータは、既存の ApplicationManifest.xml から StartupServices.xml という新しいファイルに移動します。 このファイルは、Visual Studio の各機能 (ビルド/リビルド/F5/Ctrl+F5/発行) で使用されます。

注 - StartupServices.xml は Visual Studio のデプロイのみを目的としています。この配置により、Visual Studio (StartupServices.xml) でデプロイされるパッケージが、ARM によってデプロイされるサービスと競合しないようになります。 StartupServices.xml は、アプリケーション パッケージの一部としてパッケージ化されていません。 DevOps パイプラインではサポートされておらず、お客様が、ARM または必要な構成のコマンドレットを使用して、個々のサービスをアプリケーションにデプロイする必要があります。

## <a name="existing-service-fabric-application-design"></a>既存の Service Fabric アプリケーション設計
各サービス ファブリック アプリケーションでは、ApplicationManifest.xml が、そのアプリケーションのすべてのサービス関連情報のソースとなります。 ApplicationManifest.xml は、すべての Parameters、ServiceManifestImport、および DefaultServices で構成されています。 構成パラメーターは、ApplicationParameters の下にある Cloud.xml/Local1Node.xml/Local5Node.xml ファイルに記載されています。

アプリケーションに新しいサービスが追加された場合、この新しいサービスの Parameters では、ServiceManifestImport と DefaultServices が ApplicationManifest.xml 内に追加されます。 構成パラメーターは、ApplicationParameters の下にある Cloud.xml/Local1Node.xml/Local5Node.xml ファイルに追加されます。

ユーザーが Visual Studio の ビルドまたはリビルド関数をクリックすると、ApplicationManifest.xml 内の ServiceManifestImport、Parameters、DefaultServices セクションの変更が行われます。 構成パラメーターは、ApplicationParameters の下にある Cloud.xml/Local1Node.xml/Local5Node.xml ファイルでも編集されます。

ユーザーが F5、Ctrl + F5、または発行をトリガーすると、アプリケーションとサービスは、ApplictionManifest.xml の情報に基づいてデプロイまたは発行されます。  構成パラメーターは、ApplicationParameters の下にある Cloud.xml/Local1Node.xml/Local5Node.xml のいずれかのファイルからも使用されます。

![Service Fabric アプリケーションの既存の設計][exisiting-design-diagram]

ApplicationManifest.xml のサンプル 

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
    <Parameter Name="Web1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Web1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Web1Type" TargetReplicaSetSize="[Web1_TargetReplicaSetSize]" MinReplicaSetSize="[Web1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Web1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="new-service-fabric-application-design-with-startupservicesxml"></a>StartupServices.xml を用いた新しい Service Fabric アプリケーションの設計
この設計では、サービス レベルの情報 (例えば、サービス定義やサービス パラメーター) とアプリケーションレベルの情報 (ServiceManifestImport や ApplicationParameters) が明確に区別されています。 StartupServices.xml にはすべてのサービスレベルの情報が含まれ、ApplicationManifest.xml にはすべてのアプリケーションレベルの情報が含まれます。 もう 1 つの変更点として、StartupServiceParameters の下に Cloud.xml/Local1Node.xml/Local5Node.xml が追加されました。これには、サービス パラメーターのみの構成が含まれています。 ApplicationParameters の下にある既存の Cloud.xml/Local1Node.xml/Local5Node.xml には、アプリケーションレベルのパラメータ構成のみが含まれています。

アプリケーションに新しいサービスが追加されると、アプリケーションレベルのパラメーターと ServiceManifestImport が ApplicationManifest.xml に追加されます。 アプリケーション パラメーターの構成は、ApplicationParameters の下にある Cloud.xml/Local1Node.xml/Local5Node.xml ファイルに追加されます。 サービス情報とサービス パラメーターは StartupServices.xml に追加され、サービス パラメーターの構成は StartupServiceParameters の下の Cloud.xml/Local1Node.xml/Local5Node.xml に追加されます。

プロジェクトのビルド/リビルド中、ServiceManifestImport、アプリケーション パラメーターの変更は ApplicationManifest.xml で行われます。 アプリケーション パラメーターの構成は、ApplicationParameters の下にある Cloud.xml/Local1Node.xml/Local5Node.xml ファイルでも編集されます。 サービス関連の情報は StartupServices.xml で編集され、サービス パラメーターは StartupServiceParameters の下の Cloud.xml/Local1Node.xml/Local5Node.xml で編集されます。

Visual Studio で F5、Ctrl + F5、または発行がトリガーされると、アプリケーションは、ApplictionManifest.xml からの情報と、ApplicationParameters の下にある Cloud.xml/Local1Node.xml/Local5Node.xml のいずれかのファイルにあるアプリケーション パラメーターからの情報に基づいてデプロイまたは発行されます。 各サービスは、StartupServices.xml のサービス情報と、StartupServiceParameters の下にある Cloud.xml/Local1Node.xml/Local5Node.xml のいずれかのファイルのサービス パラメータ構成に基づいて、個別に起動されます。

![StartupServices.xml を用いた Service Fabric アプリケーションの新しい設計][new-design-diagram]

これらのサービス パラメーターとアプリケーション パラメーターは、図に示すように、アプリケーションを発行する (右クリック > 発行) 前に編集できます。

![新しい設計の発行オプション][publish-application]

新しい設計の ApplicationManifest.xml のサンプル
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
</ApplicationManifest>
```

StartupServices.xml ファイルのサンプル
```xml
<?xml version="1.0" encoding="utf-8"?>
<StartupServicesManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                         xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <Services>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </Services>
</StartupServicesManifest>
```

この startupServices.xml の機能は、SDK バージョン 5.0.516.9590 以上のすべての新しいプロジェクトで有効になっています。 アクター サービスの場合、これは Microsoft.ServiceFabric.Actors NuGet バージョン 5.0.516 以上で有効になっています。 以前のバージョンの SDK で作成されたプロジェクトは、最新の SDK と完全に下位互換性があります。 古いプロジェクトを新しい設計へ移行することはサポートされていません。 新しいバージョンの SDK で StartupServices.xml を使用しない Service Fabric アプリケーションを作成する場合は、以下の図のように「プロジェクト テンプレートの選択に関するヘルプ」リンクをクリックしてください。

![新しい設計での新しいアプリケーションの作成オプション][create-new-project]


## <a name="next-steps"></a>次のステップ
- [Service Fabric のアプリケーション モデル](service-fabric-application-model.md)について確認する。
- [Service Fabric のアプリケーション マニフェストとサービス マニフェスト](service-fabric-application-and-service-manifests.md)について確認する。

<!--Image references-->
[exisiting-design-diagram]: ./media/service-fabric-startupservices/existing-design.png
[new-design-diagram]: ./media/service-fabric-startupservices/new-design.png
[publish-application]: ./media/service-fabric-startupservices/publish-application.png
[create-new-project]: ./media/service-fabric-startupservices/create-new-project.png

