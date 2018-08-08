---
title: Azure VM バックアップ スタック V2 にアップグレードする
description: VM バックアップ スタック、Resource Manager デプロイメント モデルのアップグレード プロセスとよくある質問
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 8/1/2018
ms.author: trinadhk
ms.openlocfilehash: 1021900620272cc5476d8972daf9d7e0a161797a
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398003"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>Azure VM バックアップ スタック V2 にアップグレードする

仮想マシン (VM) バックアップ スタックへのアップグレード用 Resource Manager デプロイメント モデルには、次の拡張機能が用意されています。

* データ転送の終了を待たずに復旧に利用できるバックアップ ジョブの一環として取得されるスナップショットを表示できます。 これにより、復元をトリガーする前にスナップショットをコンテナーにコピーする待機時間が短縮されます。 また、この機能により、最初のバックアップを除き、Premium VM をバックアップするための追加ストレージ要件がなくなります。  

* スナップショットを 7 日間ローカルに保持することで、バックアップと復元の時間が短縮されます。

* 最大 4 TB のディスク サイズをサポートします。

* 復元時に非管理対象 VM の元のストレージ アカウントを使用できます。 この機能は、ストレージ アカウント間に分散しているディスクが VM にある場合でも使用できます。 さまざまな VM 構成で復元操作が速くなります。
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

* VM バックアップ スタックのアップグレードは一方向です。すべてのバックアップがこのフローに入ります。 変更はサブスクリプション レベルで行われるため、すべての VM はこのフローに入ります。 新しい機能の追加はすべて、同じスタックに基づきます。 現在、ポリシー レベルではスタックを制御できません。

* 復旧ポイントの作成を促進し、復元操作の速度を上げるため、スナップショットはローカルに格納されます。 結果として、ストレージのコストは 7 日間作成されたスナップショットに対応したものになります。

* 増分スナップショットは、ページ BLOB として格納されます。 非管理対象ディスクを使用しているすべてのユーザーは、スナップショットが自分のローカル ストレージ アカウントに格納される 7 日間に対して課金されます。 マネージド VM バックアップによって使用される復元ポイントのコレクションは、基になるストレージ レベルの BLOB スナップショットを使用するため、マネージド ディスクの場合は、[BLOB スナップショットの価格](https://docs.microsoft.com/rest/api/storageservices/understanding-how-snapshots-accrue-charges)に対応するコストが表示され、このコストは増分です。 

* スナップショット復旧ポイントから Premium VM を復元する場合、VM が作成される間、一時的な保存場所が使われます。

* Premium Storage アカウントの場合、インスタント復旧ポイントのために作成されるスナップショットには上限として 10 TB の領域が割り当てられています。

## <a name="upgrade"></a>アップグレード
### <a name="the-azure-portal"></a>Azure ポータル
Azure ポータルを使用する場合は、コンテナーのダッシュボードに通知が表示されます。 この通知は、大容量ディスクのサポートと、バックアップおよび復元の速度向上に関連しています。 または、コンテナーのプロパティ ページに移動して、アップグレード オプションを取得できます。

![VM バックアップ スタック Resource Manager デプロイメント モデルのバックアップ ジョブ: サポート通知](./media/backup-azure-vms/instant-rp-banner.png) 

新しいスタックにアップグレードするための画面を開くには、バナーを選びます。 

![VM バックアップ スタック Resource Manager デプロイメント モデルのバックアップ ジョブ: アップグレード](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
管理者特権の PowerShell ターミナルから次のコマンドレットを実行します。
1.  Azure アカウントにサインインします。 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  登録するサブスクリプションを選択します。

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  このサブスクリプションを登録します。

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>アップグレードが終了したことを確認する
管理者特権の PowerShell ターミナルから、次のコマンドレットを実行します。

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

"登録済み" と表示されたら、サブスクリプションは VM バックアップ スタック Resource Manager デプロイメント モデルにアップグレードされています。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

次の質問と回答はフォーラムとお客様からの質問から集められました。

### <a name="will-upgrading-to-v2-impact-current-backups"></a>V2 にアップグレードすると、現在のバックアップに影響が出ますか。

V2 にアップグレードする場合、現在のバックアップに影響が出ることはありません。環境を再構成する必要はありません。 アップグレードしても、バックアップ環境は今と同じように機能します。

### <a name="what-does-it-cost-to-upgrade-to-azure-vm-backup-stack-v2"></a>Azure VM Backup スタック V2 にアップグレードするためのコストは?

V2 にスタックをアップグレードする場合、コストはかかりません。 復旧ポイントの作成と復元操作を速めるために、スナップショットはローカルに格納されます。 結果として、ストレージのコストは 7 日間作成されたスナップショットに対応したものになります。

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>スタック V2 にアップグレードすると、Premium Storage アカウントのストレージ上限は 10 TB 増えますか。

いいえ。ストレージ アカウントあたりの合計スナップショットの制限は、10 TB のままです。 

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Premium Storage アカウントでは、インスタント回復ポイントのために作成されたスナップショットで 10 TB のスナップショット上限が占められますか。

はい。Premium Storage アカウントでは、インスタント回復ポイントのために作成されたスナップショットが割り当てられた 10 TB の領域を占めます。

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>7 日間の期間中、スナップショットはどのように動作しますか。 

新しいスナップショットが毎日作成されます。 個々のスナップショットが 7 つになります。 このサービスでは初日にコピーが**作成されず**、翌日から 6 日間の変化が追加されます。

### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>V2 スナップショットは増分スナップショットですか、それとも完全スナップショットですか。

アンマネージド ディスクには増分スナップショットが使用されます。 マネージド ディスクの場合、Azure Backup によって作成される復元ポイント コレクションは、BLOB スナップショットを使用するため、増分されます。 
