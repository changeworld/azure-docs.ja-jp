---
title: Azure のインスタント リストア機能
description: Azure のインスタント リストア機能と、VM バックアップ スタック、Resource Manager デプロイ モデルに関する FAQ
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: trinadhk
ms.openlocfilehash: a5afdd0dca55a5df075acb99e488c932196d95c5
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55092300"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Azure Backup のインスタント リストア機能を使用してバックアップと復元のパフォーマンスを改善する

> [!NOTE]
> ユーザーからのフィードバックに基づき、Azure Stack 機能との混同を避けるため、名前を**VM バックアップ スタック V2** から**インスタント リストア**に変更しています。

インスタント リストアの新しいモデルでは、次の機能が強化されています。

* コンテナーへのデータ転送の終了を待たずに、復旧に利用できるバックアップ ジョブの一環として取得されるスナップショットを使用できます。 これにより、復元をトリガーする前にスナップショットをコンテナーにコピーする待機時間が短縮されます。
* 7 日間、スナップショットをディスクと共に保持することで、バックアップと復元の時間が短縮されます。
* 最大 4 TB のディスク サイズがサポートされます。
* Standard SSD ディスクがサポートされます。
* 復元時に、(ディスクごとの) アンマネージド VM の元のストレージ アカウントを使用できます。 この機能は、ストレージ アカウント間に分散しているディスクが VM にある場合でも使用できます。 さまざまな VM 構成で復元操作が速くなります。


## <a name="whats-new-in-this-feature"></a>この機能の更新点

現在、バックアップ ジョブは 2 つのフェーズで構成されます。

1.  VM スナップショットを作成します。
2.  Azure Recovery Services コンテナーに VM スナップショットを転送します。

復旧ポイントは、フェーズ 1 および 2 が完了した後でのみ、作成されたものと見なされます。 このアップグレードの一部として、スナップショットが完了するとすぐに復旧ポイントが作成されます。このスナップショットの種類の復旧ポイントを利用し、同じ復元フローを使って復元を実行することができます。 復旧ポイントの種類として "スナップショット" を使用することで、この復旧ポイントを Azure portal で識別することができます。コンテナーにスナップショットが転送された後、復旧ポイントの種類は "スナップショットとコンテナー" に変わります。

![VM バックアップ スタック Resource Manager デプロイメント モデルのバックアップ ジョブ: ストレージとコンテナー](./media/backup-azure-vms/instant-rp-flow.png)

スナップショットは 7 日間保持されます。 この機能では、復元時間を削減して、これらのスナップショットから復元操作を行うことができます。 アンマネージド ディスク シナリオでは、データを変換し、コンテナーからユーザーのストレージ アカウントにコピーして戻すのに要する時間が短縮されます。マネージド ディスク ユーザーの場合、バックアップ データからマネージド ディスクが作成されます。

## <a name="feature-considerations"></a>機能に関する考慮事項

* 復旧ポイントの作成を促進し、復元操作の速度を上げるため、スナップショットはディスクと共に格納されます。 結果として、ストレージのコストはこの期間に取得されたスナップショットに対応したものになります。
* 増分スナップショットは、ページ BLOB として格納されます。 アンマネージド ディスクを使用しているすべてのユーザーは、自分のローカル ストレージ アカウントに格納されているスナップショットに対して課金されます。 マネージド VM バックアップで使用される復元ポイント コレクションでは、基になるストレージ レベルの BLOB スナップショットが使用されるため、マネージド ディスクの場合は、BLOB スナップショットの価格に対応するコストが表示され、これらは増分となります。
* Premium Storage アカウントの場合、インスタント復旧ポイントのために作成されるスナップショットには上限として 10 TB の領域が割り当てられています。

## <a name="cost-impact"></a>コストの影響

増分スナップショットは VM のストレージ アカウントに格納され、インスタント復旧のために使用されます。 増分スナップショットは、スナップショットによって占有される領域が、スナップショットの作成後に記述されるページによって占有される領域と等しいことを意味します。 課金は、スナップショットによって占有される GB あたりの使用済み領域に対して引き続き行われ、GB あたりの料金は、[価格ページ](https://azure.microsoft.com/pricing/details/managed-disks/)に示されているものと同じになります。


## <a name="upgrading-to-instant-restore"></a>インスタント リストアへのアップグレード

Azure portal を使用する場合は、コンテナーのダッシュボードに通知が表示されます。 この通知は、大容量ディスクのサポートと、バックアップおよび復元の速度向上に関連しています。

![VM バックアップ スタック Resource Manager デプロイメント モデルのバックアップ ジョブ: サポート通知](./media/backup-azure-vms/instant-rp-banner.png)

インスタント リストアにアップグレードするための画面を開くには、バナーを選びます。

![VM バックアップ スタック Resource Manager デプロイメント モデルのバックアップ ジョブ: アップグレード](./media/backup-azure-vms/instant-rp.png)

または、コンテナーの **[プロパティ]** ページに移動し、**[VM バックアップ スタック]** で **[アップグレード]** オプションを取得できます。

![VM バックアップ スタックでのバックアップ ジョブ - [プロパティ] ページ](./media/backup-azure-vms/instant-restore-capability-properties.png)


## <a name="upgrade-to-instant-restore-using-powershell"></a>PowerShell を使用してインスタント リストアにアップグレードする

セルフサービスでインスタント リストアにアップグレードしたい場合は、管理者特権の PowerShell ターミナルから次のコマンドレットを実行します。

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

## <a name="upgrade-to-instant-restore-using-cli"></a>CLI を使用してインスタント リストアにアップグレードする

Shell から次のコマンドを実行します。

1.  Azure アカウントにサインインします。

    ```
    az login
    ```

2.  登録するサブスクリプションを選択します。

    ```
    az account set --subscription "Subscription Name"
    ```

3.  このサブスクリプションを登録します。

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-successful"></a>アップグレードが成功したことを確認する

### <a name="powershell"></a>PowerShell
管理者特権の PowerShell ターミナルから、次のコマンドレットを実行します。

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>CLI
シェルで、次のコマンドを実行します。

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

"登録済み" と表示された場合、サブスクリプションはインスタント リストアにアップグレードされています。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-are-the-cost-implications-of-instant-restore"></a>インスタント リストアはコストにどのように影響しますか。
復旧ポイントの作成と復元操作の速度を上げるため、スナップショットはディスクと共に格納されます。 その結果、VM バックアップ ポリシーの一部として選択されるスナップショットの保有期間に対応するストレージ コストが表示されます。

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Premium Storage アカウントでは、インスタント回復ポイントのために作成されたスナップショットで 10 TB のスナップショット上限が占められますか。
はい。Premium Storage アカウントでは、インスタント回復ポイントのために取得されたスナップショットが、割り当てられたスナップショットの 10 TB の領域を占めます。

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>5 日間の期間中、スナップショットの保有期間はどのようになりますか。
毎日新しいスナップショットが取得されるため、個別の増分スナップショットが 5 個になります。 スナップショットのサイズはデータの変化によって異なります (ほとんどの場合、約 2% から 7%)。

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>インスタント リストア スナップショットは増分スナップショットですか、それとも完全スナップショットですか。
インスタント リストア機能の一部として取得されるスナップショットは、増分スナップショットです。

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>インスタント リストア機能による、概算コストの増加はどのように計算すればよいですか。
VM の変化によって異なります。 安定状態では、コストの増加は、スナップショットの保有期間 * VM あたりの日次変化 * GB あたりのストレージ コストになると考えられます。

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>復元ポイントの回復の種類が "スナップショットとコンテナー" であり、復元操作を実行する場合は、どの回復の種類が使用されますか。
回復の種類が "スナップショットとコンテナー" の場合は、ローカルのスナップショットから自動的に復元が行われ、コンテナーからの復元に比べ、はるかに高速になります。

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>スナップショット (階層 1) の保有期間よりも短い、復元ポイント (階層 2) の保有期間を選択するとどうなりますか。
新しいモデルでは、スナップショット (階層 1) を削除しない限り、復元ポイント (階層 2) を削除することはできません。 現在、スナップショット (階層 1) の削除については 7 日間の保有期間がサポートされているため、復元ポイント (階層 2) の保有期間を 7 日未満にすることはお勧めできません。 復元ポイント (階層 2) の保有期間が 7 日間よりも長くなるようスケジュール設定することをお勧めします。

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>バックアップ ポリシーに設定されている保有期間が過ぎても、スナップショットが存在するのはなぜですか。
回復ポイントにスナップショットがあり、それが利用可能な最新の RP である場合、次のバックアップが成功するまで保持されます。 これは現在、設計されている GC ポリシーに従ったものです。このポリシーでは、VM での問題によって、今後のすべてのバックアップが失敗した場合に、1 つ以上の最新の RP が常に存在することが要求されます。 通常のシナリオでは、有効期限後、最大 48 時間で RP がクリーンアップされます。
