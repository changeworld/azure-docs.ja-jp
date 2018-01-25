---
title: "Azure Stack のバックアップ | Microsoft Docs"
description: "Azure Stack でバックアップを設定してオンデマンド バックアップを実行します。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 955b286967ca2bc8450e8988ec16c6a5c352aa8a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="back-up-azure-stack"></a>Azure Stack のバックアップ

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack でバックアップを設定してオンデマンド バックアップを実行します。 インフラストラクチャ バックアップ サービスを有効にする場合は、「[Enable Backup for Azure Stack from the administration portal](azure-stack-backup-enable-backup-console.md).」(管理ポータルから Azure Stack を有効にする) を参照してください。

> [!Note]  
>  Azure Stack ツールには **Start-AzSBackup** コマンドレットが含まれます。 ツールをインストールする手順については、「[Azure Stack での PowerShell を使用した準備と実行](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart)」を参照してください。

## <a name="start-azure-stack-backup"></a>Azure Stack のバックアップを開始する

オペレーター管理環境の管理者特権のプロンプトで Windows PowerShell を開き、次のコマンドを実行します。

```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1

    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>管理ポータルでバックアップの完了を確認する

1. Azure Stack 管理ポータル ([https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)) を開きます。
2. **[その他のサービス]** > **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** の順に選択します。 **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** ブレードで **[構成]** を選択します。
3. **[利用可能なバックアップ]** リストから、バックアップの **[名前]** と **[完了日]** を見つけます。
4. **[状態]** が **[成功]** であることを確認します。

また、管理ポータルからバックアップが完了したことを確認することもできます。 `\MASBackup\<datetime>\<backupid>\BackupInfo.xml` に移動します

## <a name="next-steps"></a>次の手順

- データ損失イベントから復旧するためのワークフローについて学びます。 「[致命的なデータの損失からの復旧](azure-stack-backup-recover-data.md)」を参照してください。
