---
title: Azure の Recovery Services コンテナーを削除する
description: Recovery Services コンテナーを削除する方法について説明します。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: raynew
ms.openlocfilehash: e83698af6bb1caab1568375b726753d34a8c8467
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861351"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services コンテナーを削除する

この記事では、[Azure Backup](backup-overview.md) Recovery Services コンテナーを削除する方法について説明します。 依存関係を削除してから、コンテナーの削除および強制的なコンテナーの削除を行うための手順が含まれています。




## <a name="before-you-start"></a>開始する前に

開始する前に、中にサーバーが登録されている Recovery Services コンテナー、またはバックアップ データを保持している Recovery Services コンテナーは削除できないことを理解することが重要です。


- コンテナーを正しく削除するには、コンテナー内のサーバーの登録を解除してから、コンテナー データを削除します。
- Recovery Services のコンテナーにデータを保持したくないときにコンテナーを削除する場合は、コンテナーを強制的に削除することができます。
- コンテナーを削除しようとしても削除できない場合、コンテナーがバックアップ データを受信するように構成されたままになっています。

コンテナーを削除する方法については、「[Azure portal からコンテナーを削除する](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal)」を参照してください。 [コンテナーの強制的な削除](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force)に関するセクション。 コンテナーの内容が不明なときにコンテナーを削除できることを確認する必要がある場合は、「[コンテナーの依存関係を削除し、コンテナーを削除する](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault)」を参照してください。

## <a name="delete-a-vault-from-the-azure-portal"></a>Azure portal からコンテナーを削除する

1. ポータル内で Recovery Services コンテナーの一覧を開きます。
2. 一覧で、削除するコンテナーを選択します。 コンテナー ダッシュボードが開かれます。

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. コンテナー ダッシュボードで **[削除]** をクリックします。 削除するかどうかを確認します。

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

2. コンテナーの依存関係が存在する場合は、**コンテナーの削除エラー**が表示されます。 

    ![Vault deletion error](./media/backup-azure-delete-vault/vault-delete-error.png)

    - コンテナーを削除する前に、以下の手順に従って依存関係を削除し、確認します。
    - PowerShell を使用してコンテナーを強制的に削除するには、[次の手順に従います](#delete-the-recovery-services-vault-by-force)。 

## <a name="delete-the-recovery-services-vault-by-force"></a>Recovery Services コンテナーを強制的に削除する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を使用すると、Recovery Services コンテナーを強制的に削除できます。 これはコンテナーと、関連付けられたすべてのバックアップ データとが完全に削除されることを意味します。 

> [!Warning]
> PowerShell を使用して Recovery Services コンテナーを削除する場合は、コンテナー内のすべてのバックアップ データを完全に削除したいのかを確認してください。
>

Recovery Services コンテナーを削除するには:

1. `Connect-AzAccount` コマンドを使用してご利用の Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
    Connect-AzAccount
   ```
2. Azure Backup を最初に使用するときは、[Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider) を使ってサブスクリプション内で Azure Recovery Service プロバイダーを登録する必要があります。

   ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

3. 管理者特権で PowerShell ウィンドウを開きます。
4. `Set-ExecutionPolicy Unrestricted` を使用して、すべての制約を解除します。
5. 次のコマンドを実行して、Azure Resource Manager クライアント パッケージを chocolately.org からダウンロードします。

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

6. 次のコマンドを使用して、Azure Resource Manager API クライアントをインストールします。

   `choco.exe install armclient`

7. Azure portal で、削除するコンテナーのサブスクリプション ID とリソース グループ名を収集します。
8. PowerShell で、ご利用のサブスクリプション ID、リソース グループ名、コンテナー名を使用して、次のコマンドを実行します。 このコマンドを実行すると、コンテナーとすべての依存関係が削除されます。

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
9. コンテナーが空ではない場合、"内部にリソースが存在するため、コンテナーを削除できません" という内容のエラーが発生します。 コンテナー内に含まれているリソースを削除するには、次の操作を行います。

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

10. Azure portal でご自分のサブスクリプションにサインインし、コンテナーが削除されていることを確認します。


## <a name="remove-vault-dependencies-and-delete-vault"></a>コンテナーの依存関係を削除し、コンテナーを削除する

コンテナーの依存関係は次のように手動で削除できます。

- **[バックアップ アイテム]** メニューで、依存関係を削除します。
    - Azure Storage (Azure Files) のバックアップ
    - Azure VM の SQL Server のバックアップ
    - Azure 仮想マシンのバックアップ
- **[バックアップ インフラストラクチャ]** メニューで、依存関係を削除します。
    - Microsoft Azure Backup Server (MABS) のバックアップ
    - System Center DPM のバックアップ

![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

### <a name="remove-backup-items"></a>バックアップ アイテムの削除

この手順では、Azure Files からバックアップ データを削除する方法を示す例を提供します。

1. **[バックアップ アイテム]** > **[Azure Storage (Azure Files)]** の順にクリックします。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. 削除する各 Azure Files アイテムを右クリックし、**[バックアップの停止]** をクリックします。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/stop-backup-item.png)


3. **[バックアップの停止]** > **[オプションの選択]** の順に進み、**[バックアップ データの削除]** を選択します。
4. アイテムの名前を入力し、**[バックアップの停止]** をクリックします。 
   - これで、コンテナーの削除を希望していることが確認されます。
   - 確認後、**[バックアップの停止]** ボタンがアクティブになります。
   - データを保持し、データを削除しない場合、コンテナーを削除することはできません。

     ![[バックアップ データの削除]](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 必要に応じて、データを削除する理由を入力し、コメントを追加します。
6. 削除ジョブが完了したことを確認するには、Azure メッセージを調べます。 ![[バックアップ データの削除]](./media/backup-azure-delete-vault/messages.png)。
7. ジョブが完了すると、"**バックアップ プロセスが停止されたことと、バックアップ データが削除されたこと**" を示すメッセージがサービスから送信されます。
8. 一覧のアイテムを削除した後、**[Backup Items ]\(バックアップ アイテム\)** メニューの **[更新]** をクリックして、コンテナー内のアイテムを確認します。

      ![[バックアップ データの削除]](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>バックアップ インフラストラクチャ サーバーを削除する

1. コンテナーのダッシュボード メニューの **[インフラストラクチャのバックアップ]** をクリックします。
2. **[バックアップ管理サーバー]** をクリックしてサーバーを確認します。 

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

2. アイテムを右クリックして、**[削除]** をクリックします。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. 。 **[バックアップの停止]** > **[オプションの選択]** の順に進み、**[バックアップ データの削除]** を選択します。
4. アイテムの名前を入力し、**[バックアップの停止]** をクリックします。 
   - これで、コンテナーの削除を希望していることが確認されます。
   - 確認後、**[バックアップの停止]** ボタンがアクティブになります。
   - データを保持し、データを削除しない場合、コンテナーを削除することはできません。

     ![[バックアップ データの削除]](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 必要に応じて、データを削除する理由を入力し、コメントを追加します。
6. 削除ジョブが完了したことを確認するには、Azure メッセージを調べます。 ![[バックアップ データの削除]](./media/backup-azure-delete-vault/messages.png)。
7. ジョブが完了すると、"**バックアップ プロセスが停止されたことと、バックアップ データが削除されたこと**" を示すメッセージがサービスから送信されます。
8. 一覧のアイテムを削除した後、**[Backup Items ]\(バックアップ アイテム\)** メニューの **[更新]** をクリックして、コンテナー内のアイテムを確認します。


### <a name="remove-azure-backup-agent-recovery-points"></a>Azure Backup エージェントの復旧ポイントを削除する

1. コンテナーのダッシュボード メニューの **[インフラストラクチャのバックアップ]** をクリックします。
2. **[バックアップ管理サーバー]** をクリックして、インフラストラクチャ サーバーを表示します。

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. **[保護されたサーバー]** の一覧で、[Azure Backup エージェント] をクリックします。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Azure Backup エージェントを使用して保護されているサーバーの一覧内でサーバーをクリックします。

    ![特定の保護されたサーバーを選択する](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. 選択したサーバーのダッシュボードで、**[削除]** をクリックします。

    ![選択したサーバーを削除する](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. **[削除]** メニューで、アイテムの名前を入力し、**[削除]** をクリックします。
   - これで、コンテナーの削除を希望していることが確認されます。
   - 確認後、**[バックアップの停止]** ボタンがアクティブになります。
   - データを保持し、データを削除しない場合、コンテナーを削除することはできません。

     ![[バックアップ データの削除]](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. 必要に応じて、データを削除する理由を入力し、コメントを追加します。
8. 削除ジョブが完了したことを確認するには、Azure メッセージを調べます。 ![[バックアップ データの削除]](./media/backup-azure-delete-vault/messages.png)。
7. ジョブが完了すると、"**バックアップ プロセスが停止されたことと、バックアップ データが削除されたこと**" を示すメッセージがサービスから送信されます。
8. 一覧のアイテムを削除した後、**[Backup Items ]\(バックアップ アイテム\)** メニューの **[更新]** をクリックして、コンテナー内のアイテムを確認します。



### <a name="delete-the-vault-after-removing-dependencies"></a>依存関係を削除してからコンテナーを削除する

1. すべての依存関係が削除されたら、コンテナー メニュー内の **[Essentials]** ウィンドウにスクロールします。

    - 一覧には、**[バックアップ アイテム]**、**[バックアップ管理サーバー]**、**[レプリケートされたアイテム]** のどれも表示されないはずです。
    - コンテナー内にまだアイテムが表示されている場合は、それを削除します。

2. コンテナーにアイテムがなくなったら、コンテナー ダッシュボードで **[削除]** をクリックします。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. コンテナーを削除することを確認するために、**[はい]** をクリックします。 コンテナーが削除され、ポータルが **[新規]** サービス メニューに戻ります。

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>バックアップ プロセスを停止した一方でデータを保持した場合の対処

バックアップ プロセスを停止したものの、誤ってデータが保持されてしまった場合は、前のセクションで説明したように、バックアップ データを削除する必要があります。

## <a name="next-steps"></a>次の手順

Recovery Services コンテナーの[詳細情報](backup-azure-recovery-services-vault-overview.md)。
