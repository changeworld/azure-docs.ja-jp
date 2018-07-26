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
ms.openlocfilehash: 76a24e7096cbc2a9bcea8bf68e2b333345dbff68
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242956"
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

PowerShell 環境の構成方法については、「[PowerShell for Azure Stack をインストールする](azure-stack-powershell-install.md)」をご覧ください。 Azure Stack にサインインする場合は、「[オペレーター環境の構成と Azure Stack へのサインイン](azure-stack-powershell-configure-admin.md)」を参照してください。

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>バックアップを有効にするためのバックアップ共有、認証情報、暗号化キーを提供する

同じ PowerShell セッションで、環境変数を追加して次の PowerShell スクリプトを編集します。 更新されたスクリプトを実行して、インフラストラクチャ バックアップ サービスにバックアップ共有、資格情報、および暗号化キーを提供します。

| 可変        | 説明   |
|---              |---                                        |
| $username       | ファイルを読み書きするための十分なアクセス権がある共有ドライブの場所のドメインとユーザー名を使用して**ユーザー名**を入力します。 たとえば、「`Contoso\backupshareuser`」のように入力します。 |
| $key            | 各バックアップの暗号化に使用される**暗号化キー**を入力します。 |
| $password       | ユーザーの**パスワード**を入力します。 |
| $sharepath      | **バックアップ ストレージの場所**のパスを入力します。 別のデバイスでホストされるファイル共有へのパスの場合、汎用名前付け規則 (UNC) の文字列を使用する必要があります。 UNC 文字列は、共有ファイルやデバイスといった、リソースの場所を指定します。 バックアップ データを確実に利用できるようにするためには、保存デバイスは別の場所に配置する必要があります。 |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>バックアップ設定を確認する

同じ PowerShell セッションで、次のコマンドを実行します。

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

結果は次の出力のようになります。

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>次の手順

 - 「[Back up Azure Stack](azure-stack-backup-back-up-azure-stack.md )」(Azure Stack のバックアップ) でバックアップの実行方法を学びます。  
 - 「[Confirm backup completed in administration portal](azure-stack-backup-back-up-azure-stack.md )」(管理ポータルでのバックアップ完了の確認) でバックアップが実行されたことを確認する方法について学びます。