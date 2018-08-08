---
title: Azure Stack でのマルチテナント
description: Azure Stack で複数の Azure Active Directory ディレクトリをサポートする方法を説明します。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: patricka
ms.openlocfilehash: e61b4457cd88c236145ce7595ee7db4340538465
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330543"
---
# <a name="multi-tenancy-in-azure-stack"></a>Azure Stack でのマルチテナント

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

複数の Azure Active Directory (Azure AD) テナントのユーザーが Azure Stack のサービスを使用できるように Azure Stack を構成することができます。 ここでは、次のシナリオを例に説明します。

 - あなたは contoso.onmicrosoft.com のサービス管理者です。ここに Azure Stack がインストールされています。
 - メアリーは、ゲスト ユーザーがいる fabrikam.onmicrosoft.com のディレクトリ管理者です。 
 - メアリーの会社は、あなたの会社から IaaS および PaaS サービスを受けており、ゲスト ディレクトリ (fabrikam.onmicrosoft.com) のユーザーが、contoso.onmicrosoft.com にある Azure Stack のリソースにサインインして使用できるようにする必要があります。

このガイドでは、このシナリオに基づいて、Azure Stack でマルチテナントを構成するために必要な手順を説明します。 このシナリオでは、Fabrikam のユーザーが Contoso の Azure Stack デプロイのサービスにサインインして使用できるようにするための手順を、あなたとメアリーがそれぞれ完了する必要があります。  

## <a name="enable-multi-tenancy"></a>マルチテナントの有効化

Azure Stack でマルチテナントを構成する前に、いくつかの前提条件を考慮する必要があります。
  
 - Azure Stack がインストールされているディレクトリ (Contoso) とゲスト ディレクトリ (Fabrikam) の両方に対して、あなたとメアリーが連携して管理上の手順を実行する必要があります。  
 - Azure Stack 用 PowerShell の[インストール](azure-stack-powershell-install.md)と[構成](azure-stack-powershell-configure-admin.md)が済んでいることを確認してください。
 - [Azure Stack Tools をダウンロード](azure-stack-powershell-download.md)して、Connect モジュールと Identity モジュールをインポートします。

    ````PowerShell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ````

 - メアリーは Azure Stack への [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) アクセスが必要です。 

### <a name="configure-azure-stack-directory"></a>Azure Stack ディレクトリの構成

このセクションでは、Fabrikam の Azure AD ディレクトリのテナントからのサインインを許可するように Azure Stack を構成します。

ゲスト ディレクトリ テナントのユーザーとサービス プリンシパルを受け入れるように Azure Resource Manager を構成して、ゲスト ディレクトリ テナント (Fabrikam) を Azure Stack にオンボードします。

````PowerShell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
````

### <a name="configure-guest-directory"></a>ゲスト ディレクトリの構成

あなたが Azure Stack ディレクトリでの手順を完了した後に、メアリーは Azure Stack がゲスト ディレクトリにアクセスすることに同意し、Azure Stack をゲスト ディレクトリに登録する必要があります。 

#### <a name="registering-azure-stack-with-the-guest-directory"></a>ゲスト ディレクトリへの Azure Stack の登録

ゲスト ディレクトリの管理者が、Azure Stack が Fabrikam のディレクトリにアクセスすることに同意したら、メアリーは Azure Stack を Fabrikam のディレクトリ テナントに登録する必要があります。

````PowerShell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````

> [!IMPORTANT]
> Azure Stack 管理者が今後新しいサービスや更新プログラムをインストールした場合には、このスクリプトをもう一度実行する必要があります。
>
> このスクリプトは、ディレクトリ内の Azure Stack アプリケーションの状態を確認するためにいつでも実行できます。

### <a name="direct-users-to-sign-in"></a>ユーザーをサインインに誘導する

あなたとメアリーの両方が Fabrikam ディレクトリのオンボード手順を完了したので、メアリーは Fabrikam のユーザーをサインインに誘導することができます。  Fabrikam のユーザー (つまり、fabrikam.onmicrosoft.com のサフィックスを持つユーザー) は、https://portal.local.azurestack.external にアクセスしてサインインします。  

メアリーは、Fabrikam ディレクトリの[外部プリンシパル](../role-based-access-control/rbac-and-directory-admin-roles.md) (つまり、fabrikam.onmicrosoft.com のサフィックスを持たない Fabrikam ディレクトリのユーザー) はすべて、https://portal.local.azurestack.external/fabrikam.onmicrosoft.com を使用してサインインするよう誘導します。  この URL を使用しなかった場合、これらのユーザーは既定のディレクトリ (Fabrikam) に送られ、管理者による同意がないことを示すエラーが表示されます。

## <a name="disable-multi-tenancy"></a>マルチテナントの無効化

Azure Stack 内に複数のテナントを持つ必要がなくなった場合は、次の手順を実行してマルチ テナントを無効にできます。

1. ゲスト ディレクトリの管理者 (このシナリオでは Mary) として、*Unregister-AzsWithMyDirectoryTenant* を実行します。 このコマンドレットは、新しいディレクトリからすべての Azure Stack アプリケーションをアンインストールします。

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Azure Stack のサービス管理者 (このシナリオではあなた) として、*Unregister-AzSGuestDirectoryTenant* を実行します。 

    ``` PowerShell  
    ## The following Azure Resource Manaager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > マルチ テナントを無効にする手順は、正しい順序で実行する必要があります。 手順 2 を先に実行した場合、手順 1 は失敗します。

## <a name="next-steps"></a>次の手順

- [委任されたプロバイダーの管理](azure-stack-delegated-provider.md)
- [Azure Stack の主要概念](azure-stack-key-features.md)
