---
title: "既定の VM イメージの Azure Stack Marketplace への追加 | Microsoft Docs"
description: "Windows Server 2016 VM の既定のイメージを Azure Stack Marketplace に追加します。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2849E53F-3D58-48A5-8007-3238FC39F630
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/23/2018
ms.author: mabrigg
ms.openlocfilehash: b0b0a4af1d852de516d387697afb2760b967db43
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2018
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Windows Server 2016 VM イメージの Azure Stack Marketplace への追加

既定では、Azure Stack Marketplace には使用可能な仮想マシン イメージがありません。 ユーザーがイメージを使用できるようにするには、Azure Stack オペレーターがイメージを Marketplace に追加する必要があります。 Windows Server 2016 イメージを Azure Stack Marketplace に追加するには、次のどちらかの方法を使用します。

* [Azure Marketplace からイメージをダウンロードする](#add-the-image-by-downloading-it-from-the-azure-marketplace)。 接続されたシナリオで操作しており、Azure Stack インスタンスを Azure に登録している場合は、このオプションを使用します。

* [PowerShell を使用してイメージを追加する](#add-the-image-by-using-powershell)。 接続が切断されたシナリオまたは接続が制限されたシナリオで Azure Stack をデプロイした場合は、このオプションを使用します。

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Azure Marketplace からイメージをダウンロードして追加

1. Azure Stack をデプロイした後、Azure Stack Development Kit にサインインします。

2. **[More services]\(その他のサービス\)** > **[Marketplace Management]\(Marketplace の管理\)** > **[Add from Azure]\(Azure から追加\)** を選択します。 

3. **Windows Server 2016 Datacenter** イメージを検索し、**[ダウンロード]** を選択します。

   ![Azure からのイメージのダウンロード](media/azure-stack-add-default-image/download-image.png)

ダウンロードが完了すると、そのイメージが **[Marketplace Management]\(Marketplace の管理\)** で利用可能になります。 イメージは **[Compute]\(コンピューティング\)** でも利用可能で、これを使用して新しい仮想マシンを作成することができます。

## <a name="add-the-image-by-using-powershell"></a>PowerShell を使用したイメージの追加

### <a name="prerequisites"></a>前提条件 

[開発キット](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)から、または [VPN 経由で接続](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)している場合は Windows ベースの外部クライアントから、次の前提条件を実行します。

1. [Azure Stack と互換性のある Azure PowerShell モジュール](azure-stack-powershell-install.md)をインストールします。  

2. [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。  

3. Windows Server 評価版のページで、[Windows Server 2016 評価版をダウンロード](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016)します。 メッセージが表示されたら、ダウンロードの ISO バージョンを選択します。 ダウンロード先のパスは記録しておいてください。この記事の後続の手順で使用します。 この手順では、インターネット接続が必要です。  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>Azure Stack Marketplace にイメージを追加する
   
1. 次のコマンドを使用して、Azure Stack Connect および ComputeAdmin モジュールをインポートします。

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Azure Stack 環境にサインインします。 Azure Stack 環境のデプロイに Azure Active Directory (Azure AD) を使用したか、Active Directory フェデレーション サービス (AD FS) を使用したかに応じて、以下のいずれかのスクリプトを実行してください。 Azure AD の `tenantName`、`GraphAudience` エンドポイント、`ArmEndpoint` の値は、実際の環境の構成に合わせて置き換えてください。  

   * **Azure Active Directory**。 次のコマンドレットを使用します。

    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"
    
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```

   * **Active Directory フェデレーション サービス (AD FS)**。 次のコマンドレットを使用します。
    
    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"

    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience `
      -EnableAdfsAuthentication:$true

    $TenantID = Get-AzsDirectoryTenantId `
     -ADFS `
     -EnvironmentName "AzureStackAdmin" 

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```
   
3. Windows Server 2016 イメージを Azure Stack Marketplace に追加します。 *fully_qualified_path_to_ISO* は、ダウンロードした Windows Server 2016 ISO のパスに置き換えてください。

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

Windows Server 2016 VM イメージに最新の累積的更新プログラムを確実に適用するには、`New-AzsServer2016VMImage` コマンドレットの実行時に `IncludeLatestCU` パラメーターを含めます。 `New-AzsServer2016VMImage` コマンドレットで許可されるパラメーターの詳細については、「[パラメーター](#parameters)」を参照してください。 イメージを Azure Stack Marketplace に公開するには、1 時間ほどかかります。 

## <a name="parameters-for-new-azsserver2016vmimage"></a>New-AzsServer2016VMImage のパラメーター

### <a name="new-azsserver2016vmimage"></a>New-AzsServer2016VMImage 

新しい Server 2016 Core または完全なイメージを作成してアップロードし、そのイメージ用の Marketplace 項目を作成します。

| parameters | 必須 | 例 | [説明] |
|-----|-----|------|---- |
|ISOPath|[はい]| N:\ISO\en_windows_16_x64_dvd | ダウンロードした Windows Server 2016 ISO の完全修飾パス。|
|Net35|いいえ | True | Windows Server 2016 イメージに .NET 3.5 ランタイムをインストールします。 既定では、この値は **true** に設定されます。|
|バージョン|いいえ | 完全 |  **Core**、**Full**、**Both** のいずれかの Windows Server 2016 イメージを指定します。 既定では、この値は **Full** に設定されます。|
|VHDSizeInMB|いいえ | 40,960 | Azure Stack 環境に追加する VHD イメージのサイズ (MB 単位) を設定します。 既定では、この値は 40,960 MB に設定されます。|
|CreateGalleryItem|いいえ | True | Windows Server 2016 イメージの Marketplace 項目を作成するかどうかを指定します。 既定では、この値は **true** に設定されます。|
|location |いいえ  | D:\ | Windows Server 2016 イメージを公開する場所を指定します。|
|IncludeLatestCU|いいえ | False | 最新の Windows Server 2016 累積的更新プログラムを新しい VHD に適用します。 スクリプトで最新の更新プログラムが指定されていることを確認するか、次の 2 つのオプションのいずれかを使用してください。 |
|CUUri |いいえ  | https://yourupdateserver/winservupdate2016 | Windows Server 2016 累積的更新プログラムを特定の URI から実行するように設定します。 |
|CUPath |いいえ  | C:\winservupdate2016 | Windows Server 2016 累積的更新プログラムをローカル パスから実行するように設定します。 このオプションは、接続が切断された環境で Azure Stack インスタンスをデプロイしている場合に便利です。|

## <a name="next-steps"></a>次の手順

[仮想マシンのプロビジョニング](azure-stack-provision-vm.md)
