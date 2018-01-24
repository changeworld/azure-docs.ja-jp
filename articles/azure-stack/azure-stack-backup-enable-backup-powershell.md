---
title: "PowerShell で Azure Stack のバックアップを有効にする | Microsoft Docs"
description: "Windows PowerShell でインフラストラクチャ バック サービスを有効にし、障害が発生した場合に Azure Stack を復元できるようにします。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: e0be5f1916ddb653550e6428201356290560c00e
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>PowerShell で Azure Stack のバックアップを有効にする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Windows PowerShell でインフラストラクチャ バック サービスを有効にし、障害が発生した場合に Azure Stack を復元できるようにします。 PowerShell コマンドレットにアクセスして、オペレーター管理エンドポイント経由でバックアップを有効にし、バックアップを開始し、バックアップ情報を取得できます。

## <a name="download-azure-stack-tools"></a>Azure Stack ツールをダウンロードする

Azure Stack の PowerShell と Azure Stack ツールをインストールして構成します。 「[Get up and running with PowerShell in Azure Stack (Azure Stack での PowerShell の稼働)](https://review.docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart)」をご覧ください。

##  <a name="load-the-connect-and-infrastructure-modules"></a>接続モジュールとインフラストラクチャ モジュールを読み込む

管理者特権のプロンプトで Windows PowerShell を開き、次のコマンドを実行します。

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>Rm 環境を設定し、オペレーター管理エンドポイントにログインする

同じ PowerShell セッションで、環境変数を追加して次の PowerShell スクリプトを編集します。 更新されたスクリプトを実行して RM 環境を設定し、オペレーター管理エンドポイントにログインします。

| 変数    | [説明] |
|---          |---          |
| $TenantName | Azure Active Directory テナント名。 |
| オペレーター アカウント名        | Azure Stack オペレーター アカウント名。 |
| Azure Resource Manager エンドポイント | Azure Resource Manager の URL。 |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>新しい暗号化キーを生成する

同じ PowerShell セッションで、次のコマンドを実行します。

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>バックアップを有効にするためのバックアップ共有、認証情報、暗号化キーを提供する

同じ PowerShell セッションで、環境変数を追加して次の PowerShell スクリプトを編集します。 更新されたスクリプトを実行して、インフラストラクチャ バックアップ サービスにバックアップ共有、資格情報、および暗号化キーを提供します。

| 変数        | [説明]   |
|---              |---                                        |
| $username       | 共有ドライブの場所のドメインとユーザー名を使用して**ユーザー名**を入力します。 たとえば、「`Contoso\administrator`」のように入力します。 |
| $password       | ユーザーの**パスワード**を入力します。 |
| $sharepath      | **バックアップ ストレージの場所**のパスを入力します。 別のデバイスでホストされるファイル共有のパスの場合、汎用名前付け規則 (UNC) の文字列を使用する必要があります。 UNC 文字列は、共有ファイルやデバイスなどのリソースの場所を指定します。 バックアップ データを確実に利用できるようにするためには、保存デバイスは別の場所に配置する必要があります。 |

   ```powershell
   $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

Set-AzSBackupShare -Location $location -Path $path -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey 

   ```
##  <a name="confirm-backup-settings"></a>バックアップ設定を確認する

同じ PowerShell セッションで、次のコマンドを実行します。

   ```powershell
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
   ```

結果は次の JSON 出力のようになります。

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>次の手順

 - 「[Back up Azure Stack](azure-stack-backup-back-up-azure-stack.md )」(Azure Stack のバックアップ) でバックアップの実行方法を学びます。  
- 「[Confirm backup completed in administration portal](azure-stack-backup-back-up-azure-stack.md )」(管理ポータルでのバックアップ完了の確認) でバックアップが実行されたことを確認する方法について学びます。