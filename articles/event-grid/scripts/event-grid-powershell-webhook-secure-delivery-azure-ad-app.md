---
title: Azure PowerShell - Azure Event Grid で Azure AD アプリを使用して Webhook 配信をセキュリティで保護する
description: Azure Event Grid を使用して Azure AD Application によって保護された HTTPS エンドポイントにイベントを配信する方法について説明します
ms.devlang: powershell
ms.topic: sample
ms.date: 10/14/2021
ms.openlocfilehash: d32676858c72685dfc70da476fb6fd8297194cfb
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066587"
---
# <a name="secure-webhook-delivery-with-azure-ad-application-in-azure-event-grid"></a>Azure Event Grid で Azure AD アプリを使用して Webhook 配信をセキュリティで保護する

このスクリプトは、Azure Event Grid を使用して Azure AD アプリによって保護された HTTPS エンドポイントにイベントを配信するための構成を提供します。

スクリプトの大まかな手順を次に示します。

1. **Microsoft.EventGrid** のサービス プリンシパルがない場合は作成します。
1. **Webhook のための Azure AD アプリ** で、**AzureEventGridSecureWebhookSubscriber** という名前のロールを作成します。
1. まだ存在しない場合は、**イベント サブスクリプション ライター アプリ** のサービス プリンシパルを作成します。
1. イベント サブスクリプション ライター Azure AD アプリのサービス プリンシパルを AzureEventGridSecureWebhookSubscriber ロールに追加します 
1. Microsoft.EventGrid のサービス プリンシパルも AzureEventGridSecureWebhookSubscriber ロールに追加します

## <a name="sample-script---stable"></a>サンプル スクリプト - 安定版

```azurepowershell
# NOTE: Before run this script ensure you are logged in Azure by using "az login" command.

$webhookAppObjectId = "[REPLACE_WITH_YOUR_ID]"
$eventSubscriptionWriterAppId = "[REPLACE_WITH_YOUR_ID]"

# Start execution
try {

    # Creates an application role of given name and description

    Function CreateAppRole([string] $Name, [string] $Description)
    {
        $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
        $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
        $appRole.AllowedMemberTypes.Add("Application");
        $appRole.AllowedMemberTypes.Add("User");
        $appRole.DisplayName = $Name
        $appRole.Id = New-Guid
        $appRole.IsEnabled = $true
        $appRole.Description = $Description
        $appRole.Value = $Name;

        return $appRole
    }

    # Creates Azure Event Grid Azure AD Application if not exists

    $eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7" # You don't need to modify this id
    $eventGridRoleName = "AzureEventGridSecureWebhookSubscriber" # You don't need to modify this role name
    $eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
    if ($eventGridSP -match "Microsoft.EventGrid")
    {
        Write-Host "The Azure AD Application is already defined.`n"
    } else {
        Write-Host "Creating the Azure Event Grid Azure AD Application"
        $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
    }

    # Creates the Azure app role for the webhook Azure AD application

    $app = Get-AzureADApplication -ObjectId $webhookAppObjectId
    $appRoles = $app.AppRoles

    Write-Host "Azure AD App roles before addition of the new role..."
    Write-Host $appRoles
    
    if ($appRoles -match $eventGridRoleName)
    {
        Write-Host "The Azure Event Grid role is already defined.`n"
    } else {      
        Write-Host "Creating the Azure Event Grid role in Azure AD Application: " $webhookAppObjectId
        $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
        $appRoles.Add($newRole)
        Set-AzureADApplication -ObjectId $app.ObjectId -AppRoles $appRoles
    }

    Write-Host "Azure AD App roles after addition of the new role..."
    Write-Host $appRoles

    # Creates the user role assignment for the app that will create event subscription

    $servicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $app.AppId + "'")
    $eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")

    if ($null -eq $eventSubscriptionWriterSP)
    {
        Write-Host "Create new Azure AD Application"
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
    }

    try
    {
        Write-Host "Creating the Azure AD Application role assignment: " $eventSubscriptionWriterAppId
        $eventGridAppRole = $app.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
        New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $servicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
    }
    catch
    {
        if( $_.Exception.Message -like '*Permission being assigned already exists on the object*')
        {
            Write-Host "The Azure AD Application role is already defined.`n"
        }
        else
        {
            Write-Error $_.Exception.Message
        }
        Break
    }

    # Creates the service app role assignment for Event Grid Azure AD Application

    $eventGridAppRole = $app.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
    New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $servicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
    # Print output references for backup

    Write-Host ">> Webhook's Azure AD Application Id: $($app.AppId)"
    Write-Host ">> Webhook's Azure AD Application ObjectId Id: $($app.ObjectId)"
}
catch {
  Write-Host ">> Exception:"
  Write-Host $_
  Write-Host ">> StackTrace:"  
  Write-Host $_.ScriptStackTrace
}
```

## <a name="script-explanation"></a>スクリプトの説明

詳細については、[Azure Event Grid で Azure AD を使用して Webhook 配信をセキュリティで保護する](../secure-webhook-delivery.md)ことに関する記事を参照してください。
