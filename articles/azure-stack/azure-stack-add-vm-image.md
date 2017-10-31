---
title: "VM イメージを Azure Stack に追加する | Microsoft Docs"
description: "テナントが使用するために組織のカスタム イメージ (Windows または Linux VM) を追加する"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 520e4dfaadf1d476447a600ef2b3d092b6955a89
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2017
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Azure Stack でカスタム仮想マシン イメージを提供する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack では、オペレーターがカスタム仮想マシン イメージをユーザーに提供できます。 それらのイメージを Azure Resource Manager テンプレートで参照したり、Marketplace 項目として Azure Marketplace UI に追加したりすることができます。 

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>PowerShell を使用して VM イメージを Marketplace に追加する

[開発キット](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)から、または [VPN 経由で接続](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)している場合は Windows ベースの外部クライアントから、次の前提条件を実行します。

1. [PowerShell for Azure Stack をインストールします](azure-stack-powershell-install.md)。  

2. [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。  

3. Windows または Linux オペレーティング システム仮想ハード ディスク イメージを (VHDX 形式ではなく) VHD 形式で準備します。
   
   * Windows イメージに関して、その準備に伴う手順については、[Resource Manager デプロイのために Windows VM イメージを Azure にアップロードする方法](../virtual-machines/windows/upload-generalized-managed.md)に関するページを参照してください。
   * Linux イメージについては、[Azure Stack への Linux 仮想マシンのデプロイ](azure-stack-linux.md)に関するページを参照してください。 記事に説明されている手順に従って、イメージを準備するか既存の Azure Stack Linux イメージを使用します。  

Azure Stack Marketplace にイメージを追加するには、以下の手順を実行します。

1. Connect モジュールと ComputeAdmin モジュールをインポートします。
   
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
          -ADFS 
          -EnvironmentName AzureStackAdmin 

        Login-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID 
        ```
    
3. `Add-AzsVMImage` コマンドレットを呼び出して、VM イメージを追加します。 `Add-AzsVMImage` コマンドレットには、`osType` として Windows または Linux を指定します。 VM イメージについて、publisher、offer、SKU、version も指定してください。 指定できるパラメーターについては、「[パラメーター](#parameters)」を参照してください。 これらのパラメーターは、Azure Resource Manager テンプレートが VM イメージを参照するために使用されます。 スクリプトを呼び出す例を次に示します。
     
  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```

このコマンドは、次の処理を実行します。

* Azure Stack 環境に対する認証。
* ローカル VHD を新規作成された一時ストレージ アカウントにアップロード。
* VM イメージを VM イメージ リポジトリに追加。
* Marketplace 項目の作成。

コマンドが正常に実行されたことを確認するには、ポータルから Marketplace に移動します。 **[Virtual Machines]** カテゴリで VM イメージが利用できることを確認します。

![VM イメージが正常に追加](./media/azure-stack-add-vm-image/image5.PNG) 

## <a name="remove-a-vm-image-by-using-powershell"></a>PowerShell を使用して VM イメージを削除する

過去にアップロードした仮想マシン イメージが必要なくなった場合は、次のコマンドレットを使用して Marketplace から削除できます。

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>パラメーター

| パラメーター | Description |
| --- | --- |
| **publisher** |イメージをデプロイするときにユーザーが使用する VM イメージの発行元名のセグメント。 たとえば、**Microsoft** です。 このフィールドではスペースや他の特殊文字は使用できません。 |
| **offer** |VM イメージをデプロイするときにユーザーが使用する VM イメージのプラン名のセグメント。 たとえば、**WindowsServer** です。 このフィールドではスペースや他の特殊文字は使用できません。 |
| **sku** |VM イメージをデプロイするときにユーザーが使用する VM イメージの SKU 名のセグメント。 たとえば、**Datacenter2016** です。 このフィールドではスペースや他の特殊文字は使用できません。 |
| **version** |VM イメージをデプロイするときにユーザーが使用する VM イメージのバージョン。 このバージョンの形式は *\#.\#.\#* です。 たとえば、**1.0.0** です。 このフィールドではスペースや他の特殊文字は使用できません。 |
| **osType** |イメージの osType には **Windows** または **Linux** を指定する必要があります。 |
| **osDiskLocalPath** |VM イメージとして Azure Stack にアップロードする OS ディスク VHD のローカル パス。 |
| **dataDiskLocalPaths** |VM イメージの一部としてアップロードできるデータ ディスクのローカル パスの配列 (省略可能)。 |
| **CreateGalleryItem** |Marketplace に項目を作成するかどうかを決定するブール値フラグ。 既定値は **true** です。 |
| **title** |Marketplace 項目の表示名。 既定では、VM イメージの `Publisher-Offer-Sku` 値に設定されています。 |
| **description** |Marketplace 項目の説明。 |
| **location** |VM イメージの発行先の場所。 既定では、この値は **local** に設定されます。|
| **osDiskBlobURI** |(省略可能) このスクリプトでは `osDisk` の Blob Storage URI を処理できます。 |
| **dataDiskBlobURIs** |(省略可能) このスクリプトでは、データ ディスクをイメージに追加するために Blob Storage URI の配列も処理できます。 |

## <a name="add-a-vm-image-through-the-portal"></a>ポータルから VM イメージを追加する

> [!NOTE]
> この方法では、Marketplace 項目を個別に作成する必要があります。

イメージは、Blob Storage URI で参照できることが必要です。 Windows または Linux オペレーティング システムのイメージを VHD 形式 (VHDX ではない) で準備して、そのイメージを Azure のストレージ アカウントまたは Azure Stack にアップロードします。 イメージが既に Azure の Blob Storage または Azure Stack にアップロードされている場合、手順 1. をスキップできます。

1. [Resource Manager のデプロイのために Windows VM イメージを Azure にアップロードする](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)か、Linux イメージの場合は、「[Deploy Linux virtual machines on Azure Stack](azure-stack-linux.md)」(Azure Stack への Linux 仮想マシンのデプロイ) の説明に従います。 イメージをアップロードする前に、次の事実を考慮する必要があります。

   * Azure BLOB Storage よりも Azure Stack BLOB Storage の方がイメージを効率よくアップロードできます。イメージを Azure Stack イメージ リポジトリにプッシュする方が時間がかからないためです。 
   
   * [Windows VM イメージ](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)をアップロードするときは、**Azure へのログイン**の手順の代わりに [Azure Stack オペレーターの PowerShell 環境を構成](azure-stack-powershell-configure-admin.md)する手順を実行してください。  

   * イメージのアップロード先の Blob Storage URI をメモしておきます。 Blob Storage URI の形式は *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd です

   * BLOB に匿名でアクセスできるようにするには、VM イメージ VHD がアップロードされたストレージ アカウントの BLOB コンテナーに移動します。 **[BLOB]** を選択し、**[アクセス ポリシー]** を選択してください。 必要であれば、このコンテナーの Shared Access Signature を生成し、それを BLOB URI に含めることもできます。

   ![ストレージ アカウント BLOB に移動](./media/azure-stack-add-vm-image/image1.png)

   ![BLOB のアクセスを公開に設定](./media/azure-stack-add-vm-image/image2.png)

2. Azure Stack にオペレーターとしてサインインします。 メニューから **[その他のサービス]** > **[リソース プロバイダー]** を選択します。 次に、**[Compute]** > **[VM イメージ]** > **[追加]** を選択します。

3. **[Add a VM Image]\(VM イメージの追加\)** で、パブリッシャー、プラン、SKU、および仮想マシン イメージのバージョンを入力します。 これらの名前セグメントによって、Resource Manager テンプレートで VM イメージが参照されます。 正しい **osType** の値を選択してください。 **[OD Disk Blob URI]\(OD ディスク BLOB URI\)** には、イメージがアップロードされた BLOB URI を入力します。 その後 **[作成]** をクリックすると VM イメージの作成が開始されます。
   
   ![イメージの作成を開始](./media/azure-stack-add-vm-image/image4.png)

   イメージが正常に作成されると、VM イメージの状態が **[成功]** に変わります。

4. 仮想マシン イメージをユーザーが UI ですぐに使用できるようにするには、[Marketplace 項目の作成](azure-stack-create-and-publish-marketplace-item.md)が最適です。

## <a name="next-steps"></a>次のステップ

[仮想マシンのプロビジョニング](azure-stack-provision-vm.md)