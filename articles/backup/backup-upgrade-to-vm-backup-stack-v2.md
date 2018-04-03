---
title: Azure VM バックアップ スタック V2 にアップグレードする | Microsoft Docs
description: VM バックアップ スタック V2 のアップグレード プロセスとよく寄せられる質問です
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 6d214072bccb8b2b42828ee003dcf349985b4f43
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>VM バックアップ スタック V2 へのアップグレード
仮想マシン (VM) バックアップ スタック V2 にアップグレードすると、次の機能強化が提供されます。
* データ転送の完了を待たずに復旧に利用できるように、バックアップ ジョブの一環として取得されるスナップショットを表示できます。
復元を開始する前に、スナップショットがコンテナーにコピーされるのを待つ時間が短縮されます。 また、最初のバックアップを除き、Premium VM をバックアップするための追加ストレージ要件がなくなります。  

* スナップショットを 7 日間ローカルに保持することで、バックアップと復元の時間が短縮されます。 

* 最大 4 TB のディスク サイズをサポートします。  

* 非管理対象 VM の復元を行うときに、(VM に複数のストレージ アカウントに分散されたディスクがある場合でも) 元のストレージ アカウントを使うことができます。 これにより、多様な VM 構成で復元が速くなります。 
    > [!NOTE] 
    > これは、元の VM のオーバーライドと同じではありません。 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>新しいスタックでの変更点
現在、バックアップ ジョブは 2 つのフェーズで構成されます。
1.  VM スナップショットを作成します。 
2.  Azure Backup コンテナーに VM スナップショットを転送します。 

復旧ポイントは、フェーズ 1 および 2 が完了した後でのみ、作成されたものと見なされます。 新しいスタックでは、復旧ポイントはスナップショットが完了するとすぐに作成されます。 同じ復元フローを使ってこの復旧ポイントから復元することもできます。 Azure Portal では、復旧ポイントの種類として "スナップショット" を使って、この復旧ポイントを識別できます。 コンテナーにスナップショットが転送されると、復旧ポイントの種類は "スナップショットとコンテナー" に変わります。 

![VM バックアップ スタック V2 でのバックアップ ジョブ](./media/backup-azure-vms/instant-rp-flow.jpg) 

既定では、スナップショットは 7 日間保持されます。 これにより、顧客のストレージ アカウントにコンテナーからデータをコピーして戻すために必要な時間が短縮されるので、スナップショットからの復元は速く完了します。 

## <a name="considerations-before-upgrade"></a>アップグレードの前の考慮事項
* これは、VM バックアップ スタックの一方向のアップグレードです。 そのため、将来のバックアップはすべて、このフローになります。 **これはサブスクリプション レベルで有効にされるので、すべての VM がこのフローになります**。 新しい機能の追加はすべて、同じスタックに基づきます。 ポリシー レベルでこれを制御する機能は、将来のリリースで予定されています。 
* Premium ディスクを使う VM では、最初のバックアップの間に、最初のバックアップが完了するまで、VM のサイズと等しい記憶域スペースがストレージ アカウントで利用できることを確認します。 
* 復旧ポイントの作成と復元を高速化するため、スナップショットはローカルに格納されるので、7 日の期間中、スナップショットに対応するストレージ コストが発生します。
* Premium VM のスナップショット復旧ポイントから復元を行う場合、復元の一部として VM が作成されている間、一時的な保存場所が使われます。 

## <a name="how-to-upgrade"></a>アップグレードする方法
### <a name="the-azure-portal"></a>Azure ポータル
Azure Portal を使っている場合、大容量ディスクのサポートおよびバックアップと復元の速度向上に関する通知が、コンテナーのダッシュボードに表示されます。

![VM バックアップ スタック V2 でのバックアップ ジョブ](./media/backup-azure-vms/instant-rp-banner.png) 

新しいスタックにアップグレードするための画面を開くには、バナーを選びます。 

![VM バックアップ スタック V2 でのバックアップ ジョブ](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
管理者特権の PowerShell ターミナルから次のコマンドレットを実行します。
1.  Azure アカウントにサインインします。 

```
PS C:> Login-AzureRmAccount
```

2.  プレビュー用に登録するサブスクリプションを選びます。

```
PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
```

3.  プライベート プレビュー用にこのサブスクリプションを登録します。

```
PS C:>  Register-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

## <a name="verify-whether-the-upgrade-is-complete"></a>アップグレードが完了したかどうかを確認します。
管理者特権の PowerShell ターミナルから、次のコマンドレットを実行します。

```
Get-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

登録済みと表示されたら、サブスクリプションは VM バックアップ スタック V2 にアップグレードされています。 



