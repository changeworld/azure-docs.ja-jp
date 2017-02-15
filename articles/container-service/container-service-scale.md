---
title: "Azure CLI を使用した ACS クラスターのスケール | Microsoft Docs"
description: "Azure CLI を使用して Azure Container Service クラスターをスケールする方法を説明します。"
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Docker、コンテナー、マイクロ サービス、Mesos、Azure"
ms.assetid: 4a567474-f9a2-4172-bf86-7522aa4d4d80
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e8df2e68b1b7018d76da89ba9ab332b6ea216fb


---
# <a name="scale-an-azure-container-service"></a>Azure Container Service をスケールする
Azure CLI ツールを使用して、Azure Container Service (ACS) に含まれるノードの数を増やすことができます。 Azure CLI を使用してスケールした場合、コンテナーに適用された変更を示す新しい構成ファイルが返されます。

## <a name="about-the-command"></a>コマンドについて
Azure コンテナーと対話するために Azure CLI は Azure Resource Manager モードである必要があります。 Resource Manager モードに切り替えるには `azure config mode arm` を呼び出します。 `acs` コマンドには、コンテナー サービスのすべてのスケール操作を実行する `scale` という子コマンドがあります。 スケール コマンドで使用されるさまざまなパラメーターに関するヘルプを表示するには `azure acs scale --help` を実行します。これにより、次のような出力が返されます。

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>コマンドを使用したスケール
コンテナー サービスをスケールするには、事前に**リソース グループ**と **Azure Container Service (ACS) 名**を把握し、またエージェントの新しい数を指定する必要があります。 小さい数を指定してスケールダウンし、大きい数を指定してスケールアップできます。

スケールする前に、コンテナー サービスの現在のエージェント数を把握しておくことをお勧めします。 `azure acs show <resource group> <ACS name>` コマンドを使用して、ACS 構成を返します。 <mark>カウント</mark>の結果を書き留めます。

#### <a name="see-current-count"></a>現在のカウントを確認する
```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>新しいカウントにスケールする
分かりきったことかもしれませんが、コンテナー サービスをスケールするには `azure acs scale` を呼び出し、**リソース グループ**、**ACS 名**、および**エージェント数**を指定します。 コンテナー サービスをスケールした場合、Azure CLI により、新しいエージェント数を含む、コンテナー サービスの新しい構成を表す JSON 文字列が返されます。

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>次のステップ
* [クラスターのデプロイ](container-service-deployment.md)




<!--HONumber=Nov16_HO3-->


