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
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075189"
---
# <a name="back-up-azure-stack"></a>Azure Stack のバックアップ

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack でバックアップを設定してオンデマンド バックアップを実行します。 インフラストラクチャ バックアップ サービスを有効にする場合は、「[Enable Backup for Azure Stack from the administration portal](azure-stack-backup-enable-backup-console.md).」(管理ポータルから Azure Stack を有効にする) を参照してください。

> [!Note]  
>  PowerShell 環境の構成方法については、「[PowerShell for Azure Stack をインストールする](azure-stack-powershell-install.md)」をご覧ください。

## <a name="start-azure-stack-backup"></a>Azure Stack のバックアップを開始する

オペレーター管理環境の管理者特権のプロンプトで Windows PowerShell を開き、次のコマンドを実行します。

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>管理ポータルでバックアップの完了を確認する

1. Azure Stack 管理者ポータル ([https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)) を開きます。
2. **[その他のサービス]** > **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** の順に選択します。 **[Infrastructure backup]\(インフラストラクチャ バックアップ\)** ブレードで **[構成]** を選択します。
3. **[利用可能なバックアップ]** リストから、バックアップの **[名前]** と **[完了日]** を見つけます。
4. **[状態]** が **[成功]** であることを確認します。

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>次の手順

- データ損失イベントから復旧するためのワークフローについて学びます。 「[致命的なデータの損失からの復旧](azure-stack-backup-recover-data.md)」を参照してください。
