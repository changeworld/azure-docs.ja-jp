---
title: "Azure Service Fabric のネットワーク パターン | Microsoft Docs"
description: "Service Fabric の一般的なネットワーク パターンと、Azure のネットワーク機能を使用してクラスターを作成する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 030114fa1ea9b76c0ed48baeffb8859260fc8e52
ms.openlocfilehash: 78012ae1552c01690b0ad5b1285173ef9faf12bc
ms.lasthandoff: 03/01/2017


---
# <a name="service-fabric-networking-patterns"></a>Service Fabric のネットワーク パターン
Service Fabric クラスターの作成に関してよく寄せられる質問の&1; つは、Azure のさまざまなネットワーク機能とクラスターを統合する方法についてです。  この記事では、次の機能を使用してクラスターを作成する方法を示します。

- [既存の Virtual Network/サブネット](#existingvnet)
- [静的パブリック IP アドレス](#staticpublicip)
- [内部ロード バランサー](#internallb)
- [内部ロード バランサーと外部ロード バランサー](#internalexternallb)

重要な概念として、Service Fabric は標準的な仮想マシン スケール セットで実行されるため、仮想マシン スケール セットで使用できる機能はすべて Service Fabric クラスターでも使用できるという点に留意してください。 Resource Manager テンプレートのネットワーク部分は同じです。  既存の VNet にデプロイしたら、ExpressRoute、VPN ゲートウェイ、ネットワーク セキュリティ グループ (NSG)、VNet ピアリングといったネットワーク機能を簡単に組み込むことができます。

ただし、Service Fabric には他とは異なる点が&1; つあります。[Azure Portal](https://portal.azure.com) が内部で Service Fabric リソース プロバイダー (SFRP) を使用し、クラスターからノードとアプリケーションに関する情報を取得するという点です。  SFRP は、管理エンドポイントの受信 HTTP ゲートウェイ ポート (既定では&19080;) にパブリックにアクセスする必要があります。[Service Fabric エクスプローラー](service-fabric-visualizing-your-cluster.md)は、このポートを使用してクラスターを管理します。 また、SFRP は、このポートを使用してクラスターに関する情報を照会し、Azure Portal に表示します。  SFRP がこのポートにアクセスできないと、管理ポータルに "ノードが見つかりません" などのメッセージが表示され、ノードとアプリケーションの一覧が空になります。  Azure Portal でクラスターの状態を確認するには、ロード バランサーでパブリック IP アドレスが公開されており、NSG で着信 19080 トラフィックが許可されている必要があります。  これらの要件を満たしていない場合、クラスターの現在の状態は Azure Portal に表示されません。  それ以外の場合はクラスターへの影響はなく、[Service Fabric エクスプローラー](service-fabric-visualizing-your-cluster.md)を使って現在の状態を取得できます。この制限が許容されるかどうかは、ネットワークの要件に基づきます。  この制限は一時的なものです。今後の更新でこの制限がなくなった時点で、管理ポータルの機能を失うことなく、クラスターがパブリックにアクセスされないようにすることができます。

## <a name="templates"></a>テンプレート

[こちら](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip)から、すべてのテンプレートを入手できます。 次の PowerShell コマンドを使用して、テンプレートをそのままデプロイできます。  既存の VNet テンプレートまたは静的パブリック IP テンプレートをデプロイする場合は、最初に「[初期セットアップ](#initialsetup)」セクションの説明をお読みください。

<a id="initialsetup"></a>
## <a name="initial-setup"></a>初期セットアップ

### <a name="existing-virtual-network"></a>既存の仮想ネットワーク

リソース グループ *ExistingRG* にある既存の Virtual Network 'ExistingRG-vnet' と、'default' という名前のサブネットを使用します。  これらは、Azure Portal で標準的な仮想マシンを作成すると作成される既定のリソースです。  仮想マシンを作成せずに VNet とサブネットを作成することもできます。 クラスターを既存の VNet に追加する主要な目的は、他の VM へのネットワーク接続を提供することですが、VM を作成することで、その基本的な使い方の具体例を確認することができます。  Service Fabric クラスターでパブリック IP アドレスを指定せずに内部ロード バランサーのみを使用している場合は、パブリック IP を指定した VM をジャンプ ボックスとして使用することもできます。

### <a name="static-public-ip-address"></a>静的パブリック IP アドレス

静的パブリック IP アドレスは、通常は割り当て先の VM から切り離して管理されている専用のリソースで、(Service Fabric クラスター リソース グループ内ではなく) 専用のネットワーク リソース グループにプロビジョニングされます。  Azure Portal か PowerShell を使用して、同じリソース グループ *ExistingRG* に 'staticIP1' という名前の静的パブリック IP アドレスを作成します。

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric テンプレート

ここでは Service Fabric template.json を使用します。これは、標準的なポータル ウィザードでクラスターを作成する前に、ポータルからダウンロードできます。 [テンプレート ギャラリー](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric)内のテンプレートを使用することもできます ([5 つのノードの Service Fabric クラスター](https://azure.microsoft.com/en-us/documentation/templates/service-fabric-unsecure-cluster-5-node-1-nodetype/)など)。

<a id="existingvnet"></a>
## <a name="existing-virtual-networksubnet"></a>既存の仮想ネットワーク/サブネット

(2016 年 1 月 24 日: 以下の場所に Service Fabric のスコープ外の別のサンプルがあります: [https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet))

1. サブネットのパラメーターを既存のサブネットの名前に変更し、既存の VNet を参照する&2; つの新しいパラメーターを追加します。

    ```
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```


2. *vnetID* 変数が既存の VNet を指すように変更します。

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Azure で新しい VNet が作成されないよう、リソースから *Microsoft.Network/virtualNetworks* を削除します。

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

4. 新しい VNet の作成に依存しないよう、*Microsoft.Compute/virtualMachineScaleSets* の *dependsOn* 属性から VNet をコメント アウトします。

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. テンプレートをデプロイします。

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    デプロイが完了すると、Virtual Network に新しいスケール セット VM が追加され、仮想マシン スケール セットのノード タイプに既存の VNet とサブネットが表示されます。  VNet に既に存在する既存の VM に RDP でアクセスし、新しいスケール セット VM を ping することもできます。

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>静的パブリック IP アドレス

1. 既存の静的 IP のリソース グループ名、名前、および FQDN のパラメーターを追加します。

    ```
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. 静的 IP には既にパラメーターがあるので、*dnsName* パラメーターを削除します。

    ```
    /*
    "dnsName": {
        "type": "string"
    }, 
    */
    ```

3. 既存の静的 IP を参照する変数を追加します。

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. 新しい IP アドレスが作成されないよう、*Resources* から *Microsoft.Network/publicIPAddresses* を削除します。

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. 新しい IP アドレスの作成に依存しないよう、*Microsoft.Network/loadBalancers* の *dependsOn* 属性から IP アドレスをコメント アウトします。

    ```
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. *Microsoft.Network/loadBalancers* リソースの *frontendIPConfigurations* の *publicIPAddress* 要素が、新しく作成した IP ではなく既存の静的 IP を参照するように変更します。

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. *Microsoft.ServiceFabric/clusters* リソースの *managementEndpoint* を、静的 IP の DNS FQDN に変更します。  **セキュリティで保護されたクラスターを使用している場合は、必ず 'http://' を 'https://' に変更してください。** (注: この手順は Service Fabric クラスター向けです。  仮想マシン スケール セットを使用している場合は省略してください。)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. テンプレートをデプロイします。

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

デプロイが完了すると、ロード バランサーが他のリソース グループからパブリック静的 IP アドレスにバインドされていることが確認できます。 Service Fabric クライアント接続エンドポイントと [Service Fabric エクスプローラー](service-fabric-visualizing-your-cluster.md) エンドポイントは、静的 IP アドレスの DNS FQDN をポイントします。

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>内部ロード バランサー

このシナリオでは、既定の Service Fabric テンプレートの外部ロード バランサーを内部ロード バランサーに置き換えます。  Azure Portal と SFRP への影響については、前のセクションの説明をご覧ください。

1. *dnsName* パラメーターは不要なので削除します。

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. IP アドレスを静的に割り当てている場合は、必要に応じて静的 IP アドレス パラメーターを追加します。 動的に割り当てている場合は必要ありません。

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. 新しい IP アドレスが作成されないよう、リソースから *Microsoft.Network/publicIPAddresses* を削除します。

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. 新しい IP アドレスの作成に依存しないよう、*Microsoft.Network/loadBalancers* の IP アドレスの *dependsOn* 属性を削除します。  VNet の *dependsOn* 属性を追加します。これは、ロード バランサーが現在 VNet のサブネットに依存しているためです。

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. ロード バランサーの *frontendIPConfigurations* を、*publicIPAddress* ではなく、サブネットと *privateIPAddress* を使用するよう変更します。privateIPAddress は、定義済みの静的内部 IP アドレスを使用します。  動的 IP アドレスを使用するには、*privateIPAddress* 要素を削除して、*privateIPAllocationMethod* を "Dynamic" に変更します。

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. *Microsoft.ServiceFabric/clusters* リソースで、*managementEndpoint* が内部ロード バランサーのアドレスを指すように変更します。  **セキュリティで保護されたクラスターを使用している場合は、必ず 'http://' を 'https://' に変更してください。** (注: この手順は Service Fabric クラスター向けです。  仮想マシン スケール セットを使用している場合は省略してください。)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. テンプレートをデプロイします。

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

デプロイが完了すると、ロード バランサーでプライベート静的 IP アドレス (10.0.0.250) が使用されていることが確認できます。 同じ VNet 内に別のマシンがある場合は、内部の [Service Fabric エクスプローラー](service-fabric-visualizing-your-cluster.md) エンドポイントで、ロード バランサーの背後にあるいずれかのノードに接続していることを確認することもできます。

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>内部ロード バランサーと外部ロード バランサー

このシナリオでは、既存の単一ノード タイプの外部ロード バランサーを取得し、同じノード タイプの内部ロード バランサーを追加します。  バックエンド アドレス プールに接続されたバックエンド ポートは、1 つのロード バランサーにしか割り当てることができません。そのため、どちらのロード バランサーにアプリケーション ポートを配置し、どちらに管理エンドポイント (ポート 19000/19080) を配置するのかを決める必要があります。  管理エンドポイントを内部ロード バランサーに配置する場合は、前述の SFRP の制限に注意してください。  このサンプルでは、管理エンドポイントを外部ロード バランサーに置いたまま、アプリケーション用のポート (ポート 80) を追加して、それを内部ロード バランサーに配置します。

外部ロード バランサーと内部ロード バランサーを使用する 2 ノード タイプのクラスターが必要な場合は、2 つのロード バランサーが含まれた 2 ノード タイプのテンプレートをポータルで作成し、上の「内部ロード バランサー」セクションの説明に従って 2 つ目のロード バランサーを内部ロード バランサーに切り替えます。

1. 静的内部 LB IP アドレスのパラメーターを追加します (動的 IP アドレスが必要な場合は、上の注意事項をご覧ください)。

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. アプリケーション ポート 80 のパラメーターを追加します。

3. 既存のネットワーク変数の内部バージョンを追加します (既存の変数をコピーして名前に "-Int" を付加します)。

    ```
    /* Add internal LB networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* internal LB networking variables end */
    ```

4. ポータルで生成したテンプレートとアプリケーション ポート 80 を使用している場合は、ポータルの既定のテンプレートによって外部ロード バランサーに *AppPort1* (ポート 80) が追加されます。  この場合は AppPort1 を外部ロード バランサーの *loadBalancingRules* とプローブから削除して、これを内部ロード バランサーに追加できるようにします。

    ```
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe", 
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,,
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ], 
    "inboundNatPools": [
    ```

5. 2 つ目の *Microsoft.Network/loadBalancers* リソースを追加します。これは、前の「[内部ロード バランサー](#internallb)」セクションで作成した内部ロード バランサーに非常によく似ていますが、ロード バランサー変数 '-Int' を使用し、アプリケーション ポート 80 の実装だけを行います。  こうすることで、パブリック ロード バランサーの RDP エンドポイントを保持するために *inboundNatPools* も削除されます。内部ロード バランサーで RDP が必要な場合は、*inboundNatPools* を外部ロード バランサーからこの内部ロード バランサーに移動します。

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" LB variables */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add '-Internal' to name */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule, making sure to reference the "-Int" versions of the backendAddressPool, frontendIPConfiguration, and probe variables */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. *Microsoft.Compute/virtualMachineScaleSets* リソースの *NetworkProfile* で内部のバックエンド アドレス プールを追加します。

    ```
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. テンプレートをデプロイします。

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

デプロイが完了すると、リソース グループ内に 2 つのロード バランサーが表示されます。ここで、パブリック IP アドレスおよびパブリック IP アドレスに割り当てられている管理エンドポイント (ポート 19000/19080)、静的内部 IP アドレスおよび内部ロード バランサーに割り当てられているアプリケーション エンドポイント (ポート 80)、および同じ仮想マシン スケール セットのバックエンド プールを使用している両方のロード バランサーを確認できます。

## <a name="next-steps"></a>次のステップ
Service Fabric クラスターを Azure のネットワーク機能と統合する方法を学習したので、次は[クラスターを作成](service-fabric-cluster-creation-via-arm.md)してみましょう。 

