---
title: PowerShell で Azure Stack のバックアップを有効にする | Microsoft Docs
description: Windows PowerShell でインフラストラクチャ バックアップ サービスを有効にし、障害が発生した場合に Azure Stack を復元できるようにします。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 4faa6930c37f9d491a3efa4b34519dbb13761a9d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074934"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>PowerShell で Azure Stack のバックアップを有効にする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Windows PowerShell で Infrastructure Backup サービスを有効にして、次のものを定期的にバックアップします。
 - 内部 ID サービスとルート証明書
 - ユーザー プラン、オファー、サブスクリプション
 - KeyVault シークレット
 - ユーザー RBAC ロールとポリシー

PowerShell コマンドレットにアクセスして、オペレーター管理エンドポイント経由でバックアップを有効にし、バックアップを開始し、バックアップ情報を取得できます。

## <a name="prepare-powershell-environment"></a>PowerShell 環境を準備する

PowerShell 環境の構成方法については、「[PowerShell for Azure Stack をインストールする](azure-stack-powershell-install.md)」をご覧ください。

## <a name="generate-a-new-encryption-key"></a>新しい暗号化キーを生成する

Azure Stack の PowerShell と Azure Stack ツールをインストールして構成します。
 - 「[Get up and running with PowerShell in Azure Stack (Azure Stack での PowerShell の稼働)](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart)」をご覧ください。
 - 「[GitHub からの Azure Stack ツールのダウンロード](azure-stack-powershell-download.md)」をご覧ください

管理者特権のプロンプトで Windows PowerShell を開き、次のコマンドを実行します。
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
同じ PowerShell セッションで、次のコマンドを実行します。

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> AzureStack-Tools を使用してキーを生成する必要があります。

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>バックアップを有効にするためのバックアップ共有、認証情報、暗号化キーを提供する

同じ PowerShell セッションで、環境変数を追加して次の PowerShell スクリプトを編集します。 更新されたスクリプトを実行して、インフラストラクチャ バックアップ サービスにバックアップ共有、資格情報、および暗号化キーを提供します。

| 変数        | [説明]   |
|---              |---                                        |
| $username       | 共有ドライブの場所のドメインとユーザー名を使用して**ユーザー名**を入力します。 たとえば、「`Contoso\administrator`」のように入力します。 |
| $password       | ユーザーの**パスワード**を入力します。 |
| $sharepath      | **バックアップ ストレージの場所**のパスを入力します。 別のデバイスでホストされるファイル共有へのパスの場合、汎用名前付け規則 (UNC) の文字列を使用する必要があります。 UNC 文字列は、共有ファイルやデバイスといった、リソースの場所を指定します。 バックアップ データを確実に利用できるようにするためには、保存デバイスは別の場所に配置する必要があります。 |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>バックアップ設定を確認する

同じ PowerShell セッションで、次のコマンドを実行します。

   ```powershell
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
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