---
title: "Azure Service Fabric の Windows コンテナー アプリケーションを作成する | Microsoft Docs"
description: "このチュートリアルでは、Azure Service Fabric で初めての Windows コンテナー アプリケーションを作成します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/18
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 7a8d28ef842ba77355628c79c20fa7fd3c693380
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="quickstart-deploy-a-service-fabric-windows-container-application-on-azure"></a>クイック スタート: Service Fabric の Windows コンテナー アプリケーションを Azure にデプロイする
Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。 

既存のアプリケーションを Service Fabric クラスター上の Windows コンテナー内で実行する場合は、アプリケーションに変更を加える必要はありません。 このクイックスタートでは、あらかじめ用意されている Docker コンテナー イメージを Service Fabric アプリケーションにデプロイする方法を紹介します。 最後まで読み進めていけば、Windows Server 2016 Nano Server と IIS コンテナーを稼働状態にすることができます。 このクイックスタートでは、Windows コンテナーのデプロイについて説明しています。Linux コンテナーをデプロイする場合は、[こちらのクイックスタート](service-fabric-quickstart-containers-linux.md)を参照してください。

![IIS の既定の Web ページ][iis-default]

このクイックスタートでは、次の方法について説明します。
> [!div class="checklist"]
> * Docker イメージ コンテナーをパッケージ化する
> * 通信を構成する
> * Service Fabric アプリケーションのビルドとパッケージ化
> * コンテナー アプリケーションを Azure にデプロイする

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション ([無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成できます)。
* 次のものを実行している開発コンピューター。
  * Visual Studio 2015 または Visual Studio 2017。
  * [Service Fabric SDK およびツール](service-fabric-get-started.md)。

## <a name="package-a-docker-image-container-with-visual-studio"></a>Visual Studio で Docker イメージ コンテナーをパッケージ化する
Service Fabric SDK およびツールには、コンテナーを Service Fabric クラスターにデプロイするときに役立つサービスのテンプレートが用意されています。

"管理者" として Visual Studio を起動します。  **[ファイル]** > **[新規作成]** > **[プロジェクト]** の順に選択します。

**[Service Fabric アプリケーション]** を選択し、"MyFirstContainer" という名前を付けて、**[OK]** をクリックします。

**[ホスト コンテナーとアプリケーション]** テンプレートから **[コンテナー]** を選択します。

**[イメージ名]** に、「microsoft/iis:nanoserver」と入力します。これが [Windows Server Nano Server と IIS の基本イメージ](https://hub.docker.com/r/microsoft/iis/)になります。 

サービスに "MyContainerService" という名前を付けて **[OK]** をクリックします。

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>通信/コンテナー ポートからホスト ポートへのマッピングを構成する
サービスには、通信のエンドポイントが必要です。  このクイック スタートでは、コンテナー化されたサービスはポート 80 でリッスンします。  ソリューション エクスプローラーで *MyFirstContainer/ApplicationPackageRoot/MyContainerServicePkg/ServiceManifest.xml* を開きます。  ServiceManifest.xml ファイルで既存の `Endpoint` を更新し、プロトコル、ポート、URI スキームを追加します。 

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
   </Endpoints>
</Resources>
```
`UriScheme` を指定すると、Service Fabric ネーム サービスを使用したコンテナー エンドポイントが自動的に登録され、検出可能性が確保されます。 ServiceManifest.xml の完全なサンプル ファイルは、この記事の最後にあります。 

ポート 80 で受信するサービスへの要求が、コンテナーのポート 80 にマッピングされるように、コンテナーとホストとの間のポート マッピングを構成します。  ソリューション エクスプローラーで *MyFirstContainer/ApplicationPackageRoot/ApplicationManifest.xml* を開き、`ContainerHostPolicies` の `PortBinding` ポリシーを指定します。  このクイックスタートでは、`ContainerPort` は 80 で、`EndpointRef` は "MyContainerServiceTypeEndpoint" (サービス マニフェストで定義されたエンドポイント) です。    

```xml
<ServiceManifestImport>
...
  <ConfigOverrides />
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

ApplicationManifest.xml の完全なサンプル ファイルは、この記事の最後にあります。

## <a name="create-a-cluster"></a>クラスターの作成
Azure のクラスターにアプリケーションをデプロイする場合、パーティ クラスターに参加できます。 パーティ クラスターは、Azure でホストされる無料の期間限定の Service Fabric クラスターであり、Service Fabric チームによって実行されます。このクラスターには、だれでもアプリケーションをデプロイして、プラットフォームについて学習することができます。 このクラスターでは、ノード間のセキュリティおよびクライアントとノードの間のセキュリティに単一の自己署名証明書が使用されます。 

サインインし、[Windows クラスターに参加](http://aka.ms/tryservicefabric)します。 **[PFX]** リンクをクリックして、PFX 証明書をコンピューターにダウンロードします。 証明書と **[接続のエンドポイント]** の値は、次の手順で使用します。

![PFX と接続エンドポイント](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

Windows コンピューターで、*CurrentUser\My* 証明書ストアに PFX をインストールします。

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:
\CurrentUser\My


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

次の手順のために拇印を覚えておいてください。  

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Visual Studio を使用してアプリケーションを Azure にデプロイする
これでアプリケーションの準備ができたので、Visual Studio から直接クラスターにデプロイできます。

ソリューション エクスプローラーで **[MyFirstContainer]** を右クリックして、**[発行]** を選択します。 [発行] ダイアログが表示されます。

パーティ クラスター ページの**接続のエンドポイント**を **[接続のエンドポイント]** フィールドにコピーします。 たとえば、「`zwin7fh14scd.westus.cloudapp.azure.com:19000`」のように入力します。 **[詳細な接続パラメーター]** をクリックし、接続パラメーターの情報を確認します。  *FindValue* と *ServerCertThumbprint* の値は、前の手順でインストールした証明書の拇印に一致する必要があります。 

![[発行] ダイアログ](./media/service-fabric-quickstart-containers/publish-app.png)

**[発行]**をクリックします。

クラスター内の各アプリケーションには、一意の名前が必要です。  パーティ クラスターはパブリックの共有環境ですが、既存のアプリケーションと競合している可能性があります。  名前の競合が発生している場合は、Visual Studio プロジェクトの名前を変更し、もう一度デプロイします。

ブラウザーを開き、http://zwin7fh14scd.westus.cloudapp.azure.com:80 に移動します。 IIS の既定の Web ページが表示されます。![IIS の既定の Web ページ][iis-default]

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric のアプリケーション マニフェストとサービス マニフェストの完全な例
このクイックスタートで使用される完全なサービス マニフェストとアプリケーション マニフェストは次のとおりです。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>microsoft/iis:nanoserver</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>次の手順
このクイック スタートでは、次の方法について説明しました。
> [!div class="checklist"]
> * Docker イメージ コンテナーをパッケージ化する
> * 通信を構成する
> * Service Fabric アプリケーションのビルドとパッケージ化
> * コンテナー アプリケーションを Azure にデプロイする

* [Service Fabric でのコンテナー](service-fabric-containers-overview.md)の実行について確認します。
* [コンテナー内の .NET アプリケーションをデプロイする方法](service-fabric-host-app-in-a-container.md)に関するチュートリアルをご覧ください。
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。
* GitHub で [Service Fabric コンテナーのコード サンプル](https://github.com/Azure-Samples/service-fabric-containers)を確認します。

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
