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
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 08335f676437a32aa2240298be4680633eff16ba
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432972"
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
Start-AzSBackup を使用して、**-AsJob** パラメーターを指定して新しいバックアップを開始し、バックアップ ジョブの進捗状況を追跡する変数として保存します。

> [!NOTE]
> バックアップ ジョブは、ジョブが完了する約 10 から 15 分前に、正常に完了したことがポータルに表示されます。
>
> そのため、実際の状態は次のコードを使用するとより適切に監視できます。

> [!IMPORTANT]
> 最初に 1 ミリ秒の遅延を入れたのは、コードがジョブを正しく登録するには早すぎて、**PSBeginTime** なしで戻り、次にジョブの **State** なしで戻ることへの対処としてです。

```powershell
    $BackupJob = Start-AzsBackup -Force -AsJob
    While (!$BackupJob.PSBeginTime) {
        Start-Sleep -Milliseconds 1
    }
    Write-Host "Start time: $($BackupJob.PSBeginTime)"
    While ($BackupJob.State -eq "Running") {
        Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
        Start-Sleep -Seconds 30
    }

    If ($BackupJob.State -eq "Completed") {
        Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
        $Duration = $BackupJob.Output.TimeTakenToCreate
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
        If ($Duration -match $Pattern) {
            If (!$Matches.ContainsKey("Hours")) {
                $Hours = ""
            } 
            Else {
                $Hours = ($Matches.Hours).ToString + 'h '
            }
            $Minutes = ($Matches.Minutes)
            $Seconds = [math]::round(($Matches.Seconds))
            $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
        }
        Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
    }
    ElseIf ($BackupJob.State -ne "Completed") {
        $BackupJob
        $BackupJob.Output
    }
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
2. **[すべてのサービス]** を選択し、**[管理]** カテゴリで **[Infrastructure backup]** を選択します。 **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** ブレードで **[構成]** を選択します。
3. **[利用可能なバックアップ]** リストから、バックアップの **[名前]** と **[完了日]** を見つけます。
4. **[状態]** が **[成功]** であることを確認します。

## <a name="next-steps"></a>次の手順

データ損失イベントから復旧するためのワークフローについて学びます。 「[致命的なデータの損失からの復旧](azure-stack-backup-recover-data.md)」を参照してください。
