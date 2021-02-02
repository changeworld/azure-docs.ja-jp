---
title: Azure PowerShell の例 - Azure クラウド サービス (延長サポート) の詳細を取得する
description: Azure クラウド サービス (延長サポート) のデプロイに関する情報を取得するサンプルスクリプト
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 522ba95a5d6d97a8fde242274b1b0af969af20a5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881503"
---
# <a name="retrieve-information-about-your-azure-cloud-service-extended-support-deployments"></a>Azure クラウド サービス (延長サポート) のデプロイに関する情報を取得する

ここに挙げたサンプルでは、Azure クラウド サービス (延長サポート) の既存のデプロイに関する情報を取得する際のさまざまな方法を紹介します。

## <a name="get-all-cloud-services-under-a-resource-group"></a>あるリソース グループに所属するクラウド サービスをすべて取得する

```powershell
Get-AzCloudService -ResourceGroup "ContosOrg"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded
ContosOrg         ContosoCSTest     eastus2euap Failed
```

## <a name="get-single-cloud-service"></a>単一のクラウド サービスを取得する
```powershell
Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded

$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
$cloudService | Format-List
ResourceGroupName                       : ContosOrg
Configuration                           : <?xml version="1.0" encoding="utf-8"?>
                                          <ServiceConfiguration 
                                          xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
                                          </ServiceConfiguration>
ConfigurationUrl                        :
ExtensionProfileExtension               : {RDPExtension}
Id                                      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ContosOrg/providers/Microsoft.Compute/cloudServices/ContosoCS
Location                                : eastus2euap
Name                                    : ContosoCS
NetworkProfileLoadBalancerConfiguration : {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
OSProfileSecret                         : {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
PackageUrl                              : https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
ProvisioningState                       : Succeeded
RoleProfileRole                         : {ContosoFrontEnd, ContosoBackEnd}
StartCloudService                       :
SwappableCloudServiceId                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ContosOrg/providers/Microsoft.Compute/cloudServices/ContosoCSTest
Tag                                     : {
                                            "Owner": "Contos"
                                          }
Type                                    : Microsoft.Compute/cloudServices
UniqueId                                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
UpgradeMode                             : Auto
```

## <a name="get-cloud-service-instance-view"></a>クラウド サービス インスタンス ビューを取得する
```powershell
Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS" -InstanceView | Format-List

RoleInstanceStatusesSummary : {{
                                "code": "ProvisioningState/succeeded",
                                "count": 4
                              }, {
                                "code": "PowerState/started",
                                "count": 4
                              }}
Statuses                    : {{
                                "code": "ProvisioningState/succeeded",
                                "displayStatus": "Provisioning succeeded",
                                "level": "Info",
                                "time": "2020-10-20T13:13:45.0067685Z"
                              }, {
                                "code": "PowerState/started",
                                "displayStatus": "Started",
                                "level": "Info",
                                "time": "2020-10-20T13:13:45.0067685Z"
                              }, {
                                "code": "CurrentUpgradeDomain/-1",
                                "displayStatus": "Current Upgrade Domain of Cloud Service is -1.",
                                "level": "Info"
                              }, {
                                "code": "MaxUpgradeDomain/1",
                                "displayStatus": "Max Upgrade Domain of Cloud Service is 1.",
                                "level": "Info"
                              }}
```

## <a name="next-steps"></a>次のステップ

- Azure Cloud Services (延長サポート) の詳細については、[Azure Cloud Services (延長サポート) の概要](overview.md)に関するページを参照してください。
- [Cloud Services (延長サポート) のサンプル リポジトリ](https://github.com/Azure-Samples/cloud-services-extended-support)を確認します。