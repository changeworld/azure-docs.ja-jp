---
title: Immersive Reader のリソースを作成する
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム サブドメインを含む新しい Immersive Reader リソースを作成した後、Azure テナントで Azure AD を構成する方法について説明します。
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330721"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Immersive Reader リソースを作成して Azure Active Directory 認証を構成する

この記事では、Immersive Reader リソースの作成と Azure Active Directory (Azure AD) 認証の構成を行うスクリプトを紹介します。 このスクリプトとポータルのどちらを使用した場合でも、Immersive Reader リソースが作成されるたびに、Azure AD のアクセス許可も構成する必要があります。 その作業に、このスクリプトが役に立ちます。

このスクリプトは、Immersive Reader と Azure AD に関して必要なすべてのリソースを、1 つの手順だけで作成および構成できるように設計されています。 ただし、たとえば、Azure portal で既に Immersive Reader リソースを作成していた場合は、既存の Immersive Reader リソースに対する Azure AD 認証の構成だけを行うこともできます。

お客様によっては、開発向けと運用向け、またはサービスのデプロイ先となる複数の異なるリージョン向けなど、複数の Immersive Reader リソースを作成しなければならない場合もあります。 そのような場合は、スクリプトを複数回使用してさまざまな Immersive Reader リソースを作成し、Azure AD のアクセス許可を構成することができます。

スクリプトは柔軟に設計されています。 最初は、お使いのサブスクリプション内で既存の Immersive Reader と Azure AD リソースを探し、存在しない場合は必要に応じて作成します。 Immersive Reader リソースの作成が初めての場合、必要な処理はすべてスクリプトによって実行されます。 ポータルで作成された既存の Immersive Reader リソースに Azure AD を構成するためだけに使用したい場合は、その処理も行われます。 また、複数の Immersive Reader リソースの作成と構成にも使用できます。

## <a name="set-up-powershell-environment"></a>PowerShell 環境をセットアップする

1. まず、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を開きます。 左上のドロップダウンを参照するか、または「`pwsh`」と入力して、Cloud Shell が PowerShell に設定されていることを確認します。

1. 以下のコード スニペットをコピーしてシェルに貼り付けます。

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. 必要に応じてパラメーターを指定して、関数 `Create-ImmersiveReaderResource` を実行します。

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | パラメーター | 説明 |
    | --- | --- |
    | SubscriptionName |Immersive Reader リソースで使用する Azure サブスクリプションの名前。 リソースを作成するには、サブスクリプションが必要です。 |
    | ResourceName |  英数字を使用する必要があります。最初または最後の文字を除き、"-" を含めることができます。 長さは 63 文字を超えないようにしてください。|
    | ResourceSubdomain |Immersive Reader リソースには、カスタム サブドメインが必要です。 サブドメインは、Immersive Reader サービスを呼び出してリーダーを起動する際に、SDK によって使用されます。 サブドメインはグローバルに一意である必要があります。 サブドメインには英数字を使用する必要があります。最初または最後の文字を除き、"-" を含めることができます。 長さは 63 文字を超えないようにしてください。 リソースが既に存在する場合、このパラメーターは省略可能です。 |
    | ResourceSKU |オプション: `S0`。 使用可能な各 SKU の詳細については、[Cognitive Services の価格に関するページ](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/)を参照してください。 リソースが既に存在する場合、このパラメーターは省略可能です。 |
    | ResourceLocation |オプションは、`eastus`、`eastus2`、`southcentralus`、`westus`、`westus2`、`australiaeast`、`southeastasia`、`centralindia`、`japaneast`、`northeurope`、`uksouth`、`westeurope` です。 リソースが既に存在する場合、このパラメーターは省略可能です。 |
    | ResourceGroupName |リソースは、サブスクリプション内のリソース グループに作成されます。 既存のリソース グループの名前を指定します。 リソース グループがまだ存在しない場合は、この名前を使用して新しく作成されます。 |
    | ResourceGroupLocation |リソース グループが存在しない場合は、グループの作成先の場所を指定する必要があります。 場所の一覧を表示するには、`az account list-locations` を実行します。 返された結果の *name* プロパティ (スペースなし) を使用します。 リソース グループが既に存在する場合、このパラメーターは省略可能です。 |
    | AADAppDisplayName |Azure Active Directory アプリケーションの表示名。 既存の Azure AD アプリケーションが見つからない場合は、この名前を使用して新しく作成されます。 Azure AD アプリケーションが既に存在する場合、このパラメーターは省略可能です。 |
    | AADAppIdentifierUri |Azure AD アプリの URI。 既存の Azure AD アプリが見つからない場合は、この URI を使用して新しく作成されます。 たとえば、「 `https://immersivereaderaad-mycompany` 」のように入力します。 |
 | AADAppClientSecret |作成するパスワード。このパスワードは、後で Immersive Reader を起動するためのトークンを取得する際の認証に使用されます。 パスワードは 16 文字以上でなければなりません。また、特殊文字を 1 文字以上、数字を 1 文字以上含める必要があります。 |

1. 後で使用するために、JSON 出力をテキスト ファイルにコピーします。 出力は次のようになります。

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>次のステップ

* [Node.js クイックスタート](./quickstart-nodejs.md)で、Node.js と Immersive Reader SDK を使用して他にできることを確認する
* [Python チュートリアル](./tutorial-python.md)で、Python と Immersive Reader SDK を使用して他にできることを確認する
* [Swift チュートリアル](./tutorial-ios-picture-immersive-reader.md)で、Swift と Immersive Reader SDK を使用して他にできることを確認する
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](./reference.md)を探索する




