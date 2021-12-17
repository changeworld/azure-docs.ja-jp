---
title: Azure Virtual Desktop (クラシック) サービス プリンシパル ロールの割り当て - Azure
description: Azure Virtual Desktop (クラシック) で PowerShell を使用して、サービス プリンシパルを作成し、ロールを割り当てる方法。
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: eacb82408c2981375e599cf29c67b016ba0f3b41
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111749851"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-azure-virtual-desktop-classic"></a>チュートリアル: Azure Virtual Desktop (クラシック) で PowerShell を使用してサービス プリンシパルとロールの割り当てを作成する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトをサポートしていない Azure Virtual Desktop (クラシック) に適用されます。

サービス プリンシパルとは、特定の目的に使用するロールやアクセス許可を割り当てるために Azure Active Directory に作成できる ID です。 Azure Virtual Desktop では、次の用途でサービス プリンシパルを作成できます。

- Azure Virtual Desktop の特定の管理タスクを自動化する。
- Azure Virtual Desktop 用の Azure Resource Manager テンプレートを実行する際、MFA が求められるユーザーの代わりとなる資格情報として使用する。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Active Directory にサービス プリンシパルを作成します。
> * Azure Virtual Desktop にロールの割り当てを作成します。
> * サービス プリンシパルを使用して Azure Virtual Desktop にサインインします。

## <a name="prerequisites"></a>前提条件

サービス プリンシパルとロールの割り当てを作成するには、次の 3 つのことを行う必要があります。

1. AzureAD モジュールをインストールします。 このモジュールをインストールするには、PowerShell を管理者として実行し、次のコマンドレットを実行します。

    ```powershell
    Install-Module AzureAD
    ```

2. [Azure Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します。

3. 同じ PowerShell セッションで、この記事のすべての手順を実行します。 ウィンドウを閉じてから開き直すことによって PowerShell セッションを中断すると、手順がうまくいかない場合があります。

## <a name="create-a-service-principal-in-azure-active-directory"></a>Azure Active Directory にサービス プリンシパルを作成する

これらの前提条件を PowerShell セッションで満たしたら、次の PowerShell コマンドレットを実行して、マルチテナント サービス プリンシパルを Azure に作成します。

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>PowerShell で資格情報を確認する

サービス プリンシパルのロールの割り当てを作成する前に、自分の資格情報を確認し、後で参照できるよう書き留めておいてください。 特にパスワードは、この PowerShell セッションを閉じた後は取得できなくなるので注意が必要です。

次に示したのは、書き留めておくべき 3 つの資格情報と、それらを取得するために実行する必要のあるコマンドレットです。

- Password (パスワード):

    ```powershell
    $svcPrincipalCreds.Value
    ```

- テナント ID:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- アプリケーション ID:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-azure-virtual-desktop"></a>Azure Virtual Desktop にロールの割り当てを作成する

次に、サービス プリンシパルが Azure Virtual Desktop にサインインできるよう、ロールの割り当てを作成する必要があります。 必ずロールの割り当てを作成するアクセス許可があるアカウントでサインインしてください。

まず、PowerShell セッション内で使用する [Azure Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します (まだ行っていない場合)。

次の PowerShell コマンドレットを実行して、Azure Virtual Desktop に接続し、テナントを表示します。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

ロールの割り当てを作成するテナントの名前を見つけたら、その名前を次のコマンドレットで使用します。

```powershell
$myTenantName = "<Azure Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>サービス プリンシパルでサインインする

サービス プリンシパルに対してロールの割り当てを作成したら、そのサービス プリンシパルが Azure Virtual Desktop にサインインできることを次のコマンドレットを実行して確認します。

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

サインインしたら、そのサービス プリンシパルで、いくつかの Azure Virtual Desktop PowerShell コマンドレットを試し、すべてがうまく機能することを確認します。

## <a name="next-steps"></a>次の手順

サービス プリンシパルを作成して Azure Virtual Desktop テナント内のロールに割り当てたら、それを使用してホスト プールを作成できます。 ホスト プールについて詳しく学習するには、Azure Virtual Desktop でのホスト プールの作成に関するチュートリアルに進んでください。

 > [!div class="nextstepaction"]
 > [Azure Marketplace を使用してホスト プールを作成する](create-host-pools-azure-marketplace-2019.md)
