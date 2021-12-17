---
title: Azure Spring Cloud 用に Palo Alto を構成する方法
description: Azure Spring Cloud 用に Palo Alto を構成する方法
author: karlerickson
ms.author: vaangadi
ms.topic: how-to
ms.service: spring-cloud
ms.date: 09/17/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: f3b0aec801598faea2fd741177d34ecdc127a9a8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083386"
---
# <a name="how-to-configure-palo-alto-for-azure-spring-cloud"></a>Azure Spring Cloud 用に Palo Alto を構成する方法

**この記事の適用対象:** ✔️ Java ✔️ C#

この記事では、Palo Alto ファイアウォールで Azure Spring Cloud を使用する方法について説明します。

たとえば、[Azure Spring Cloud リファレンス アーキテクチャ](./reference-architecture.md)には、アプリケーションをセキュリティで保護するための Azure Firewall が含まれています。 ただし、現在のデプロイに Palo Alto ファイアウォールが含まれている場合は、この記事で説明されているように、Azure Spring Cloud デプロイから Azure Firewall を省略し、代わりに Palo Alto を使用することができます。

ルールやワイルドカードなどの構成情報は、Git リポジトリの CSV ファイルに保持する必要があります。 この記事では、オートメーションを使用してこれらのファイルを Palo Alto に適用する方法について説明します。 Palo Alto に適用される構成を理解するには、「[VNET での Azure Spring Cloud の実行に関するお客様の責任](./vnet-customer-responsibilities.md)」を参照してください。 

> [!Note]
> この記事では、REST API の使用について説明する際に、ユーザーの裁量に任されている名前と値を示すために PowerShell 変数構文を使用しています。 すべての手順で同じ値を使用してください。
>
> Palo Alto で TLS/SSL 証明書を構成したら、次の例のすべての Palo Alto REST API 呼び出しから `-SkipCertificateCheck` 引数を削除します。
>
> この記事は、Palo Alto REST API のリファレンスとしては使用しないでください。 すべての例は、デモンストレーションのみを目的としています。 正式な API の詳細については、Palo Alto のドキュメントの「[PAN-OS REST API](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/pan-os-rest-api.html)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* Palo Alto のデプロイ。 デプロイがない場合は、[Palo Alto を Azure Marketplace から](https://ms.portal.azure.com/#create/paloaltonetworks.vmseries-ngfwbundle2)プロビジョニングすることができます。
* [PowerShell](/powershell/scripting/install/installing-powershell)
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="configure-palo-alto"></a>Palo Alto を構成する

まず、Palo Alto VM-Series ファイアウォールを構成します。 詳細な手順については、[Azure Marketplace からの VM-Series ファイアウォールのデプロイ (ソリューション テンプレート)](https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/set-up-the-vm-series-firewall-on-azure/deploy-the-vm-series-firewall-on-azure-solution-template.html) に関するページを参照してください。 ここでは、VM-Series ファイアウォールをプロビジョニングし、`Trust` および `UnTrust` サブネットの両方と関連するネットワーク インターフェイス カードを構成する方法について説明します。 一貫性を維持するため、このファイアウォールは参照アーキテクチャの `Hub` 仮想ネットワークのアドレス空間に作成する必要があり ます。

[Azure の参照アーキテクチャ ガイド](https://www.paloaltonetworks.com/resources/guides/azure-architecture-guide)では、Azure でこのファイアウォールをデプロイするためのいくつかの技術設計モデルについて説明しています。

この記事の残りの部分では、次の 2 つの事前構成済みのネットワーク ゾーンがあることを前提としています。

* `Trust`。Azure Spring Cloud 仮想ネットワークとピアリングされた仮想ネットワークに接続されているインターフェイスが含まれています。
* `UnTrust`。VM-Series デプロイ ガイドで既に作成済みのパブリック インターネットへのインターフェイスが含まれています。

## <a name="prepare-csv-files"></a>CSV ファイルを準備する

次に、3 つの CSV ファイルを作成します。

1 つ目のファイルに *AzureSpringCloudServices.csv* という名前を付けます。 このファイルには、Azure Spring Cloud 用のイングレス ポートが含まれている必要があります。 次の例の値は、デモ目的でのみ提供されています。 必要なすべての値については、「[VNET での Azure Spring Cloud の実行に関するお客様の責任](./vnet-customer-responsibilities.md)」の「[Azure Spring Cloud のネットワーク要件](./vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements)」セクションを参照してください。

```CSV
name,protocol,port,tag
ASC_1194,udp,1194,AzureSpringCloud
ASC_443,tcp,443,AzureSpringCloud
ASC_9000,tcp,9000,AzureSpringCloud
ASC_445,tcp,445,AzureSpringCloud
ASC_123,udp,123,AzureSpringCloud
```

2 つ目のファイルに *AzureSpringCloudUrlCategories.csv* という名前を付けます。 このファイルには、Azure Spring Cloud からのエグレスに使用できるアドレス (ワイルドカードを含む) が含まれている必要があります。 次の例の値は、デモ目的でのみ提供されています。 最新の値については、「[Azure Spring Cloud の FQDN 要件またはアプリケーション ルール](./vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirementsapplication-rules)」を参照してください。

```CSV
name,description
*.azmk8s.io,
mcr.microsoft.com,
*.cdn.mscr.io,
*.data.mcr.microsoft.com,
management.azure.com,
*.microsoftonline.com,
*.microsoft.com,
packages.microsoft.com,
acs-mirror.azureedge.net,
mscrl.microsoft.com,
crl.microsoft.com,
crl3.digicert.com
```

3 つ目のファイルに *AzureMonitorAddresses.csv*  という名前を付けます。 このファイルには、Azure Monitor を使用している場合に Azure Monitor でメトリックと監視に使用できるようにするために、すべてのアドレスと IP 範囲を含める必要があります。 次の例の値は、デモ目的でのみ提供されています。 最新の値については、「[Azure Monitor で使用される IP アドレス](../azure-monitor/app/ip-addresses.md)」を参照してください。

```CSV
name,type,address,tag
40.114.241.141,ip-netmask,40.114.241.141/32,AzureMonitor
104.45.136.42,ip-netmask,104.45.136.42/32,AzureMonitor
40.84.189.107,ip-netmask,40.84.189.107/32,AzureMonitor
168.63.242.221,ip-netmask,168.63.242.221/32,AzureMonitor
52.167.221.184,ip-netmask,52.167.221.184/32,AzureMonitor
live.applicationinsights.azure.com,fqdn,live.applicationinsights.azure.com,AzureMonitor
rt.applicationinsights.microsoft.com,fqdn,rt.applicationinsights.microsoft.com,AzureMonitor
rt.services.visualstudio.com,fqdn,rt.services.visualstudio.com,AzureMonitor

```

## <a name="authenticate-into-palo-alto"></a>Palo Alto に対して認証する

次に、Palo Alto に対して認証を行い、API キーを取得する必要があります。 詳細については、Palo Alto のドキュメントの [API キーの取得](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-xml-api/get-your-api-key.html)に関するページを参照してください。

次の例では、PowerShell を使用して認証を行い、この記事の後半で使用する要求ヘッダーを生成します。

```powershell
$username=<username for PaloAlto>
$password=<password for PaloAlto>
$authResponse = irm "https://${PaloAltoIpAddress}/api/?type=keygen&user=${username}&password=${password}" -SkipCertificateCheck
$paloAltoHeaders = @{'X-PAN-KEY' = $authResponse.response.result.key; 'Content-Type' = 'application/json' }
```

## <a name="delete-existing-service-group"></a>既存のサービスを削除する

前の構成を試みた場合は、これらの構成をリセットし、セキュリティ規則とサービス グループを削除する必要があります。

次の例に示すように、[セキュリティ規則 REST API](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/create-security-policy-rule-rest-api.html) を使用してセキュリティ規則を削除します。

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=${paloAltoSecurityPolicyName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

次の例に示すように、サービス グループを削除します。

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${paloAltoServiceGroupName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

次の例に示すように、(*AzureSpringCloudServices.csv* で定義されているように) 各 Palo Alto サービスを削除します。

```powershell
Get-Content .\AzureSpringCloudServices.csv | ConvertFrom-Csv | select name | ForEach-Object {
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${_}"
    Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
}
```

## <a name="create-a-service-and-service-group"></a>サービスとサービス グループを作成する

前に作成した *AzureSpringCloudServices.csv* ファイルに基づいてサービスの作成を自動化するには、次の例を使用します。

```powershell
# Define a function to create and submit a Palo Alto service creation request
function New-PaloAltoService {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject]
        $ServiceObject
    )
    PROCESS {
        $requestBody = @{
            'entry' = @{
                '@name'    = $ServiceObject.name
                'protocol' = @{
                    $ServiceObject.protocol = @{
                        'port'     = $ServiceObject.port
                        'override' = @{
                            'no' = @{}
                        }

                    }
                }
                'tag'      = @{
                    'member' = @($ServiceObject.tag)
                }
            }
        }

        # Some rules in the CSV may need to conain source ports or descriptions. If these are present, populate them in the request
        if ($ServiceObject.description) {
            $requestBody.entry.description = $ServiceObject.description
        }
        if ($ServiceObject.'source-port') {
            $requestBody.entry.protocol."$($ServiceObject.protocol)".'source-port' = $ServiceObject.'source-port'
        }

        # Send the request
        $name = $requestBody.entry.'@name'
        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${name}"
         Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 9) -Verbose
    }
}

# Now invoke that function for every row in AzureSpringCloudServices.csv
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoService
```

次に、次の例に示すように、これらのサービスのサービス グループを作成します。

```powershell
# Create a function to consume service definitions and submit a service group creation request
function New-PaloAltoServiceGroup {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject[]]
        $RuleData,

        [Parameter(Mandatory = $true)]
        [string]
        $ServiceGroupName
    )
    begin {
        [array] $names = @()
    }

    process {
        $names += $RuleData.name
    }

    end {
        $requestBody = @{ 'entry' = [ordered] @{
                '@name'   = $ServiceGroupName
                'members' = @{ 'member' = $names }
                'tag'     = @{ 'member' = 'AzureSpringCloud' }
            }
        }

        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${ServiceGroupName}"

        Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json $requestBody) -Verbose
    }
}

# Run that function for all services in AzureSpringCloudServices.csv.
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoServiceGroup -ServiceGroupName 'AzureSpringCloud_SG'
```

## <a name="create-custom-url-categories"></a>カスタム URL カテゴリを作成する

次に、次の例に示すように、サービス グループのカスタム URL カテゴリを、Azure Spring Cloud からのエグレスを有効にするように定義します。

```powershell
# Read Service entries from CSV to enter into Palo Alto
$csvImport = Get-Content ${PSScriptRoot}/AzureSpringCloudUrls.csv | ConvertFrom-Csv

# Convert name column of CSV to add to the Custom URL Group in Palo Alto
$requestBody = @{ 'entry' = [ordered] @{
        '@name' = 'AzureSpringCloud_SG'
        'list'  = @{ 'member' = $csvImport.name }
        'type'  = 'URL List'
    }
} | ConvertTo-Json -Depth 9

$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/CustomURLCategories?location=vsys&vsys=vsys1&name=AzureSpringCloud_SG"

try {
    $existingObject = Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
}
catch {
}

Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body $requestBody -Verbose
```

## <a name="create-a-security-rule"></a>セキュリティ規則を作成する

次に、JSON ファイルを作成してセキュリティ規則を含めます。 このファイルに *Securityrule. json* という名前を付けて、次の内容を追加します。 2 つのゾーン `Trust` および `UnTrust` の名前は、「[Palo Alto を構成する](#configure-palo-alto)」セクションで前に説明したゾーン名と一致します。 `service/member`エントリには、前の手順で作成したサービス グループの名前が含まれています。

```json
{
    "entry": [
        {
            "@name": "azureSpringCloudRule",
            "@location": "vsys",
            "@vsys": "vsys1",
            "to": {
                "member": [
                    "UnTrust"
                ]
            },
            "from": {
                "member": [
                    "Trust"
                ]
            },
            "source-user": {
                "member": [
                    "any"
                ]
            },
            "application": {
                "member": [
                    "any"
                ]
            },
            "service": {
                "member": [
                    "AzureSpringCloud_SG"
                ]
            },
            "hip-profiles": {
                "member": [
                    "any"
                ]
            },
            "action": "allow",
            "category": {
                "member": [
                    "any"
                ]
            },
            "source": {
                "member": [
                    "any"
                ]
            },
            "destination": {
                "member": [
                    "any"
                ]
            }
        }
    ]
}
```

ここで、次の例に示すように、この規則を Palo Alto に適用します。

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=azureSpringCloudRule"

# Delete the rule if it already exists
try {
    $getResult = Invoke-RestMethod -Headers $paloAltoHeaders -Method Get -SkipCertificateCheck -Uri $url -Verbose
    if ($getResult.'@status' -eq 'success') {
        Invoke-RestMethod -Method Delete  -Headers $paloAltoHeaders -SkipCertificateCheck -Uri $url
    }
}
catch {}

# Create the rule from the JSON file
Invoke-WebRequest -Uri $url -Method Post -Headers $paloAltoHeaders -Body (Get-Content SecurityRule.json) -SkipCertificateCheck
```

## <a name="create-azure-monitor-addresses"></a>Azure Monitor アドレスを作成する

次に、*AzureMonitorAddresses.csv* ファイルを使用して、Palo Alto でアドレス オブジェクトを定義します。 次の例のコードでは、このタスクを自動化する方法を示します。

```powershell
Get-Content ./AzureMonitorAddresses.csv | ConvertFrom-Csv | ForEach-Object {
    $requestBody = @{ 'entry' = [ordered]@{
            '@name' = $_.name
            $_.type = $_.address
            'tag'   = @{ 'member' = @($_.tag) }
        }
    }

    $name = $requestBody.entry.'@name'
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Addresses?location=vsys&vsys=vsys1&name=${name}"

    # Delete the address if it already exists
    try {
        Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    }
    catch {
    }

    # Create the address
    Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 3) -Verbose
}
```

## <a name="commit-changes-to-palo-alto"></a>変更を Palo Alto にコミットする

上記の変更がアクティブになるように、その一部をコミットする必要があります。 これは次の REST API 呼び出しで行うことができます。

```powershell
$url = "https://${PaloAltoIpAddress}/api/?type=commit&cmd=<commit></commit>"
Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
```

## <a name="configure-the-security-rules-for-azure-spring-cloud-subnets"></a>Azure Spring Cloud サブネットのセキュリティ規則を構成する

次に、Palo Alto からのトラフィックが Azure Spring Cloud にアクセスできるように、ネットワーク セキュリティ規則を追加します。 次の例では、参照アーキテクチャによって作成されたスポーク ネットワーク セキュリティ グループ (NSG) (`nsg-spokeapp` および `nsg-spokeruntime`) を参照します。

PowerShell ウィンドウで次の Azure CLI コマンドを実行して、これらの NSG のそれぞれに必要なネットワーク セキュリティ規則を作成します。ここで、`$PaloAltoAddressPrefix` は、Palo Alto のプライベート IP の Classless Inter-Domain Routing (CIDR) アドレスです。

```azurecli
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeapp' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeruntime' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
```

## <a name="configure-the-next-hop"></a>次ホップを構成する

Palo Alto を構成したら、発信インターネット アクセスの次ホップとして Palo Alto を持つように Azure Spring Cloud を構成します。 この構成には、PowerShell ウィンドウで次の Azure CLI コマンドを使用できます。 次の変数の値を指定してください。

* `$AppResourceGroupName`: Azure Spring Cloud を含むリソース グループの名前。
* `$AzureSpringCloudServiceSubnetRouteTableName`: Azure Spring Cloud サービス/ランタイム サブネット ルート テーブルの名前。 参照アーキテクチャでは、これは `rt-spokeruntime` に設定されます。
* `$AzureSpringCloudAppSubnetRouteTableName`: Azure Spring Cloud アプリのサブネット ルート テーブルの名前。 参照アーキテクチャでは、これは `rt-spokeapp` に設定されます。

```azurecli
az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudServiceSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose

az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudAppSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose
```

これで構成は完了です。

## <a name="next-steps"></a>次のステップ

* [Azure Spring Cloud アプリのログをリアルタイムでストリーム配信する](./how-to-log-streaming.md)
* [Azure Spring Cloud での Application Insights Java In-Process Agent](./how-to-application-insights.md)
* [Azure Spring Cloud へのアプリケーション デプロイを自動化する](./how-to-cicd.md)