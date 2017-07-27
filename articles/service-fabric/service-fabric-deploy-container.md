---
title: "Service Fabric とコンテナーのデプロイ | Microsoft Docs"
description: "Service Fabric と、コンテナーを使用してマイクロサービス アプリケーションをデプロイする方法。 この記事では、Service Fabric が提供するコンテナー用の機能と、クラスターに Windows コンテナー イメージをデプロイする方法について説明します。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 799cc9ad-32fd-486e-a6b6-efff6b13622d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/16/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 25d6b056421e71fa70ed20a39589f77dbbc25c69
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017


---
# <a name="deploy-a-windows-container-to-service-fabric"></a>Service Fabric への Windows コンテナーのデプロイ
> [!div class="op_single_selector"]
> * [Windows コンテナーのデプロイ](service-fabric-deploy-container.md)
> * [Docker コンテナーのデプロイ](service-fabric-deploy-container-linux.md)
> 
> 

この記事では、コンテナー化されたサービスを Windows コンテナーに構築する手順を紹介します。

Service Fabric には、コンテナー内で実行されているマイクロサービスで構成されたアプリケーションの構築に役立つ、いくつかの機能が用意されています。 

機能は次のとおりです。

* コンテナー イメージのデプロイとアクティブ化
* リソース ガバナンス
* リポジトリの認証
* コンテナー ポートからホスト ポートへのマッピング
* コンテナー間での検出と通信
* 環境変数の構成と設定の機能

ここで、コンテナー化されたサービスをパッケージ化してアプリケーションに組み込む際の各機能の動作を見ていきます。

## <a name="package-a-windows-container"></a>Windows コンテナーのパッケージ化
コンテナーをパッケージ化する際は、Visual Studio プロジェクト テンプレートを使用するか、それとも [アプリケーション パッケージを手動で作成](#manually)するかを選択できます。  Visual Studio を使用するときには、アプリケーション パッケージの構造とマニフェスト ファイルは新しいプロジェクト テンプレートによって作成されます。

> [!TIP]
> 既存のコンテナー イメージをパッケージ化してサービスにする場合は、Visual Studio を使用すると最も簡単に行うことができます。

## <a name="use-visual-studio-to-package-an-existing-container-image"></a>Visual Studio を使用した既存コンテナ― イメージのパッケージ化
Visual Studio には、コンテナーを Service Fabric クラスターにデプロイするときに役立つ Service Fabric サービスのテンプレートが用意されています。

1. **[ファイル]**、 > **[新しいプロジェクト]** の順に選択し、Service Fabric アプリケーションを作成します。
2. サービス テンプレートとして**ゲスト コンテナー**を選択します。
3. **[イメージ名]** を選択し、コンテナー リポジトリ内のイメージへのパスを指定します。 たとえば、https://hub.docker.com/ の `myrepo/myimage:v1` を指定します。
4. サービスに名前を付けて、**[OK]** をクリックします。
5. コンテナー化されたサービスに通信用のエンドポイントが必要な場合は、ServiceManifest.xml ファイルに Protocol、Port、Type を追加できます。 For example: 
     
    `<Endpoint Name="MyContainerServiceEndpoint" Protocol="http" Port="80" UriScheme="http" PathSuffix="myapp/" Type="Input" />`
    
    `UriScheme` を指定すると、Service Fabric では、ネーム サービスを使用したコンテナー エンドポイントが自動的に登録され、検出可能性が確保されます。 ポートは (上記の例のように) 固定することも、動的に割り当てることもできます。 ポートを指定しない場合、(他のサービスと同様に) アプリケーション ポート範囲から動的に割り当てられます。
    また、アプリケーション マニフェストで `PortBinding` ポリシーを指定して、コンテナーからホスト ポートへのポート マッピングを構成する必要があります。 詳細については、「[コンテナー ポートからホスト ポートへのマッピングの構成](#Portsection)」を参照してください。
6. コンテナーでリソース ガバナンスが必要な場合は、`ResourceGovernancePolicy` を追加する必要があります。
8. コンテナーでプライベート リポジトリを使用した認証が必要な場合は、`RepositoryCredentials` を追加します。
7. コンテナー サポートが有効な Windows Server 2016 コンピューターで実行している場合は、パッケージを使用し、アクションを公開してローカル クラスターにデプロイすることができます。 
8. 準備ができたら、アプリケーションをリモート クラスターに発行するか、ソリューションをソース管理にチェックインすることができます。 

例については、[GitHub にある Service Fabric コンテナーのコード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-containers)を参照してください。

## <a name="creating-a-windows-server-2016-cluster"></a>Windows Server 2016 クラスターの作成
コンテナー化されたアプリケーションをデプロイするには、コンテナーのサポートが有効になっている Windows Server 2016 を実行するクラスターを作成する必要があります。 クラスターはローカルで実行することも、Azure で Azure Resource Manager を使用してデプロイすることもできます。 

Azure Resource Manager を使用してクラスターをデプロイするには、Azure で **Windows Server 2016 with Containers** イメージ オプションを選択します。 「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」をご覧ください。 必ず、以下の Azure Resource Manager 設定を使用してください。

```xml
"vmImageOffer": { "type": "string","defaultValue": "WindowsServer"     },
"vmImageSku": { "defaultValue": "2016-Datacenter-with-Containers","type": "string"     },
"vmImageVersion": { "defaultValue": "latest","type": "string"     },  
```
[こちらの 5 ノード Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)を使用してクラスターを作成することもできます。 または、Service Fabric と Windows コンテナーの使用に関する[こちらのコミュニティのブログ記事](https://loekd.blogspot.com/2017/01/running-windows-containers-on-azure.html)をご覧ください。

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container-image"></a>コンテナー イメージの手動によるパッケージ化およびデプロイ
コンテナー化されたサービスを手動でパッケージ化するプロセスは、次の手順に基づいています。

1. リポジトリにコンテナーを発行します。
2. パッケージ ディレクトリ構造を作成します。
3. サービス マニフェスト ファイルを編集します。
4. アプリケーション マニフェスト ファイルを編集します。

## <a name="deploy-and-activate-a-container-image"></a>コンテナー イメージのデプロイおよびアクティブ化
Service Fabric の [アプリケーション モデル](service-fabric-application-model.md)では、コンテナーは複数のサービス レプリカが配置されたアプリケーション ホストを表します。 コンテナーをデプロイしてアクティブ化するには、コンテナー イメージ名をサービス マニフェストの `ContainerHost` 要素に入力します。

サービス マニフェストで、エントリ ポイントの `ContainerHost` を追加します。 次に、コンテナー リポジトリとイメージの名前になるように `ImageName` を設定します。 次のマニフェストの一部では、`myrepo` というリポジトリから `myimage:v1` というコンテナーをデプロイする場合の例を示しています。

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

`Commands` 要素でコンテナーの開始時に実行するようにオプションのコマンドを指定することができます。 コマンドが複数ある場合は、それらをコンマで区切ります。 

## <a name="understand-resource-governance"></a>リソース ガバナンスについて
リソース ガバナンスは、コンテナー機能の 1 つです。コンテナーがホスト上で使用できるリソースを制限します。 アプリケーション マニフェストで指定された `ResourceGovernancePolicy` は、サービス コード パッケージのリソース制限を宣言するために使用します。 次のリソースのリソースの制限を設定できます。

* メモリ
* MemorySwap
* CpuShares (CPU の相対的な重み)
* MemoryReservationInMB  
* BlkioWeight (BlockIO の相対的な重み)

> [!NOTE]
> 今後のリリースで、IOPS、読み取り/書き込み BPS などの特定のブロック IO 制限の指定がサポートされる予定です。
> 
> 

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>リポジトリの認証
コンテナーをダウンロードするには、コンテナー リポジトリにサインイン資格情報を提供する必要がある場合があります。 アプリケーション マニフェストで指定されたサインイン資格情報は、イメージ リポジトリからコンテナー イメージをダウンロードする際に必要なサインイン情報または SSH キーを指定するのに使用されます。 次の例は、 *TestUser* というアカウントと、クリア テキスト (推奨*されません*) のパスワードを示しています。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

コンピューターにデプロイされた証明書を使用してパスワードを暗号化することをお勧めします。

次の例は、*MyCert* という証明書を使用してパスワードが暗号化された *TestUser* というアカウントを示しています。 `Invoke-ServiceFabricEncryptText` PowerShell コマンドを使用して、パスワードのシークレット暗号化テキストを作成できます。 詳細については、「[Service Fabric アプリケーションでのシークレットの管理](service-fabric-application-secret-management.md)」を参照してください。

パスワードの暗号化を解除するための証明書の秘密キーは、ローカル コンピューターにアウトオブバンド方式でデプロイされる必要があります (Azure では、Azure Resource Manager を使用します)。その後、Service Fabric によってコンピューターにサービス パッケージが展開されたときに、シークレットを暗号化解除できます。 アカウント名とシークレットを使用すると、コンテナーのリポジトリで認証できます。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name ="Portsection"></a> コンテナーからホスト ポートへのマッピングの構成
アプリケーション マニフェストで `PortBinding` を指定することで、コンテナーとの通信に使用されるホスト ポートを構成できます。 このポートのバインドでは、サービスがコンテナー内部でリッスンしているポートをホスト上のポートにマップします。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>コンテナー間での検出と通信の構成
`PortBinding` 要素を使用して、コンテナー ポートをサービス マニフェストのエンドポイントにマップすることができます。 次の例に示すように、エンドポイント `Endpoint1` では固定ポート 8905 を指定します。 ポートをまったく指定せずにいることもでき、その場合はクラスターのアプリケーションのポート範囲からランダムにポートが選択されます。


```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```
エンドポイントを指定する場合、Service Fabric は、サービス マニフェストの `Endpoint` タグを使用して、このエンドポイントをネーム サービスに自動的に発行することができます。 これにより、クラスター内で実行されているその他のサービスが解決のための REST クエリを使用してこのコンテナーを検出できます。

ネーム サービスに登録すると、[リバース プロキシ](service-fabric-reverseproxy.md)を使用して、コンテナー内でコンテナー間の通信を行うことができます。 通信は、リバース プロキシ HTTP リスニング ポートおよび通信先のサービスの名前を環境変数として設定することで実行します。 詳細については、次のセクションを参照してください。 

## <a name="configure-and-set-environment-variables"></a>環境変数の構成と設定
環境変数は、サービス マニフェストのコード パッケージごとに指定できます。 この機能は、コンテナー、プロセス、またはゲスト実行可能ファイルとしてデプロイされているかどうかに関係なく、すべてのサービスで使用できます。 環境変数の値は、アプリケーション マニフェスト内で上書きすることも、デプロイ中にアプリケーションのパラメーターとして指定することもできます。

次のサービス マニフェストの XML スニペットは、コード パッケージ用の環境変数を指定する方法の例です。

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>
```

これらの環境変数は、アプリケーション マニフェスト レベルで上書きできます。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

前の例では、`HttpGateway` 環境変数に対して明示的な値 (19000) を指定しています。一方、`BackendServiceName` パラメーターの値は `[BackendSvc]` アプリケーション パラメーターによって設定します。 これらの設定により、アプリケーションのデプロイ時に `BackendServiceName` の値を指定できるようになり、マニフェスト内の固定値もなくすことができます。

## <a name="configure-isolation-mode"></a>分離モードの構成

Windows では、コンテナーの 2 つの分離モード (プロセスおよび Hyper-V) がサポートされます。  プロセス分離モードでは、同じホスト コンピューターで実行されているすべてのコンテナーがホストとカーネルを共有します。 Hyper-V 分離モードでは、各 Hyper-V コンテナーとコンテナー ホスト間でカーネルが分離されます。 分離モードは、アプリケーション マニフェスト ファイルの `ContainerHostPolicies` タグに指定されます。  指定できる分離モードは、`process`、`hyperv`、および `default` です。 `default` 分離モードの場合、Windows Server ホストでは `process` に、Windows 10 ホストでは `hyperv` にそれぞれ既定で設定されます。  以下のスニペットは、アプリケーション マニフェスト ファイルで分離モードがどのように指定されるかを示しています。

```xml
   <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
```


## <a name="complete-examples-for-application-and-service-manifest"></a>アプリケーション マニフェストとサービス マニフェストのすべてのコード例

アプリケーション マニフェスト ファイルの例を次に示します。

```xml
    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>
```

次にサービス マニフェストの例 (上記のアプリケーション マニフェストで指定されたもの) を示します。

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Endpoints>
                <Endpoint Name="Endpoint1" UriScheme="http" Port="80" Protocol="http"/>
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>次のステップ
コンテナー化サービスをデプロイしたので、ライフ サイクルを管理する方法について、「[Service Fabric アプリケーションのライフ サイクル](service-fabric-application-lifecycle.md)」を参照してください。

* [Service Fabric とコンテナーの概要](service-fabric-containers-overview.md)
* 例については、[GitHub にある Service Fabric コンテナーのコード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-containers)を参照してください。

