---
title: "Azure Service Fabric コンテナー アプリを作成する | Microsoft Docs"
description: "Azure Service Fabric で初めてのコンテナー アプリを作成します。  アプリで Docker イメージを構築して、そのイメージをコンテナー レジストリにプッシュし、Service Fabric コンテナー アプリをビルドおよびデプロイします。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: acb68b274228aa647dc7be5d36b2b077bd213c1b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>初めての Service Fabric コンテナー アプリを作成する
既存のアプリケーションを Service Fabric クラスター上の Windows コンテナー内で実行する場合は、アプリに変更を加える必要はありません。 このクイック スタートでは、Web アプリを含む Docker イメージの作成、Azure Container Registry への新しいイメージのプッシュ、Service Fabric コンテナー アプリの作成、Service Fabric クラスターへのコンテナー アプリのデプロイについて説明します。  この記事では、Docker の基本的な理解ができていることを前提としています。 Docker の詳細は、「[Docker Overview (Docker の概要)](https://docs.docker.com/engine/understanding-docker/)」で確認できます。

## <a name="prerequisites"></a>前提条件
次のものを実行している開発コンピューター。
* Visual Studio 2015 または Visual Studio 2017。
* [Service Fabric SDK およびツール](service-fabric-get-started.md)。
*  Docker for Windows。  [Docker CE for Windows (安定版) を入手します](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)。 Docker をインストールして起動したら、トレイ アイコンを右クリックし、**[Switch to Windows containers]\(Windows コンテナーに切り替える\)** を選択します。 これは、Windows に基づいて Docker イメージを実行するために必要です。

Windows Server 2016 上で実行されている 3 つ以上のノードがあり、コンテナーを含む Windows クラスター。[クラスターを作成する](service-fabric-get-started-azure-cluster.md)か、[無料で Service Fabric を試してください](http://tryazureservicefabric.westus.cloudapp.azure.com/)。 

Azure Container Registry のレジストリ。Azure サブスクリプションに[コンテナー レジストリを作成します](../container-registry/container-registry-get-started-portal.md)。 

## <a name="create-a-simple-web-app"></a>単純な Web アプリを作成する
Docker イメージに読み込む必要があるすべての資産を 1 か所に収集します。 このクイック スタートでは、開発コンピューターに "Hello World" Web アプリを作成します。

1. *c:\temp\helloworldapp* のようなディレクトリを作成します。
2. *c:\temp\helloworldapp\content* のようなサブディレクトリを作成します。
3. *c:\temp\helloworldapp\content* に *index.html* というファイルを作成します。
4. *index.html* を編集し、次の行を追加します。
    ```
    <h1>Hello World!</h1>
    ```
5. *index.html* の変更内容を保存します。

## <a name="build-the-docker-image"></a>Docker イメージを構築する
Docker Hub にある [microsft/iis イメージ](https://hub.docker.com/r/microsoft/iis/)に基づいたイメージを構築します。 microsoft/iis イメージは Windows Server Core の基本 OS イメージから派生しており、インターネット インフォメーション サービス (IIS) を含みます。  このイメージをコンテナーで実行すると、IIS およびインストールされた Web サイトが自動的に起動します。

Dockerfile で Docker イメージを定義します。 Dockerfile には、イメージを構築し、実行するアプリを読み込むための手順が含まれています。 Dockerfile はイメージを作成する ```docker build``` コマンドへの入力です。 

1. *Dockerfile* というファイル (ファイル拡張子なし) を *c:\temp\helloworldapp* に作成し、次を追加します。

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    この Dockerfile には ```ENTRYPOINT``` コマンドが存在しません。 これは必要ありません。 IIS が組み込まれた Windows Server を実行するとき、IIS プロセスは、基本イメージ内で起動するように構成されたエントリ ポイントとなります。

    詳細については、[Dockerfile のリファレンス](https://docs.docker.com/engine/reference/builder/)を参照してください。

2. ```docker build``` コマンドを実行して、Web アプリを実行するイメージを作成します。 PowerShell ウィンドウを開き、*c:\temp\helloworldapp* に移動します。 次のコマンドを実行します。

    ```
    docker build -t helloworldapp .
    ```
    このコマンドでは、Dockerfile の手順を使用して新しいイメージを構築し、"helloworldapp" と名前を付けます (-t というタグを付けます)。 イメージを構築すると、基本イメージが Docker Hub から取得され、基本イメージ上にアプリを追加する新しいイメージが作成されます。  [microsft/iis イメージ](https://hub.docker.com/r/microsoft/iis/)と OS 基本イメージは 10.5 GB あるため、開発コンピューターへのダウンロードと展開には時間がかかります。  昼食や休憩を取ってもよいでしょう。  既に開発コンピューターに基本 OS イメージが取得されている場合は、ダウンロード時間は短くなります。

3. ビルド コマンドが完了したら、`docker images` コマンドを実行して、新しいイメージの情報を確認します。

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>ローカルでイメージの実行を確認する
コンテナー レジストリにプッシュする前に、ローカルでイメージを確認します。  

1. ```docker run``` でコンテナーを起動します。

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    *name* で、実行中のコンテナーに名前を付けます (コンテナー ID ではありません)。

2. コンテナーが起動したら、実行中のコンテナーにブラウザーから接続できるように、その IP アドレスを確認します。
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. 実行中のコンテナーに接続します。  Web ブラウザーで、ポート 8000 で返された IP アドレスを開きます (たとえば、" http://172.31.194.61:8000 ")。 "Hello World!" という見出しが ブラウザーに表示されます。

4. コンテナーを停止するには、次を実行します。

    ```
    docker stop my-web-site
    ```

5. コンテナーを開発コンピューターから削除します。

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>コンテナー レジストリにイメージをプッシュする
コンテナーが開発コンピューターで実行されることを確認したら、イメージを Azure Container Registry のレジストリにプッシュします。

1. [レジストリの資格情報](../container-registry/container-registry-authentication.md)を使用してコンテナー レジストリにログインするには、``docker login`` を実行します。

    次の例では、Azure Active Directory [サービス プリンシパル](../active-directory/active-directory-application-objects.md)の ID とパスワードを渡します。 たとえば、自動化シナリオのために、レジストリにサービス プリンシパルを割り当てることができます。

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. 次のコマンドでは、レジストリへの完全修飾パスを使用して、イメージのタグまたはエイリアスを作成します。 この例では、レジストリのルートが煩雑にならないように、イメージを ```samples``` 名前空間に配置しています。

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  コンテナー レジストリにイメージをプッシュします。

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>コンテナー化されたサービスを Visual Studio で作成してパッケージ化する
Service Fabric SDK およびツールには、コンテナーを Service Fabric クラスターにデプロイするときに役立つサービスのテンプレートが用意されています。

1. Visual Studio を起動します。  **[ファイル]** > **[新規作成]** > **[プロジェクト]** の順に選択します。
2. **[Service Fabric アプリケーション]** を選択し、"MyFirstContainer" という名前を付けて、**[OK]** をクリックします。
3. **[サービス テンプレート]** の一覧から **[ゲスト コンテナー]** を選択します。
4. **[Image Name]\(イメージ名\)** に、コンテナー リポジトリにプッシュされたイメージである "myregistry.azurecr.io/samples/helloworldapp" を入力します。 
5. サービスに名前を付けて、**[OK]** をクリックします。
6. コンテナー化されたサービスに通信用のエンドポイントが必要な場合は、ServiceManifest.xml ファイルの ```Endpoint``` にプロトコル、ポート、種類を追加できます。 このクイック スタートでは、コンテナー化されたサービスはポート 80 でリッスンします。 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    ```UriScheme``` を指定すると、Service Fabric ネーム サービスを使用したコンテナー エンドポイントが自動的に登録され、検出可能性が確保されます。 ServiceManifest.xml の完全なサンプル ファイルは、この記事の最後にあります。 
7. ApplicationManifest.xml ファイルの ```ContainerHostPolicies``` 内にある ```PortBinding``` ポリシーを指定して、コンテナーのポートからホストへのポート マッピングを構成します。  このクイック スタートでは、```ContainerPort``` は 8000 で (コンテナーは Dockerfile で指定されたとおりにポート 8000 を公開します)、```EndpointRef``` は "Guest1TypeEndpoint" です (サービス マニフェストで定義されているエンドポイントです)。  ポート 80 のサービスへの受信要求は、コンテナーのポート 8000 にマッピングされています。  コンテナーでプライベート リポジトリを使用した認証が必要な場合は、```RepositoryCredentials``` を追加します。  このクイック スタートでは、myregistry.azurecr.io コンテナー レジストリのアカウント名とパスワードを追加します。 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    ApplicationManifest.xml の完全なサンプル ファイルは、この記事の最後にあります。
8. クラスターにアプリを発行できるように、クラスターの接続エンドポイントを構成します。  [Azure Portal](https://portal.azure.com) にある、お使いのクラスターの [概要] ブレードで、クライアントの接続エンドポイントを探すことができます。 ソリューション エクスプローラーで、**MyFirstContainer**->**PublishProfiles** にある *Cloud.xml* を開きます。  **ClusterConnectionParameters** にクラスター名と接続ポートを追加します。  For example:
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. すべてのファイルを保存し、プロジェクトをビルドします。  

10. アプリをパッケージ化するために、ソリューション エクスプローラーの **[MyFirstContainer]** を右クリックし、**[パッケージ]** を選択します。 

## <a name="deploy-the-container-app"></a>コンテナー アプリをデプロイする
1. アプリを発行するために、ソリューション エクスプローラーの **[MyFirstContainer]** を右クリックし、**[発行]** を選択します。

2. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) は、Service Fabric クラスター内のアプリケーションとノードを検査および管理するための Web ベースのツールです。 ブラウザーを開き、http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ に移動して、アプリのデプロイを進めます。  アプリはデプロイされますが、クラスター ノードにイメージがダウンロードされるまではエラー状態となります (イメージのサイズによって時間がかかる場合があります) 。 ![エラー][1]

3. ```Ready``` 状態になったら、アプリの準備は完了しています。 ![準備完了][2]

4. ブラウザーを開き、http://containercluster.westus2.cloudapp.azure.com に移動します。 "Hello World!" という見出しが ブラウザーに表示されます。

## <a name="clean-up"></a>クリーンアップ
クラスターの実行中は、料金が継続的に発生します。[クラスターの削除](service-fabric-get-started-azure-cluster.md#remove-the-cluster)を検討してください。  [パーティ クラスター](http://tryazureservicefabric.westus.cloudapp.azure.com/)は数時間後に自動的に削除されます。

コンテナー レジストリにイメージをプッシュした後は、開発コンピューターからローカルのイメージを削除できます。

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric のアプリケーション マニフェストとサービス マニフェストの完全な例
このクイック スタートで使用される完全なサービス マニフェストとアプリケーション マニフェストは次のとおりです。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
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
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
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
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>次のステップ
* [Service Fabric でのコンテナー](service-fabric-containers-overview.md)の実行について確認します。
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。
* GitHub で [Service Fabric コンテナーのコード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-containers)を確認します。

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

