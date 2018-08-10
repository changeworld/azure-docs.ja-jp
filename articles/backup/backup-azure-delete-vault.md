---
title: Azure の Recovery Services コンテナーを削除する
description: この記事では、Recovery Services コンテナーを削除する方法について説明します。 この記事には、コンテナーを削除しようとしているのに削除できない場合のトラブルシューティングの手順が含まれています。
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 7/6/2018
ms.author: markgal
ms.openlocfilehash: 4dc5b006be8599177fb908fe022a3a821b137e12
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422945"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services コンテナーを削除する

この記事では、Recovery Services コンテナーからすべてのアイテムを削除した後、コンテナーを削除する方法について説明します。 Recovery Services コンテナーがサーバーに登録されていて、バックアップ データを保持している場合、Recovery Services コンテナーを削除することはできません。 コンテナーを削除しようとしても削除できない場合、コンテナーがバックアップ データを受信するように構成されたままになっています。

コンテナーを削除する方法については、「[Azure portal からコンテナーを削除する](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal)」を参照してください。 Recovery Services のコンテナーにデータを保持したくないときにコンテナーを削除する場合は、[コンテナーを強制的に削除する方法](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force)に関するセクションを参照してください。 コンテナーの内容が不明なときにコンテナーを削除できることを確認する必要がある場合は、「[コンテナーの依存関係を削除し、コンテナーを削除する](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault)」を参照してください。

## <a name="delete-a-vault-from-azure-portal"></a>Azure portal からコンテナーを削除する

既に Recovery Services コンテナーを開いている場合は、手順 2. に進みます。

1. Azure Portal を開き、削除するコンテナーをダッシュボードから開きます。

   Recovery Services コンテナーがダッシュボードにピン留めされていない場合は、ハブ メニューで **[すべてのサービス]** をクリックし、リソースの一覧で「**Recovery Services**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 サブスクリプションのコンテナーの一覧を表示するために、**[Recovery Services コンテナー]** をクリックします。

   ![Create Recovery Services Vault step 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Recovery Services コンテナーの一覧が表示されます。 

   ![choose vault from list](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

1. 一覧で、削除するコンテナーを選択します。 コンテナーを選択すると、そのコンテナーのダッシュボードが開きます。

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. コンテナーを削除するために、コンテナー ダッシュボードで **[削除]** をクリックします。 コンテナーの削除を確認するように求めるメッセージが表示されます。

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    "**コンテナーの削除エラー**" が表示された場合は、コンテナーから依存関係を削除するか、PowerShell を使用して強制的にコンテナーを削除することができます。 以降のセクションでは、これらのタスクを実行する方法について説明します。

    ![Vault deletion error](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Recovery Services コンテナーを強制的に削除する

PowerShell を使用すると、Recovery Services コンテナーを強制的に削除できます。 "強制的に" とは、Recovery Services コンテナーと関連するすべてのバックアップ データが完全に削除されることを意味します。 

> [!Warning]
> PowerShell を使用して Recovery Services コンテナーを削除する場合は、コンテナー内のすべてのバックアップ データを完全に削除する必要があることを確認してください。
>

Recovery Services コンテナーを削除するには:

1. Azure アカウントにサインインします。

   `Connect-AzureRmAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
    Connect-AzureRmAccount
   ```
   Azure Backup を最初に使用するときは、[Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) を使ってサブスクリプション内で Azure Recovery Service プロバイダーを登録する必要があります。

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

1. 管理者特権で PowerShell ウィンドウを開きます。

1. `Set-ExecutionPolicy Unrestricted` を使用して、すべての制約を解除します。

1. 次のコマンドを実行して、Azure Resource Manager クライアント パッケージを chocolately.org からダウンロードします。

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

1. 次のコマンドを使用して、Azure Resource Manager API クライアントをインストールします。

   `choco.exe install armclient`

1. Azure portal で、削除する Recovery Services コンテナーのサブスクリプション ID と関連するリソース グループ名を収集します。

1. PowerShell で、目的のサブスクリプション ID、リソース グループ名、および Recovery Services コンテナー名を使用して、次のコマンドを実行します。 このコマンドを実行すると、コンテナーとすべての依存関係が削除されます。

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
1. Azure portal でサブスクリプションにサインインし、コンテナーが削除されたことを確認します。


## <a name="remove-vault-dependencies-and-delete-vault"></a>コンテナーの依存関係を削除し、コンテナーを削除する

コンテナーの依存関係を手動で削除するには、各アイテムまたはサーバーと Recovery Services コンテナー間の構成を削除します。 次の手順を実行するときは、次のアイテムに対して **[Backup Items ]\(バックアップ アイテム\)** メニュー (画像を参照) を使用します。

* Azure Storage (Azure Files) のバックアップ
* Azure VM の SQL Server のバックアップ
* Azure 仮想マシンのバックアップ
* Microsoft Azure Recovery Services エージェントのバックアップ

次の項目に対しては **[バックアップ インフラストラクチャ]** メニュー (画像を参照) を使用します。

* Azure Backup Server のバックアップ
* System Center DPM のバックアップ

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. コンテナー ダッシュボードのメニューで、[Protected Items]\(保護されたアイテム\) セクションまで下へスクロールし、**[Backup Items ]\(バックアップ アイテム\)** をクリックします。 このメニューでは、Azure ファイル サーバー、Azure VM の SQL Server、および Azure 仮想マシンを停止および削除できます。 この例では、Azure ファイル サーバーからバックアップ データを削除します。

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/selected-backup-items.png)

1. バックアップの種類を選択して、その種類のすべてのアイテムを表示します。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

1. 一覧内のすべてのアイテムについて、アイテムを右クリックし、コンテキスト メニューから **[バックアップの停止]** を選択します。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/stop-backup-item.png) 

    [バックアップの停止] メニューが開きます。

1. **[バックアップの停止]** メニューの **[オプションの選択]** メニューで **[バックアップ データの削除]** を選択し、アイテムの名前を入力して、**[バックアップの停止]** をクリックします。

    削除することを確認するために、アイテムの名前を入力します。 アイテムを確認したら、**[バックアップの停止]** ボタンがアクティブになります。 データを保持すると、コンテナーを削除することはできません。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    必要に応じて、データを削除する理由を指定し、コメントを追加します。 ジョブの完了を確認するために、Azure メッセージ![バックアップ データの削除](./media/backup-azure-delete-vault/messages.png)を確認します。 <br/>
    ジョブが完了すると、"*バックアップ プロセスが停止されたことと、バックアップ データが削除されたこと*" を示すメッセージがサービスから送信されます。

1. 一覧のアイテムを削除した後、**[Backup Items ]\(バックアップ アイテム\)** メニューの **[更新]** をクリックして、コンテナー内のアイテムを確認します。

      ![[バックアップ データの削除]](./media/backup-azure-delete-vault/empty-items-list.png)

      一覧にアイテムがなくなったら、[Recovery Services コンテナー] メニューの **[要点]** ウィンドウまでスクロールします。 一覧には、**[バックアップ アイテム]**、**[バックアップ管理サーバー]**、**[レプリケートされたアイテム]** のどれも表示されないはずです。 コンテナーに引き続きアイテムが表示される場合は、手順 3. に戻り、別のアイテムの種類の一覧を選択します。  

1. コンテナー ツール バーにアイテムがなくなったら、 **[削除]** をクリックします。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. コンテナーを削除することを確認するために、**[はい]** をクリックします。

    コンテナーが削除され、ポータルが **[新規]** サービス メニューに戻ります。

## <a name="removing-azure-backup-server-or-dpm"></a>Azure Backup Server または DPM の削除

1. コンテナー ダッシュボードのメニューで、[管理] セクションまで下へスクロールし、**[バックアップ インフラストラクチャ]** をクリックします。 

1. サブメニューで、**[バックアップ管理サーバー]** をクリックして、Azure Backup サーバーと System Center DPM サーバーを表示します。 Azure ファイル サーバー、Azure VM の SQL Server、および Azure 仮想マシンを停止および削除できます。 

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

1. 削除するアイテムを右クリックし、サブメニューの **[削除]** を選択します。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    [バックアップの停止] メニューが開きます。

1. **[バックアップの停止]** メニューの **[オプションの選択]** メニューで **[バックアップ データの削除]** を選択し、アイテムの名前を入力して、**[バックアップの停止]** をクリックします。

    削除することを確認するには、その名前を入力します。 アイテムを確認したら、**[バックアップの停止]** ボタンがアクティブになります。 データを保持すると、コンテナーを削除することはできません。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    必要に応じて、データを削除する理由を指定し、コメントを追加できます。 ジョブの完了を確認するには、Azure メッセージ ![バックアップ データの削除](./media/backup-azure-delete-vault/messages.png) <br/>
    ジョブが完了すると、バックアップ プロセスが停止されたことと、バックアップ データが削除されたことを示すメッセージがサービスから送信されます。

1. 一覧のアイテムを削除した後、**[バックアップ アイテム]** メニューの **[更新]** をクリックして、コンテナー内の残りのアイテムを確認します。

      ![[バックアップ データの削除]](./media/backup-azure-delete-vault/empty-items-list.png)

      一覧にアイテムがなくなったら、[Recovery Services コンテナー] メニューの **[要点]** ウィンドウまでスクロールします。 一覧には、**[バックアップ アイテム]**、**[バックアップ管理サーバー]**、**[レプリケートされたアイテム]** のどれも表示されないはずです。 コンテナーに引き続きアイテムが表示される場合は、手順 3. に戻り、別のアイテムの種類の一覧を選択します。  
1. コンテナーにアイテムがなくなったら、コンテナー ダッシュボードで **[削除]** をクリックします。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. コンテナーを削除することを確認するために、**[はい]** をクリックします。

    コンテナーが削除され、ポータルが **[新規]** サービス メニューに戻ります。


## <a name="removing-azure-backup-agent-recovery-points"></a>Azure Backup エージェントの復旧ポイントの削除

1. コンテナー ダッシュボードのメニューで、[管理] セクションまで下へスクロールし、**[バックアップ インフラストラクチャ]** をクリックします。

1. サブメニューで、**[保護されたサーバー]** をクリックして、Azure Backup エージェントを含む保護されたサーバーの種類の一覧を表示します。

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/identify-protected-servers.png)

1. **[保護されたサーバー]** の一覧で、[Azure Backup エージェント] をクリックします。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Azure Backup エージェントを使用して保護されているサーバーの一覧が表示されます。

    ![特定の保護されたサーバーを選択する](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

1. サーバーの一覧で、目的のサーバーをクリックしてメニューを開きます。

    ![選択したサーバーのダッシュボードを表示する](./media/backup-azure-delete-vault/selected-protected-server.png)

1. 選択したサーバーのダッシュボード メニューで、**[削除]** をクリックします。

    ![選択したサーバーを削除する](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

1. **[削除]** メニューで、アイテムの名前を入力し、**[削除]** をクリックします。

    削除することを確認するために、アイテムの名前を入力します。 アイテムを確認すると、**[削除]** ボタンがアクティブになります。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    必要に応じて、データを削除する理由を指定し、コメントを追加できます。 ジョブの完了を確認するには、Azure メッセージ ![バックアップ データの削除](./media/backup-azure-delete-vault/messages.png) <br/>
    ジョブが完了すると、バックアップ プロセスが停止されたことと、バックアップ データが削除されたことを示すメッセージがサービスから送信されます。

1. 一覧のアイテムを削除した後、**[バックアップ アイテム]** メニューの **[更新]** をクリックして、コンテナー内の残りのアイテムを確認します。

      ![[バックアップ データの削除]](./media/backup-azure-delete-vault/empty-items-list.png)

      一覧にアイテムがなくなったら、[Recovery Services コンテナー] メニューの **[要点]** ウィンドウまでスクロールします。 一覧には、**[バックアップ アイテム]**、**[バックアップ管理サーバー]**、**[レプリケートされたアイテム]** のどれも表示されないはずです。 コンテナーに引き続きアイテムが表示される場合は、手順 3. に戻り、別のアイテムの種類の一覧を選択します。  
1. コンテナーにアイテムがなくなったら、コンテナー ダッシュボードで **[削除]** をクリックします。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. コンテナーを削除することを確認するために、**[はい]** をクリックします。

    コンテナーが削除され、ポータルが **[新規]** サービス メニューに戻ります。

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>バックアップ プロセスを停止した一方でデータを保持した場合の対処

バックアップ プロセスを停止したものの、誤ってデータが "*保持*" されてしまった場合は、コンテナーを削除する前にバックアップ データを削除する必要があります。 バックアップ データを削除するには:

1. **[バックアップ アイテム]** メニューでアイテムを右クリックし、コンテキスト メニューで **[バックアップ データの削除]** をクリックします。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    **[バックアップ データを削除]** メニューが開きます。
1. **[バックアップ データの削除]** メニューでアイテムの名前を入力し、**[削除]** をクリックします。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/delete-retained-vault.png)

    データを削除したら、手順 4. の c. に戻り、プロセスを続行します。
