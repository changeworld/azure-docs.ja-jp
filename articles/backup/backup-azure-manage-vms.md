---
title: Azure Backup サービスで Azure VM のバックアップを管理および監視する
description: Azure Backup サービスで Azure VM のバックアップを管理および監視する方法を説明します。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: f5c0373e2ef094a7fc5be64f4aeb8c0bb132e683
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430065"
---
# <a name="manage-azure-vm-backups"></a>Azure VM のバックアップを管理する

この記事では、[Azure Backup サービス](backup-overview.md) バックアップでバックアップされる Azure VM を管理する方法を説明し、ポータルのダッシュボードで使用できるバックアップ アラートの情報をまとめます。


Azure ポータルから Recovery Services コンテナーのダッシュボードにアクセスすると、コンテナーに関して次のような情報を確認できます。

* 最新のバックアップ (最新の復元ポイントでもあります)。
* バックアップ ポリシー
* すべてのバックアップ スナップショットの合計サイズ
* バックアップが有効になっている VM の数

ダッシュボードを使用し、個々の VM にドリルダウンして、バックアップを管理できます。 仮想マシンのバックアップに関する管理タスクの多くでは、最初にダッシュボードでコンテナーを開きます。 

![Full view with slider](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>ダッシュボードで VM を表示する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. ハブ メニューで **[参照]** をクリックし、リソースの一覧で「**Recovery Services**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]** をクリックします。 
    ![Recovery Services コンテナー作成ステップ 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. 使いやすいように、コンテナーの一覧でコンテナーを右クリックして、**[ダッシュボードにピン留めする]** を選択します。
3. コンテナー ダッシュボードを開きます。 
    ![コンテナー ダッシュボードと設定ブレードを開く](./media/backup-azure-manage-vms/full-view-rs-vault.png)

4. **[バックアップ項目]** タイルで、**[Azure Virtual Machines]** をクリックします。

    ![Open backup items tile](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. **[バックアップ項目]** ブレードでは、項目ごとの最新のバックアップ ジョブを確認できます。 この例では、このコンテナーによって保護されている仮想マシンが 1 つ存在します (demovm-markgal)。  

    ![Backup items tile](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

 
6. コンテナー項目ダッシュボードでは、バックアップ ポリシーの作成または変更、復元ポイントの表示、オンデマンド バックアップの実行、VM の保護の停止と再開、復旧ポイントの削除、復元の実行を行うことができます。

    ![Backup items dashboard with Settings blade](./media/backup-azure-manage-vms/item-dashboard-settings.png)



## <a name="manage-backup-policies"></a>バックアップ ポリシーを管理する
1. [コンテナー項目ダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で、**[すべての設定]** をクリックします。

    ![Backup policy blade](./media/backup-azure-manage-vms/all-settings-button.png)
2. **[設定]** で、**[バックアップ ポリシー]** をクリックします。
3. **[バックアップ ポリシーの選択]** メニューから次の手順を実行します。

   * ポリシーを変更するには、別のポリシーを選択し、 **[保存]** をクリックします。 すぐに、新しいポリシーがコンテナーに適用されます。
   * ポリシーを作成するには、 **[新規作成]** を選択します。 [詳細情報](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![仮想マシンのバックアップ](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する
VM に保護が構成されている場合、その VM のオンデマンド バックアップを作成できます。
- 初回バックアップがまだ実行されていない場合、オンデマンド バックアップを実行すると、仮想マシンの完全なコピーが Recovery Services コンテナーに作成されます。
- 初回バックアップが完了している場合、オンデマンド バックアップによって Recovery Services コンテナーに送信されるのは、前回のスナップショット以降の変更だけです。 つまり、以降のバックアップでは、増分のみが送信対象となります。
- オンデマンド バックアップのリテンション期間は、ポリシーの中で日単位のバックアップ ポイントに対して指定されたリテンション期間の値となります。 日単位のバックアップ ポイントが選択されていない場合は、週単位のバックアップ ポイントが使用されます。


オンデマンド バックアップをトリガーするには:

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[今すぐバックアップ]** をクリックします。

    ![Backup now button](./media/backup-azure-manage-vms/backup-now-button.png)

 2. バックアップ ジョブを開始する場合は **[はい]** をクリックします。

    ![Backup now button](./media/backup-azure-manage-vms/backup-now-check.png)

 
 バックアップ ジョブによって、復旧ポイントが作成されます。 復旧ポイントのリテンション期間は、仮想マシンに関連付けられているポリシーで指定されるリテンション期間と同じです。 ジョブの進行状況を追跡するには、コンテナーのダッシュボードで **[バックアップ ジョブ]** タイルをクリックします。  

## <a name="stop-protecting-a-vm"></a>VM の保護を停止する

VM の保護を停止するには、次の 2 つの方法があります。

- 今後のバックアップ ジョブすべてを停止し、すべての復旧ポイントを削除する。 この場合、VM を復元することはできません。
- 今後のバックアップ ジョブすべてを停止するが、復旧ポイントはそのままにする。 復旧ポイントをストレージに保持するためにはコストが発生します。 一方、復旧ポイントを残す利点は、必要に応じて VM を復元できることです。 料金の詳細については[こちらをご覧ください](https://azure.microsoft.com/pricing/details/backup/)。

仮想マシンに対する保護を停止するには、次の手順に従います。

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[バックアップの停止]** をクリックします。

    ![Stop backup button](./media/backup-azure-manage-vms/stop-backup-button.png
2. バックアップ データを保持するか削除するかを選択し、必要に応じて確認します。 必要に応じて確認し、必要に応じてコメントを入力します。 項目名がわからない場合は、感嘆符にマウス カーソルを合わせると名前が表示されます。

    ![保護の停止](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 バックアップ ジョブが停止したことを示す通知メッセージが表示されます。


## <a name="resume-protection-of-a-vm"></a>VM の保護を再開する

VM を停止するときにバックアップ データを保持した場合は、保護を再開できます。 バックアップ データを削除した場合は、再開できません。

Te

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[バックアップの再開]** をクリックします。

2. [バックアップ ポリシーの管理](backup-azure-manage-vms.md#manage-backup-policies)に関するトピックの手順に従って、仮想マシンのポリシーを割り当てます。 最初に仮想マシンを保護するために使用いたポリシー以外のポリシーを選択できます。
3. 仮想マシンにバックアップ ポリシーが適用された後、次のメッセージが表示されます。

    ![Successfully protected VM](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>バックアップ データの削除

VM に関連付けられているバックアップ データは、**バックアップの停止**ジョブの実行中やバックアップ ジョブが完了した後にいつでも削除することができます。

- 数日または数週間待ってから復旧ポイントを削除する方が便利な場合もあります。
- 復旧ポイントの復元とは異なり、バックアップ データを削除するときは、特定の復旧ポイントを削除対象として選択することができません。 バックアップ データを削除するように選択すると、その項目に関連付けられている復旧ポイントもすべて削除されます。

この手順では、VM のバックアップ ジョブが停止済みであるか、無効にされていることが想定されています。


1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[バックアップ データの削除]** をクリックします。

    ![VM の種類](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. 項目の名前を入力して、復旧ポイントを削除してもよいことを確認します。

    ![Stop verification](./media/backup-azure-manage-vms/item-verification-box.png)

4. 現在の項目のバックアップ データを削除するには、![[バックアップの停止] ボタン](./media/backup-azure-manage-vms/delete-button.png) をクリックします。

    バックアップ データが削除されたことを示す通知メッセージが表示されます。

## <a name="next-steps"></a>次の手順
- VM 設定からの Azure VM のバックアップについて[学習します](backup-azure-vms-first-look-arm.md)。
- VM の復元について[学習します](backup-azure-arm-restore-vms.md)。 
- Azure VM のバックアップの監視について[学習します](backup-azure-monitor-vms.md)。
 
