---
title: Azure Stack のバックアップ | Microsoft Docs
description: Azure Stack でバックアップを設定してオンデマンド バックアップを実行します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c9e7ffae1b988d0940d10acdb1b387a25e0466ec
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242888"
---
# <a name="back-up-azure-stack"></a>Azure Stack のバックアップ

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack でバックアップを設定してオンデマンド バックアップを実行します。 PowerShell 環境の構成方法については、「[PowerShell for Azure Stack をインストールする](azure-stack-powershell-install.md)」をご覧ください。 Azure Stack にサインインする場合は、「[オペレーター環境の構成と Azure Stack へのサインイン](azure-stack-powershell-configure-admin.md)」を参照してください。

## <a name="start-azure-stack-backup"></a>Azure Stack のバックアップを開始する

Start-AzSBackup を使用し、-AsJob 変数で新しいバックアップを開始して進捗状況を追跡記録します。 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>PowerShell でバックアップの完了を確認する

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- 結果は次の出力のようになります。

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

## <a name="confirm-backup-completed-in-the-administration-portal"></a>管理ポータルでバックアップの完了を確認する

1. Azure Stack 管理者ポータル ([https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)) を開きます。
2. **[その他のサービス]** > **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** の順に選択します。 **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** ブレードで **[構成]** を選択します。
3. **[利用可能なバックアップ]** リストから、バックアップの **[名前]** と **[完了日]** を見つけます。
4. **[状態]** が **[成功]** であることを確認します。

## <a name="next-steps"></a>次の手順

- データ損失イベントから復旧するためのワークフローについて学びます。 「[致命的なデータの損失からの復旧](azure-stack-backup-recover-data.md)」を参照してください。