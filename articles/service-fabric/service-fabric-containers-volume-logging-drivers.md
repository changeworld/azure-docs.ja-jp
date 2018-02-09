---
title: "Azure Service Fabric Docker Compose (プレビュー) | Microsoft Docs"
description: "Azure Service Fabric は、Service Fabric を使うことにより既存のコンテナーの調整を容易にするため、Docker Compose 形式を受け入れます。 Docker Compose のサポートは現在プレビュー段階です。"
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
ms.openlocfilehash: 8918d6d53d7dd04e2a685707979526230ebfbc42
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>コンテナーで Docker ボリューム プラグインとログ ドライバーを使用する
Azure Service Fabric では、コンテナー サービスへの [Docker ボリューム プラグイン](https://docs.docker.com/engine/extend/plugins_volume/)および [Docker ログ ドライバー](https://docs.docker.com/engine/admin/logging/overview/)の指定がサポートされています。 コンテナーが別のホストに移動または再開された場合でも、[Azure Files](https://azure.microsoft.com/services/storage/files/) にデータを維持できます。

現在は、Linux コンテナー用のボリューム ドライバーだけがサポートされています。 Windows コンテナーを使っている場合は、ボリューム ドライバーなしでボリュームを Azure Files [SMB3 共有](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/)にマップできます。 このマッピングの場合は、クラスターの仮想マシン (VM) を最新の Windows Server 1709 バージョンに更新します。


## <a name="install-the-docker-volumelogging-driver"></a>Docker ボリューム/ログ ドライバーをインストールする

Docker ボリューム/ログ ドライバーがコンピューターにインストールされていない場合は、RDP/SSH プロトコルを使って手動でインストールできます。 これらのプロトコルによるインストールは、[仮想マシン スケール セット スタートアップ スクリプト](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/)または [SetupEntryPoint スクリプト](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service)を使って実行できます。

[Azure 用 Docker ボリューム ドライバー](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)をインストールするスクリプトの例を次に示します。

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

> [!NOTE]
> Windows Server 2016 Datacenter は、SMB マウントのコンテナーへのマッピングをサポートしていません ([Windows Server バージョン 1709 でのみサポートされています](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/container-storage))。 そのため、1709 より古いバージョンでは、ネットワーク ボリュームのマッピングと Azure Files ボリューム ドライバーを使用できません。 
>   


## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>マニフェストでプラグインまたはドライバーを指定する
次のようにアプリケーション マニフェストでプラグインを指定します。

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

**Volume** 要素の **Source** タグは、ソース フォルダーを示します。 ソース フォルダーには、コンテナーまたは永続的なリモート ストアをホストする VM 内のフォルダーを指定できます。 **Destination** タグは、実行中のコンテナー内で **Source** がマップされている場所です。 そのため、Destination としてコンテナー内の既存の場所を指定することはできません。

ボリューム プラグインを指定すると、Service Fabric は指定されたパラメーターを使って自動的にボリュームを作成します。 **Source** タグはボリュームの名前を示し、**Driver** タグはボリュームのドライバー プラグインを指定します。 次のように、**DriverOption** タグを使ってオプションを指定できます。

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Docker ログ ドライバーを指定する場合は、クラスター内のログを処理するエージェント (またはコンテナー) をデプロイする必要があります。 **DriverOption** タグを使って、ログ ドライバーのオプションを指定できます。

## <a name="next-steps"></a>次の手順
Service Fabric クラスターにコンテナーをデプロイする方法については、[Service Fabric へのコンテナーのデプロイ](service-fabric-deploy-container.md)に関するページをご覧ください。
