---
title: Azure Event Grid の Azure AD を使用して WebHook 配信をセキュリティで保護する
description: Azure Event Grid を使用して Azure Active Directory によって保護された HTTPS エンドポイントにイベントを配信する方法について説明します
ms.topic: how-to
ms.date: 03/20/2021
ms.openlocfilehash: 1298910db78ba468dd9744e84ee4629161e0a776
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076038"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Azure Active Directory で保護されたエンドポイントにイベントを発行する
この記事では、Azure Active Directory (Azure AD) を使用して、**イベント サブスクリプション** と **Webhook エンドポイント** 間の接続をセキュリティで保護する方法について説明します。 Azure AD アプリケーションとサービス プリンシパルの概要については、[Microsoft ID プラットフォーム (v2.0) の概要](../active-directory/develop/v2-overview.md)に関するページを参照してください。

この記事ではデモンストレーションのために Azure portal を使用しますが、CLI、PowerShell、または SDK を使用して機能を有効にすることもできます。


## <a name="create-an-azure-ad-application"></a>Azure AD アプリケーションを作成する
保護されたエンドポイント用の Azure AD アプリケーションを作成して、Webhook を Azure AD に登録します。 「[シナリオ: 保護された Web API](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview)」を参照してください。 デーモン アプリで呼び出されるよう、保護された API を構成します。
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Event Grid で Azure AD アプリケーションを使用できるようにします
このセクションでは、Event Grid を有効にして Azure AD アプリケーションを使用する方法を紹介します。 

> [!NOTE]
> このスクリプトを実行するには、[Azure AD アプリケーション管理者ロール](../active-directory/roles/permissions-reference.md#all-roles)のメンバーである必要があります。

### <a name="connect-to-your-azure-tenant"></a>Azure テナントに接続します。
まず、`Connect-AzureAD` コマンドを使用して Azure テナントに接続します。 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Microsoft EventGrid サービス プリンシパルの作成
**Microsoft.EventGrid** のサービス プリンシパルがない場合、次のスクリプトを使用して作成します。 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>アプリケーションのロールの作成   
次のスクリプトを実行し、Azure AD アプリケーションのロールを作成します。 この例では、ロールの名前は **AzureEventGridSecureWebhookSubscriber** です。 自分の Azure AD テナント ID を使用するように PowerShell スクリプトの `$myTenantId` を変更します。`$myAzureADApplicationObjectId` は、お使いの Azure AD アプリケーションのオブジェクト ID に変更します。

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
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
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-a-role-assignment"></a>ロールの割り当てを作成する
ロールの割り当ては、イベント サブスクリプションを作成する AAD アプリまたは AAD ユーザーの Webhook Azure AD アプリに作成する必要があります。 AAD アプリと AAD ユーザーのどちらでイベント サブスクリプションを作成するかに応じて、下のいずれかのスクリプトを使用します。

#### <a name="option-a-create-a-role-assignment-for-event-subscription-aad-app"></a>オプション A。イベント サブスクリプション AAD アプリに対するロールの割り当てを作成する 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="option-b-create-a-role-assignment-for-event-subscription-aad-user"></a>オプション B。イベント サブスクリプション AAD ユーザーに対するロールの割り当てを作成する 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
New-AzureADUserAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Event Grid サービス プリンシパルをロールに追加する
New-AzureADServiceAppRoleAssignment コマンドを実行して、前の手順で作成したロールに Event Grid サービス プリンシパルを割り当てます。

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

次のコマンドを実行して、後で使用する情報を出力します。

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>イベント サブスクリプションを構成する
イベント サブスクリプションを作成するときは、次の手順を実行します。

1. エンドポイントの種類として **[Webhook]** を選択します。 
1. エンドポイントの **URI** を指定します。

    ![エンドポイントの種類で Webhook を選択します。](./media/secure-webhook-delivery/select-webhook.png)
1. **[イベント サブスクリプションの作成]** ページの上部にある **[追加機能]** タブを選択します。
1. **[追加機能]** タブで、次の手順を実行します。
    1. **[AAD 認証を使用する]** を選択し、テナント ID とアプリケーション ID を構成します。
    1. スクリプトの出力から Azure AD のテナント ID をコピーし、 **[AAD テナント ID]** フィールドに入力します。
    1. スクリプトの出力から Azure AD のアプリケーション ID をコピーし、 **[AAD アプリケーション ID]** フィールドに入力します。

        ![Secure Webhook アクション](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>次のステップ

* イベント配信の監視について詳しくは、「[Event Grid メッセージ配信の監視](monitor-event-delivery.md)」をご覧ください。
* 認証キーについて詳しくは、「[Event Grid のセキュリティと認証](security-authentication.md)」をご覧ください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
