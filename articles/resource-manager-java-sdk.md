<properties
   pageTitle="Resource Manager SDK for .Java| Microsoft Azure"
   description="Resource Manager Java SDK の認証と使用例の概要"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/16/2016"
   ms.author="navale;tomfitz;"/>
   
# Azure Resource Manager SDK for Java
   
Azure Resource Manager (ARM) のプレビュー SDK は、複数の言語とプラットフォームで利用できます。これらの言語実装はそれぞれ、エコシステム パッケージ マネージャーと GitHub を介して入手できます。

これらの SDK のコードはそれぞれ、[Azure REST ベースの API 仕様](https://github.com/azure/azure-rest-api-specs)から生成されます。これらの仕様はオープン ソースであり、Swagger v2 仕様に基づいています。SDK コードは、[AutoRest](https://github.com/azure/autorest) と呼ばれるオープン ソース プロジェクトを使用して生成されたコードです。AutoRest では、これらの REST ベースの API 仕様が、複数の言語でクライアント ライブラリに変換されます。SDK で生成されたコードに改善する箇所がある場合、広範に適用されている API 仕様形式に基づく、SDK を作成するためのツール全体が自由に使用できます。

Azure Resource Manager Java SDK は GitHub の [Azure Java SDK リポジトリ](https://github.com/azure/azure-sdk-for-java)でホストされます。この記事の執筆時には、SDK はプレビューであることに注意してください。次のパッケージを使用できます。

* コンピューティングの管理: (azure-mgmt-compute)
* DNS の管理: (azure-mgmt-dns)
* ネットワーク管理: (azure-mgmt-network)
* リソースの管理: (azure-mgmt-resources)
* SQL 管理: (azure-mgmt-sql)
* ストレージの管理: (azure-mgmt-storage)
* Traffic Manager の管理: (azure-mgmt-traffic-manager)
* ユーティリティとヘルパー: (azure-mgmt-utility)
* Web サイト/WebApps の管理: (azure-mgmt-websites)

最新の一覧については、「Azure SDK for [Java Features」 Wiki ページ](https://github.com/Azure/azure-sdk-for-java/wiki/Azure-SDK-for-Java-Features)を参照してください。

## 前提条件
1. Java v1.6 以上
2. [Maven](https://maven.apache.org/) (SDK での開発を希望の場合)

## SDK の取得
[Maven](https://maven.apache.org/) 分散 jar ファイルは、Azure Java SDK を開始する方法としてお勧めします。これらの依存関係を、多数の Java の依存関係管理ツール (Maven、Gradle、Ivy など) に追加できます。Maven で使用できるライブラリの一覧については、この[リンク](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.azure%22)を参照してください。

または、git を使用しているソースから直接 SDK を取得します。SDK のソース コードを git 経由で取得するには、次の手順に従ってください。

    git clone https://github.com/Azure/azure-sdk-for-java.git
    cd ./azure-sdk-for-java/

(この概要のサンプルでは、SDK パッケージのソースとして Maven を使用しています)

SDK には、一部のシナリオ (認証、VM のプロビジョニングなど) のサンプルが含まれています。これらはすべて、 [azure-mgmt-samples](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples) GitHub のリポジトリにあります。

## ヘルパー クラス

SDK には、いくつかのメイン パッケージのヘルパー クラスが含まれます。ヘルパー クラスは、auzre-mgmt-utility パッケージで実装されます。

* AuthHelper - 認証ヘルパー クラス
* ComputeHelper - コンピューティング ヘルパー クラス
* NetworkHelper - ネットワーク ヘルパー クラス
* ResourceHelper - デプロイメントのヘルパー クラス
* StorageHelper - ストレージのヘルパー クラス

**Maven 依存関係情報**

    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-utility</artifactId>
      <version>0.9.1</version>
    </dependency>

これにより、0.9.1 バージョン以降のユーティリティ パッケージが使用されます。

## 認証

ARM の認証は、Azure Active Directory (AD) によって処理されます。任意の API に接続するには、最初に Azure AD で認証を行って認証トークンを受信する必要があります。この認証トークンは、すべての要求に対して渡すことができます。このトークンを取得するには、まず Azure AD アプリケーションと、ログインに使用するサービス プリンシパルを作成する必要があります。手順については、「[ポータルを利用し、Active Directory のアプリケーションとサービス プリンシパルを作成する](./resource-group-create-service-principal-portal.md)」を参照してください。

サービス プリンシパルを作成すると、次の項目が準備できたことになります。

* クライアント ID (GUID)
* クライアント シークレット (文字列)
* テナント ID (GUID) またはドメイン名 (文字列)

この値を取得すると、Active Directory アクセス トークン (1 時間有効) を取得できます。

Java SDK には、クライアント ID、シークレット、およびテナント ID とともに 1 回提供されるアクセス トークンを作成するヘルパー クラス AuthHelper が含まれます 。次の例では、 [ServicePrincipalExample](https://github.com/Azure/azure-sdk-for-java/blob/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/authentication/ServicePrincipalExample.java) クラスで、AuthHelper *getAccessTokenFromServicePrincipalCredentials* メソッドを使用してアクセス トークンを取得します。

```java
public static Configuration createConfiguration() throws Exception {
   String baseUri = System.getenv("arm.url");

   return ManagementConfiguration.configure(
         null,
         baseUri != null ? new URI(baseUri) : null,
         System.getenv(ManagementConfiguration.SUBSCRIPTION_ID),
         AuthHelper.getAccessTokenFromServicePrincipalCredentials(
                  System.getenv(ManagementConfiguration.URI), System.getenv("arm.aad.url"),
                  System.getenv("arm.tenant"), System.getenv("arm.clientid"),
                  System.getenv("arm.clientkey"))
                  .getAccessToken());
}
```

## 仮想マシンを作成する 
ユーティリティ パッケージには、仮想マシンを作成するためのヘルパー クラス [ComputeHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ComputeHelper.java) が含まれています。 仮想マシンを操作するためのサンプルは、[コンピューティング](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/compute)の下の azure-mgmt-samples パッケージにあります。

仮想マシンを作成するための単純なフローを次に示します。この例では、ヘルパー クラスによって、VM 作成の一環として、ストレージとネットワークが作成されます。

```java
public static void main(String[] args) throws Exception {
        Configuration config = createConfiguration();
        ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
        StorageManagementClient storageManagementClient = StorageManagementService.create(config);
        ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
        NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

        String resourceGroupName = "javasampleresourcegroup";
        String region = "EastAsia";

        ResourceContext context = new ResourceContext(
                region, resourceGroupName, System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);

        System.out.println("Start create vm...");

        try {
            VirtualMachine vm = ComputeHelper.createVM(
                    resourceManagementClient, computeManagementClient, networkResourceProviderClient, 
                    storageManagementClient,
                    context, vnName, vmAdminUser, vmAdminPassword)
              .getVirtualMachine();

            System.out.println(vm.getName() + " is created");
        } catch (Exception ex) {
            System.out.println(ex.toString());
        }
}
```

## テンプレートのデプロイ
[ResouceHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ResourceHelper.java) クラスは、Java SDK に ARM テンプレートをデプロイするプロセスを容易にするために作成されました。

```java
// create a new resource group
ResourceManagementClient resourceManagementClient = createResourceManagementClient();
ResourceContext resourceContext = new ResourceContext(
        resourceGroupLocation, resourceGroupName,
        System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);
ComputeHelper.createOrUpdateResourceGroup(resourceManagementClient, resourceContext);

// create the template deployment
DeploymentExtended deployment = ResourceHelper.createTemplateDeploymentFromURI(
        resourceManagementClient,
        resourceGroupName,
        DeploymentMode.Incremental,
        deploymentName,
        TEMPLATE_URI,
        "1.0.0.0",
        parameters);
```
## すべての Virtual Machines の一覧を表示する
ヘルパー クラスを使用する必要はありません (ただし、使用すると作業が容易になります) が、代わりに各リソース プロバイダーのサービス クラスを直接使用する必要があります。この例では、認証済みのサブスクリプションに基づいてリソースの一覧を表示します (各リソース グループのについて、仮想マシンを検索してから、それに関連付けられている IP アドレスを確認します)。

```java
// authenticate and get access token
Configuration config = createConfiguration();
ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

// list all resource groups     
ArrayList<ResourceGroupExtended> resourceGroups = resourceManagementClient.getResourceGroupsOperations().list(null).getResourceGroups();
for (ResourceGroupExtended resourcesGroup : resourceGroups) {
   String rgName = resourcesGroup.getName();
   System.out.println("Resource Group: " + rgName);
   
   // list all virtual machines
   ArrayList<VirtualMachine> vms = computeManagementClient.getVirtualMachinesOperations().list(rgName).getVirtualMachines();
   for (VirtualMachine vm : vms) {
      System.out.println("    VM: " + vm.getName());
      // list all nics
      ArrayList<NetworkInterfaceReference> nics = vm.getNetworkProfile().getNetworkInterfaces();
      for (NetworkInterfaceReference nicReference : nics) {
         String[] nicURI = nicReference.getReferenceUri().split("/");
         NetworkInterface nic = networkResourceProviderClient.getNetworkInterfacesOperations().get(rgName, nicURI[nicURI.length - 1]).getNetworkInterface();
         System.out.println("        NIC: " + nic.getName());
         System.out.println("        Is primary: " + nic.isPrimary());
         ArrayList<NetworkInterfaceIpConfiguration> ips = nic.getIpConfigurations();

         // find public ip address
         for (NetworkInterfaceIpConfiguration ipConfiguration : ips) {
               System.out.println("        Private IP address: " + ipConfiguration.getPrivateIpAddress());
               String[] pipID = ipConfiguration.getPublicIpAddress().getId().split("/");
               PublicIpAddress pip = networkResourceProviderClient.getPublicIpAddressesOperations().get(rgName, pipID[pipID.length - 1]).getPublicIpAddress();
               System.out.println("        Public IP address: " + pip.getIpAddress());
         }
      }
}  
```

その他のサンプルは、[templatedeployments](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/templatedeployments) のサンプル パッケージにあります。

## 参考資料とヘルプ
Azure SDK for Java のドキュメント: [Java ドキュメント](http://azure.github.io/azure-sdk-for-java/)

SDK でバグが発生した場合、 [問題](https://github.com/Azure/azure-sdk-for-java/issues)で問題を報告するか、「[StackOverflow for Azure Java SDK](http://stackoverflow.com/questions/tagged/azure-java-sdk)」(Azure Java SDK の StackOverflow) を確認してください。

<!---HONumber=AcomDC_0323_2016-->