---
title: ローカル ソースから Azure Stack マーケットプレース項目を追加する | Microsoft Docs
description: ローカル オペレーティング システム イメージを Azure Stack Marketplace に追加する方法について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 815dc055e19a38a61cbb3e927c3d7e92393b17dc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>チュートリアル: ローカル ソースから Azure Stack マーケットプレース項目を追加する

Azure Stack オペレーターとして、ユーザーが仮想マシン (VM) をデプロイできるようにするために最初に行うことは、VM イメージを Azure Stack マーケットプレースに追加することです。 既定では、Azure Stack マーケットプレースに何も発行されませんが、VM ISO イメージをアップロードしてユーザーに提供することができます。 接続が切断されたシナリオまたは接続が制限されたシナリオで Azure Stack をデプロイした場合は、このオプションを使用します。

このチュートリアルでは、Windows Server 評価版のページから Windows Server 2016 VM イメージをダウンロードし、Azure Stack マーケットプレースにアップロードします。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Windows Server 2016 VM イメージを追加する
> * VM イメージが使用可能であることを確認する 
> * VM イメージをテストする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [Azure Stack と互換性のある Azure PowerShell モジュール](asdk-post-deploy.md#install-azure-stack-powershell)をインストールする
- [Azure Stack ツール](asdk-post-deploy.md#download-the-azure-stack-tools)をダウンロードする
- Windows Server 評価版のページから [Windows Server 2106 仮想マシン ISO イメージ](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016)をダウンロードする

## <a name="add-a-windows-server-vm-image"></a>Windows Server VM イメージを追加する
PowerShell を使用して、以前にダウンロードした ISO イメージを追加することによって、Azure Stack マーケットプレースに Windows Server 2016 イメージを発行することができます。 

接続が切断されたシナリオまたは接続が制限されたシナリオで Azure Stack をデプロイした場合は、このオプションを使用します。

1. 次のコマンドを使用して、Azure Stack ツール ディレクトリに含まれている Azure Stack `Connect` および `ComputeAdmin` PowerShell モジュールをインポートします。

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Azure Stack 環境のデプロイに Azure Active Directory (Azure AD) を使用したか、Active Directory フェデレーション サービス (AD FS) を使用したかに応じて、以下のいずれかのスクリプトを ASDK ホスト コンピューターに対して実行してください。

  - **Azure AD デプロイ**の場合のコマンド: 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Connect-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - **AD FS デプロイ**の場合のコマンド:
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

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

      Connect-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. Windows Server 2016 イメージを Azure Stack マーケットプレースに追加します。 *fully_qualified_path_to_ISO* は、ダウンロードした Windows Server 2016 ISO のパスに置き換えてください。

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

## <a name="verify-the-marketplace-item-is-available"></a>マーケットプレース項目が利用可能であることを確認する
以下の手順を使用して、新しい VM イメージが Azure Stack マーケットプレースで利用可能であることを確認します。

1. [ASDK 管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. **[その他のサービス]** > **[Marketplace Management]\(Marketplace の管理\)** の順に選択します。 

3. Windows Server 2016 Datacenter VM イメージが正常に追加されていることを確認します。

   ![Azure からダウンロードされたイメージ](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>VM イメージをテストする
Azure Stack オペレーターとして、[管理者ポータル](https://adminportal.local.azurestack.external)を使用してテスト VM を作成し、イメージが正常に利用可能になったことを検証できます。 

1. [ASDK 管理ポータル](https://adminportal.local.azurestack.external)にサインインします。

2. **[新規]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter]** > **[作成]** の順にクリックします。  
 ![マーケットプレース イメージから VM を作成する](media/asdk-marketplace-item/new-compute.png)

3. **[基本]** ブレードで次の情報を入力し、**[OK]** をクリックします。
  - **名前**: test-vm-1
  - **ユーザー名**: AdminTestUser
  - **パスワード**: AzS-TestVM01
  - **サブスクリプション**: 既定のプロバイダー サブスクリプションを使用します
  - **リソース グループ**: test-vm-rg
  - **場所**: ローカル

4. **[サイズの選択]** ブレードで、**[A1 標準]** をクリックしてから **[選択]** をクリックします。  

5. **[設定]** ブレードで、既定値のまま **[OK]** をクリックします。

6. **[概要]** ブレードで、**[OK]** をクリックして仮想マシンを作成します。  
> [!NOTE]
> 仮想マシンのデプロイが完了するまでに数分かかる場合があります。

7. 新しい VM を表示するには、**[仮想マシン]** をクリックし、**[test-vm-1]** を検索して、その名前をクリックします。
    ![表示される最初のテスト VM イメージ](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
VM に正常にサインインし、テスト イメージが正常に動作していることを確認した後で、テスト リソース グループを削除する必要があります。 そうすることで、単一ノード ASDK のインストールに利用できる、限られたリソースが解放されます。

不要になったら、次の手順に従って、リソース グループ、VM、および関連するすべてのリソースを削除できます。

1. [ASDK 管理ポータル](https://adminportal.local.azurestack.external)にサインインします。
2. **[リソース グループ]** > **[test-vm-rg]** > **[リソース グループの削除]** の順にクリックします。
3. リソース グループ名として「**test-vm-rg**」と入力し、**[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Windows Server 2016 VM イメージを追加する
> * VM イメージが使用可能であることを確認する 
> * VM イメージをテストする

次のチュートリアルに進み、Azure Stack のオファーとプランを作成する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Stack の IaaS サービスをオファーする](asdk-offer-services.md)
