---
title: "Azure Service Fabric Docker Compose プレビュー | Microsoft Docs"
description: "Azure Service Fabric では、Service Fabric を使用して既存のコンテナーの調整を容易にするため、Docker Compose 形式を受け入れます。 このサポートは現在プレビューの段階です。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: cf7b0dd3a81c35be4907dbba85b72ce4f87e3a9f
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>コンテナーでのボリューム プラグインとログ ドライバーの使用

Service Fabric では、コンテナー サービスへの [Docker ボリューム プラグイン](https://docs.docker.com/engine/extend/plugins_volume/)および [Docker ログ ドライバー](https://docs.docker.com/engine/admin/logging/overview/)の指定をサポートします。 

## <a name="install-volumelogging-driver"></a>ボリューム/ログ ドライバーをインストールする

Docker ボリューム/ログ ドライバーがコンピューターにインストールされていない場合は、RDP/SSH または VMSS スタートアップ スクリプトによって手動でインストールします。 たとえば、Docker ボリューム ドライバーをインストールするには、コンピューターに SSH でアクセスして次のように実行します。

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>マニフェストにプラグインまたはドライバーを指定する
プラグインは、次のマニフェストで示すように、アプリケーション マニフェストに指定します。

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

上記の例では、`Volume`の `Source` タグはソース フォルダーを指します。 ソース フォルダーは、コンテナーまたは永続的なリモート ストアをホストする VM のフォルダーである場合があります。 `Destination` タグは、実行中のコンテナー内で `Source` がマップされている場所です。 

ボリュームのプラグインを指定すると、Service Fabric は指定されたパラメーターを使用して自動的にボリュームを作成します。 `Source` タグはボリュームの名前を示し、`Driver` タグはボリュームのドライバー プラグインを指定します。 次のスニペットに示すように、`DriverOption` タグを使用してオプションを指定することができます。

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azurefile" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Docker ログ ドライバーが指定されている場合は、クラスター内のログを処理するエージェント (またはコンテナー) をデプロイする必要があります。  `DriverOption` タグを使用してログのドライバー オプションを指定することもできます。

Service Fabric クラスターにコンテナーをデプロイするには、以下の記事をご覧ください。


[Windows で初めての Service Fabric コンテナー アプリケーションを作成する](service-fabric-deploy-container.md)

