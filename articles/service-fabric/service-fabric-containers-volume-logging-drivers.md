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
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 868c3051f60c27f15bfd99f66e50b65595951a00
ms.contentlocale: ja-jp
ms.lasthandoff: 05/25/2017

---

# <a name="specifying-volume-plugins-and-logging-drivers-for-your-container"></a>コンテナーのボリューム プラグインとログ ドライバーを指定する

Service Fabric では、コンテナー サービスへの [Docker ボリューム プラグイン](https://docs.docker.com/engine/extend/plugins_volume/)および [Docker ログ ドライバー](https://docs.docker.com/engine/admin/logging/overview/)の指定をサポートします。 プラグインは、次のマニフェストで示すように、アプリケーション マニフェストに指定します。


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
        <Volume Source="myexternalvolume" Destination="c:\testmountlocation3" Driver="sf" IsReadOnly="true"></Volume>
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

上記の例では、`Volume`の `Source` タグはソース フォルダーを指します。 ソース フォルダーは、コンテナーまたは永続的なリモート ストアをホストする VM のフォルダーである場合があります。 `Destination` タグは、実行中のコンテナー内で `Source` がマップされる場所です。 ボリューム プラグインを使用する場合、プラグインの名前 (`Driver` タグ) を上記の例で示したように指定します。  Docker ログ ドライバーが指定されている場合は、クラスター内のログを処理するエージェント (またはコンテナー) をデプロイする必要があります。 

Service Fabric クラスターにコンテナーをデプロイするには、以下の記事をご覧ください。

[Windows Server 2016 上での Service Fabric への Windows コンテナーのデプロイ](service-fabric-deploy-container.md)

[Linux 上での Service Fabric への Docker コンテナーのデプロイ](service-fabric-deploy-container-linux.md)


