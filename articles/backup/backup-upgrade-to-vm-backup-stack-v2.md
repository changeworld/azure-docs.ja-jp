---
title: Azure VM バックアップ スタック用 Azure Resource Manager デプロイメント モデルへのアップグレード
description: VM バックアップ スタック、Resource Manager デプロイメント モデルのアップグレード プロセスとよくある質問
services: backup, virtual-machines
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: trinadhk
ms.openlocfilehash: e822e0c354fd671ee2802506e0e268d4078b395e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606904"
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>Azure VM バックアップ スタック用 Azure Resource Manager デプロイメント モデルへのアップグレード
仮想マシン (VM) バックアップ スタックへのアップグレード用 Resource Manager デプロイメント モデルには、次の拡張機能が用意されています。
* データ転送の終了を待たずに復旧に利用できるバックアップ ジョブの一環として取得されるスナップショットを表示できます。 これにより、復元をトリガーする前にスナップショットをコンテナーにコピーする待機時間が短縮されます。 また、この機能により、最初のバックアップを除き、Premium VM をバックアップするための追加ストレージ要件がなくなります。  

* スナップショットを 7 日間ローカルに保持することで、バックアップと復元の時間が短縮されます。

* 最大 4 TB のディスク サイズをサポートします。

* 復元時に非管理対象 VM の元のストレージ アカウントを使用できます。 この機能は、ストレージ アカウント間に分散しているディスクが VM にある場合でも使用できます。 これにより、多様な VM 構成で復元が速くなります。
    > [!NOTE] 
    > この機能は、元の VM のオーバーライドと同じではありません。 
    >

## <a name="whats-changing-in-the-new-stack"></a>新しいスタックでの変更点
現在、バックアップ ジョブは 2 つのフェーズで構成されます。
1.  VM スナップショットを作成します。 
2.  Azure Backup コンテナーに VM スナップショットを転送します。 

復旧ポイントは、フェーズ 1 および 2 が完了した後でのみ、作成されたものと見なされます。 新しいスタックでは、復旧ポイントはスナップショットが終了するとすぐに作成されます。 同じ復元フローを使ってこの復旧ポイントから復元することもできます。 Azure Portal では、復旧ポイントの種類として "スナップショット" を使って、この復旧ポイントを識別できます。 コンテナーにスナップショットが転送されると、復旧ポイントの種類は "スナップショットとコンテナー" に変わります。 

![VM バックアップ スタック Resource Manager デプロイメント モデルのバックアップ ジョブ: ストレージとコンテナー](./media/backup-azure-vms/instant-rp-flow.jpg) 

既定では、スナップショットは 7 日間保持されます。 この機能により、このスナップショットからすばやく復元を終了できます。 データをコンテナーからユーザーのストレージ アカウントにコピーして戻すために必要な時間が短縮されます。 

## <a name="considerations-before-upgrade"></a>アップグレードの前の考慮事項
* VM バックアップ スタックのアップグレードは一方向です。 そのため、すべてのバックアップはそのフローになります。 これはサブスクリプション レベルで有効になっているため、すべての VM はそのフローになります。 新しい機能の追加はすべて、同じスタックに基づきます。 ポリシー レベルでこれを制御する機能は、将来のリリースで予定されています。

* 復旧ポイントの作成を促進し、復元の速度を上げるため、スナップショットはローカルに格納されます。 そのため、ストレージのコストは 7 日間のスナップショットに対応したものになります。

* 増分スナップショットは、ページ BLOB として格納されます。 非管理対象ディスクを使用しているすべてのユーザーは、スナップショットが自分のローカル ストレージ アカウントに格納される 7 日間に対して課金されます。 現在の価格モデルでは、管理対象ディスクのユーザーにはコストがかかりません。

* Premium VM のスナップショット復旧ポイントから復元を行う場合、復元の一部として VM が作成される間、一時的な保存場所が使われます。

* Premium Storage アカウントの場合、即時の復旧のために作成されるスナップショットは 10 TB の割り当て領域を占めます。

## <a name="upgrade"></a>アップグレード
### <a name="the-azure-portal"></a>Azure ポータル
Azure ポータルを使用する場合は、コンテナーのダッシュボードに通知が表示されます。 この通知は、大容量ディスクのサポートと、バックアップおよび復元の速度向上に関連しています。

![VM バックアップ スタック Resource Manager デプロイメント モデルのバックアップ ジョブ: サポート通知](./media/backup-azure-vms/instant-rp-banner.png) 

新しいスタックにアップグレードするための画面を開くには、バナーを選びます。 

![VM バックアップ スタック Resource Manager デプロイメント モデルのバックアップ ジョブ: アップグレード](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
管理者特権の PowerShell ターミナルから次のコマンドレットを実行します。
1.  Azure アカウントにサインインします。 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  プレビュー用に登録するサブスクリプションを選びます。

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  プライベート プレビュー用にこのサブスクリプションを登録します。

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>アップグレードが終了したことを確認する
管理者特権の PowerShell ターミナルから、次のコマンドレットを実行します。

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

"登録済み" と表示されたら、サブスクリプションは VM バックアップ スタック Resource Manager デプロイメント モデルにアップグレードされています。
