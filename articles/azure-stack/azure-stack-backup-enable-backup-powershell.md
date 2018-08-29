---
title: PowerShell で Azure Stack のバックアップを有効にする | Microsoft Docs
description: Windows PowerShell でインフラストラクチャ バックアップ サービスを有効にし、障害が発生した場合に Azure Stack を復元できるようにします。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 8fe7f0ddd630cfca0242af6cc1d728bdef163352
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41946402"
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
| $username       | ファイルを読み書きするための十分なアクセス権がある共有ドライブの場所のドメインとユーザー名を使用して**ユーザー名**を入力します。 たとえば、「 `Contoso\backupshareuser` 」のように入力します。 |
| $password       | ユーザーの**パスワード**を入力します。 |
| $sharepath      | **バックアップ ストレージの場所**のパスを入力します。 別のデバイスでホストされるファイル共有へのパスの場合、汎用名前付け規則 (UNC) の文字列を使用する必要があります。 UNC 文字列は、共有ファイルやデバイスといった、リソースの場所を指定します。 バックアップ データを確実に利用できるようにするためには、保存デバイスは別の場所に配置する必要があります。 |
| $frequencyInHours | 頻度 (時間単位) はバックアップの作成頻度を決定します。 既定値は 12 です。 Scheduler では、最大値 12 から最小値 4 までをサポートします。|
| $retentionPeriodInDays | 保有期間 (日単位) は、バックアップが外部の保存場所に保持される日数を決定します。 既定値は 7 です。 Scheduler では、最大値 14 から最小値 2 までをサポートします。 保有期間より前のバックアップは、外部の保存場所から自動的に削除されます。|
|     |     |

   ```powershell
    # Example username:
    $username = "domain\backupadmin"
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
   
    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    
    # The encryption key is generated using the New-AzsEncryptionKeyBase64 cmdlet provided in Azure Stack PowerShell.
    # Make sure to store your encryption key in a secure location after it is generated.
    $Encryptionkey = New-AzsEncryptionKeyBase64
    $key = ConvertTo-SecureString -String ($Encryptionkey) -AsPlainText -Force

    Set-AzsBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>バックアップ設定を確認する

同じ PowerShell セッションで、次のコマンドを実行します。

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName
   ```

結果は次の例が示す出力のようになります。

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>バックアップ設定の更新
同じ PowerShell セッションで、バックアップの保有期間と頻度の既定値を更新できます。 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupShare -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

結果は次の例が示す出力のようになります。

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

## <a name="next-steps"></a>次の手順

 - 「[Back up Azure Stack](azure-stack-backup-back-up-azure-stack.md )」(Azure Stack のバックアップ) でバックアップの実行方法を学びます。  
 - 「[Confirm backup completed in administration portal](azure-stack-backup-back-up-azure-stack.md )」(管理ポータルでのバックアップ完了の確認) でバックアップが実行されたことを確認する方法について学びます。
