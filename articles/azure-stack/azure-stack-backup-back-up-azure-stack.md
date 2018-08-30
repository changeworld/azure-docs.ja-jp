---
title: Azure Stack のバックアップ | Microsoft Docs
description: Azure Stack でバックアップを設定してオンデマンド バックアップを実行します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 578bb864f56b788db77d1201533e73d3b9616669
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "41946589"
---
# <a name="back-up-azure-stack"></a>Azure Stack のバックアップ

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack でバックアップを設定してオンデマンド バックアップを実行します。 PowerShell 環境の構成方法については、「[PowerShell for Azure Stack をインストールする](azure-stack-powershell-install.md)」をご覧ください。 Azure Stack にサインインするには、「[Azure Stack の管理者ポータルの使用](azure-stack-manage-portals.md)」をご覧ください。

## <a name="start-azure-stack-backup"></a>Azure Stack のバックアップを開始する

### <a name="start-a-new-backup-without-job-progress-tracking"></a>ジョブの進行状況を追跡せずに新しいバックアップを開始する
Start-AzSBackup を使用して、ジョブの進行状況を追跡せずに新しいバックアップを迅速に開始します。

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>ジョブの進行状況を追跡して Azure Stack のバックアップを開始する
-AsJob 変数を指定して Start-AzSBackup を使用し、新しいバックアップを開始してバックアップ ジョブの進捗状況を追跡記録します。

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

## <a name="confirm-backup-has-completed"></a>バックアップの完了を確認する

### <a name="confirm-backup-has-completed-using-powershell"></a>PowerShell を使用してバックアップが完了したことを確認する
次の PowerShell コマンドを使用して、バックアップが正常に完了したことを確認します。

```powershell
   Get-AzsBackup
```

結果は次の出力のようになります。

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>管理ポータルでバックアップの完了を確認する
Azure Stack 管理ポータルを使用して、以下の手順でバックアップが正常に完了したことを確認します。

1. [Azure Stack 管理者ポータル](azure-stack-manage-portals.md)を開きます。
2. **[その他のサービス]** > **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** の順に選択します。 **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** ブレードで **[構成]** を選択します。
3. **[利用可能なバックアップ]** リストから、バックアップの **[名前]** と **[完了日]** を見つけます。
4. **[状態]** が **[成功]** であることを確認します。

## <a name="next-steps"></a>次の手順

データ損失イベントから復旧するためのワークフローについて学びます。 「[致命的なデータの損失からの復旧](azure-stack-backup-recover-data.md)」を参照してください。
