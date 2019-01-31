---
title: Kubernetes を Azure Stack Marketplace に追加する | Microsoft Docs
description: Kubernetes を Azure Stack Marketplace に追加する方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: b7e293018f2dc248fae8de8bd9e82943606f4ead
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239504"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Kubernetes を Azure Stack Marketplace に追加する

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

> [!note]  
> Azure Stack 上の Kubernetes はプレビュー段階にあります。

Kubernetes は、Marketplace 項目としてユーザーに提供できます。 ユーザーはその後、1 回の連携した操作で Kubernetes をデプロイできます。

以降の記事では、Azure Resource Manager テンプレートを使用して、スタンドアロンの Kubernetes Cluster のためにリソースのデプロイとプロビジョニングを行う方法を示しています。 Kubernetes Cluster Marketplace の項目 0.3.0 には、Azure Stack バージョン 1808 が必要です。 開始する前に、Azure Stack とグローバルな Azure テナントの設定を確認します。 必要な、Azure Stack に関する情報を収集します。 テナントと Azure Stack Marketplace に必要なリソースを追加します。 このクラスターは、Ubuntu サーバー、カスタム スクリプト、およびマーケットプレース内に置かれる Kubernetes 項目に依存します。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>プラン、オファー、サブスクリプションを作成する

Kubernetes の Marketplace 項目のプラン、オファー、サブスクリプションを作成します。 既存のプランとオファーを使うこともできます。

1. [管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

1. プランは基本プランとして作成します。 手順については、「[Azure Stack でのプランの作成](azure-stack-create-plan.md)」をご覧ください。

1. オファーを作成します。 手順については、「[Azure Stack でのオファーの作成](azure-stack-create-offer.md)」をご覧ください。

1. **[オファー]** を選択し、作成したプランを見つけます。

1. [オファー] ブレードで **[概要]** を選択します。

1. **[状態の変更]** を選択します。 **[パブリック]** を選択します。

1. **[+ リソースの作成]** > **[Offers and Plans] (オファーとプラン)** > **[サブスクリプション]** と選択し、サブスクリプションを作成します。

    a. **表示名**を入力します。

    b. **ユーザー**を入力します。 テナントに関連付けられている Azure AD アカウントを使用します。

    c. **[プロバイダーの説明]**

    d. **[Directory tenant]** を Azure Stack の Azure AD テナントに設定します。 

    e. **[オファー]** を選択します。 作成したオファーの名前を選択します。 サブスクリプション ID をメモします。

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>AD FS でサービス プリンシパルと資格情報を作成する

ID 管理サービスのために Active Directory Federated Services (AD FS) を使用する場合は、Kubernetes クラスターをデプロイするユーザーのサービス プリンシパルを作成する必要があります。

1. サービス プリンシパルの作成に使用する証明書を作成し、エクスポートします。 下記のコード スニペットは、自己署名証明書を作成する方法を示しています。 

    - 次の情報が必要です。

       | 値 | 説明 |
       | ---   | ---         |
       | パスワード | 証明書のパスワード。 |
       | ローカルの証明書パス | 証明書のパスとファイル名。 次に例を示します。`path\certfilename.pfx` |
       | 証明書名 | 証明書の名前。 |
       | 証明書ストアの場所 |  たとえば、`Cert:\LocalMachine\My` のように指定します。 |

    - 管理者特権のプロンプトで PowerShell を開きます。 実際の値に更新したパラメーターを指定して、次のスクリプトを実行します。

        ```PowerShell  
        # Creates a new self signed certificate 
        $passwordString = "<password>"
        $certlocation = "<local certificate path>.pfx"
        $certificateName = "<certificate name>"
        #certificate store location. Eg. Cert:\LocalMachine\My
        $certStoreLocation="<certificate store location>"
        
        $params = @{
        CertStoreLocation = $certStoreLocation
        DnsName = $certificateName
        FriendlyName = $certificateName
        KeyLength = 2048
        KeyUsageProperty = 'All'
        KeyExportPolicy = 'Exportable'
        Provider = 'Microsoft Enhanced Cryptographic Provider v1.0'
        HashAlgorithm = 'SHA256'
        }
        
        $cert = New-SelfSignedCertificate @params -ErrorAction Stop
        Write-Verbose "Generated new certificate '$($cert.Subject)' ($($cert.Thumbprint))." -Verbose
        
        #Exports certificate with password in a .pfx format
        $pwd = ConvertTo-SecureString -String $passwordString -Force -AsPlainText
        Export-PfxCertificate -cert $cert -FilePath $certlocation -Password $pwd
        ```

2. 証明書を使用したサービス プリンシパルの作成。

    - 次の情報が必要です。

       | 値 | 説明                     |
       | ---   | ---                             |
       | ERCS IP | ASDK では、特権エンドポイントは通常 `AzS-ERCS01` です。 |
       | アプリケーション名 | アプリケーションのサービス プリンシパルの簡易名。 |
       | 証明書ストアの場所 | 証明書を保存したコンピューター上のパス。 次に例を示します。`Cert:\LocalMachine\My\<someuid>` |

    - 管理者特権のプロンプトで PowerShell を開きます。 実際の値に更新したパラメーターを指定して、次のスクリプトを実行します。

        ```PowerShell  
        #Create service principal using the certificate
        $privilegedendpoint="<ERCS IP>"
        $applicationName="<application name>"
        #certificate store location. Eg. Cert:\LocalMachine\My
        $certStoreLocation="<certificate store location>"
        
        # Get certificate information
        $cert = Get-Item $certStoreLocation
        
        # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
        $creds = Get-Credential

        # Creating a PSSession to the ERCS PrivilegedEndpoint
        $session = New-PSSession -ComputerName $privilegedendpoint -ConfigurationName PrivilegedEndpoint -Credential $creds

        # Get Service principal Information
        $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name "$using:applicationName" -ClientCertificates $using:cert}

        # Get Stamp information
        $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }

        # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
        $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

        # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
        $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

        # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
        $TenantID = $AzureStackInfo.AADTenantID

        # Register an AzureRM environment that targets your Azure Stack instance
        Add-AzureRMEnvironment `
        -Name "AzureStackUser" `
        -ArmEndpoint $ArmEndpoint

        # Set the GraphEndpointResourceId value
        Set-AzureRmEnvironment `
        -Name "AzureStackUser" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true
        Add-AzureRmAccount -EnvironmentName "azurestackuser" `
        -ServicePrincipal `
        -CertificateThumbprint $ServicePrincipal.Thumbprint `
        -ApplicationId $ServicePrincipal.ClientId `
        -TenantId $TenantID

        # Output the SPN details
        $ServicePrincipal
        ```

    - サービス プリンシパルの詳細は、次のスニペットのようになります

        ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
        ```

## <a name="add-an-ubuntu-server-image"></a>Ubuntu サーバーのイメージを追加する

次の Ubuntu Server イメージを Marketplace に追加します。

1. [管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

1. **[すべてのサービス]** を選択し、**[管理]** カテゴリで **[Marketplace management] (Marketplace 管理)** を選択します。

1. **+ Add from Azure**(+ Azure から追加) を選択します。

1. 「 `UbuntuServer` 」を入力します。

1. サーバーの最新バージョンを選択します。 通常版を確認して、最新バージョンがインストールされていることを確認してください。
    - **発行元**: Canonical
    - **プラン**: UbuntuServer
    - **バージョン**:16.04.201806120 (または最新バージョン)
    - **SKU**:16.04 LTS

1. **[ダウンロード]** を選択します。

## <a name="add-a-custom-script-for-linux"></a>Linux 用のカスタム スクリプトを追加する

Marketplace から Kubernetes を追加します。

1. [管理ポータル](https://adminportal.local.azurestack.external)を開きます。

1. **[すべてのサービス]** を選択し、**[管理]** カテゴリで **[Marketplace management] (Marketplace 管理)** を選択します。

1. **+ Add from Azure**(+ Azure から追加) を選択します。

1. 「 `Custom Script for Linux` 」を入力します。

1. 次のプロファイルを持つスクリプトを選択します。
    - **プラン**: Linux 2.0 用のカスタム スクリプト
    - **バージョン**:2.0.6 (または最新バージョン)
    - **[発行者]**: Microsoft Corp

    > [!Note]  
    > 複数バージョンの Linux 用カスタム スクリプトが表示されることがあります。 最新バージョンの項目を追加する必要があります。

1. **[ダウンロード]** を選択します。


## <a name="add-kubernetes-to-the-marketplace"></a>Kubernetes をマーケットプレースに追加する

1. [管理ポータル](https://adminportal.local.azurestack.external)を開きます。

1. **[すべてのサービス]** を選択し、**[管理]** カテゴリで **[Marketplace management] (Marketplace 管理)** を選択します。

1. **+ Add from Azure**(+ Azure から追加) を選択します。

1. 「 `Kubernetes` 」を入力します。

1. [`Kubernetes Cluster`] を選択します。

1. **[ダウンロード]** を選択します。

    > [!note]  
    > マーケットプレース項目が Marketplace に表示されるまで 5 分かかる場合があります。

    ![Kubernetes](user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Kubernetes を更新または削除する 

Kubernetes 項目の更新時には、Marketplace で以前の項目を削除する必要があります。 この記事の指示に従って、Kubernetes の更新をマーケットプレースに追加します。

Kubernetes を削除するには:

1. オペレーターとして PowerShell を使用して Azure Stack に接続します。 手順については、「[Azure Stack の PowerShell 環境を構成する](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin)」を参照してください。

2. ギャラリーで、Kubernetes クラスターの現在の項目を検索します。

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. 現在の項目の名前に注意してください。例: `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. 次の PowerShell コマンドレットを使用して項目を削除します。

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>次の手順

[Kubernetes を Azure Stack にデプロイする](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

[Azure Stack でのサービスの提供の概要](azure-stack-offer-services-overview.md)
