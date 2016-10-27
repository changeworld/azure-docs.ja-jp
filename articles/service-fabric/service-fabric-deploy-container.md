<properties
   pageTitle="Service Fabric とコンテナーのデプロイ | Microsoft Azure"
   description="Service Fabric と、コンテナーを使用してマイクロサービス アプリケーションをデプロイする方法。 この記事では、Service Fabric が提供するコンテナー用の機能と、クラスターにコンテナー イメージをデプロイする方法について説明しています。"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="msfussell"/>


# <a name="preview:-deploy-a-container-to-service-fabric"></a>プレビュー: Service Fabric へのコンテナーのデプロイ

>[AZURE.NOTE] この機能は Linux 向けのプレビューの段階にあり、現時点で Windows Server では使用できません。 Windows Server 向けのプレビューは、Windows Server 2016 GA の後に行われる Service Fabric の次回のリリースで提供され、それ以降のリリースでサポートされます。

Service Fabric には、コンテナー化されたマイクロサービスで構成されたアプリケーションの構築に役立つ、いくつかのコンテナー機能が用意されています。 これをコンテナー化されたサービスといいます。 機能は次のとおりです。

- コンテナー イメージのデプロイとアクティブ化
- リソース ガバナンス
- リポジトリの認証
- コンテナー ポートからホスト ポートへのマッピング
- コンテナー間での検出と通信
- 環境変数の構成と設定の機能

ここで、コンテナー化されたサービスをパッケージ化してアプリケーションに組み込むための各機能を順番に見ていきます。

## <a name="packaging-a-container"></a>コンテナーのパッケージ化

コンテナーをパッケージ化する際は、Visual Studio プロジェクト テンプレートを使用するか、それとも [アプリケーション パッケージを手動で作成](#manually)するかを選択できます。 Visual Studio を使用すると、アプリケーション パッケージの構造とマニフェスト ファイルは新しいプロジェクト ウィザードによって作成されます。

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Visual Studio を使用した既存の実行可能ファイルのパッケージ化

>[AZURE.NOTE] Visual Studio ツール SDK の今後のリリースでは、ゲスト実行可能ファイルを追加する現在の方法と同様のやり方で、アプリケーションにコンテナーを追加することができるようになります。 詳細については、「 [Service Fabric へのゲスト実行可能ファイルのデプロイ](service-fabric-deploy-existing-app.md) 」を参照してください。 現時点では、以下の説明に従って、手動でパッケージ化を行う必要があります。

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-container"></a>手動でのパッケージ化とコンテナーのデプロイ
コンテナー化されたサービスを手動でパッケージ化するプロセスは、次の手順に基づいています。

1. リポジトリにコンテナーを発行します。
2. パッケージ ディレクトリ構造を作成します。
3. サービス マニフェスト ファイルを編集します。
4. アプリケーション マニフェスト ファイルを編集します。

## <a name="container-image-deployment-and-activation."></a>コンテナー イメージのデプロイとアクティブ化
Service Fabric の [アプリケーション モデル](service-fabric-application-model.md)では、コンテナーは複数のサービス レプリカが配置されたアプリケーション ホストを表します。 コンテナーをデプロイしてアクティブ化するには、コンテナー イメージ名をサービス マニフェストの `ContainerHost` 要素に入力します。

サービス マニフェストで、エントリ ポイントとして `ContainerHost` を追加し、コンテナー リポジトリおよびイメージの名前として `ImageName` を設定します。 次のマニフェストの一部では、*myrepo* というリポジトリから *myimage:v1* というコンテナーをデプロイする場合の例を示しています

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

コンテナー内で実行されるコンマ区切りの一連のコマンドによりオプションの `Commands` 要素を指定することで、コンテナー イメージに入力コマンドを指定することができます。 

## <a name="resource-governance"></a>リソース ガバナンス
リソース ガバナンスは、コンテナー機能の 1 つです。コンテナーがホスト上で使用できるリソースを制限します。 アプリケーション マニフェストで指定された `ResourceGovernancePolicy` は、サービス コード パッケージのリソース制限を宣言する機能を提供します。 次の事項に関して、リソース制限を設定できます。

- メモリ
- MemorySwap
- CpuShares (CPU の相対的な重み)
- MemoryReservationInMB  
- BlkioWeight (BlockIO の相対的な重み) 

>[AZURE.NOTE] 今後のリリースで、IOPS、読み取り/書き込み BPS などの特定のブロック IO 制限の指定がサポートされる予定です。


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>リポジトリの認証
コンテナーをダウンロードするには、コンテナー リポジトリにログイン資格情報を提供する必要がある場合があります。 " *アプリケーション* " マニフェストで指定されたログイン資格情報は、イメージ リポジトリからコンテナー イメージをダウンロードする際に必要なログイン情報または SSH キーを指定するのに使用されます。  次の例は、 *TestUser* というアカウントと、クリア テキストのパスワードを示しています。 これは推奨 **されません** 。


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

パスワードは、マシンにデプロイされた証明書を使用して暗号化できます (暗号化する必要があります)。

次の例は、*TestUser* というアカウントと、*MyCert* という証明書を使用して暗号化されたパスワードを示しています。 `Invoke-ServiceFabricEncryptText` Powershell コマンドを使用して、パスワードのシークレット暗号化テキストを作成できます。 作成方法の詳細については、「 [Service Fabric アプリケーションでのシークレットの管理](service-fabric-application-secret-management.md) 」を参照してください。 パスワードの暗号化を解除するための証明書の秘密キーは、ローカル コンピューターにアウトオブバンド方式でデプロイされる必要があります (Azure では Resource Manager を使用します)。 これで、Service Fabric がサービス パッケージをマシンにデプロイする際に、シークレットの暗号化の解除と、アカウント名およびこれらの資格情報を使用したコンテナー リポジトリによる認証を行えるようになります。


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
            <SecurityAccessPolicies>
                <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
            </SecurityAccessPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>コンテナー ポートからホスト ポートへのマッピング
アプリケーション マニフェストで `PortBinding` を指定することで、コンテナーとの通信に使用されるホスト ポートを構成できます。 このポートのバインドでは、サービスがコンテナー内部でリッスンしているポートをホスト上のポートにマップします。


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>コンテナー間での検出と通信
次の例に示すように、`PortBinding` ポリシーを使用して、コンテナー ポートをサービス マニフェストの `Endpoint` にマップすることができます。 エンドポイント `Endpoint1` では、ポート 80 などの固定ポートを指定できます。ポートをまったく指定せずにいることもでき、その場合はクラスターのアプリケーションのポート範囲からランダムにポートが選択されます。

ゲスト コンテナーの場合、サービス マニフェストでこのように `Endpoint` を指定することで、このエンドポイントを Service Fabric で自動的にネーム サービスに発行できるようになります。これにより、クラスターで実行されているその他のサービスが、解決サービスの REST クエリを使用してこのコンテナーを検出できるようになります。 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

ネーム サービスに登録すると、 [リバース プロキシ](service-fabric-reverseproxy.md)を使用して、コンテナー内のコードでコンテナー間の通信を簡単に行うことができます。 リバース プロキシ HTTP リスニング ポートおよび通信先のサービスの名前を環境変数として設定するだけで、これが可能になります。 実施方法については、次のセクションを参照してください。  

## <a name="configure-and-set-environment-variables"></a>環境変数の構成と設定
環境変数は、サービス マニフェストのコード パッケージごとに指定できます。これは、コンテナーにデプロイされたサービスのサービス マニフェストでも、プロセス実行可能ファイルまたはゲスト実行可能ファイルとしてデプロイされたサービスのサービス マニフェストでも実施可能です。 これらの環境変数の値は、アプリケーション マニフェスト内で個別に上書きできるほか、デプロイの最中にアプリケーションのパラメーターとして指定することもできます。

次のサービス マニフェストの XML スニペットは、コード パッケージ用の環境変数を指定する方法の例です。 

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

これらの環境変数は、アプリケーション マニフェスト レベルで上書きできます。

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

上記の例では、`HttpGateway` 環境変数に対して明示的な値 (19000) を指定しています。一方、`BackendServiceName` パラメーターの値は `[BackendSvc]` アプリケーション パラメーターによって設定されています。 これにより、アプリケーションのデプロイ時に `BackendServiceName` の値を指定できるようになり、マニフェスト内の固定値もなくすことができます。 

## <a name="complete-examples-for-application-and-service-manifest"></a>アプリケーション マニフェストとサービス マニフェストのすべてのコード例
次のコードは、コンテナー機能を示すアプリケーション マニフェストの例です。


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


次のコードは、コンテナー機能を示すサービス マニフェスト (上記のアプリケーション マニフェストで指定されたもの) の例です。

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
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
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>



<!--HONumber=Oct16_HO2-->


