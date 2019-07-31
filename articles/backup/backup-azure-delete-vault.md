---
title: Azure の Recovery Services コンテナーを削除する
description: Recovery Services コンテナーを削除する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: f98b9a02d12cc53ba23857b203ee3eaed9dd7cfa
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466657"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services コンテナーを削除する

この記事では、[Azure Backup](backup-overview.md) Recovery Services コンテナーを削除する方法について説明します。 依存関係を削除してからコンテナーの削除を行うための手順が含まれています。


## <a name="before-you-start"></a>開始する前に

コンテナーに関連付けられている保護されたサーバーやバックアップ管理サーバーなどの依存関係を持つ Recovery Services コンテナーを削除することはできません。<br/>
バックアップ データを含むコンテナーを削除することはできません (つまり、保護を停止したけれどもバックアップデータを保持している場合であっても)。

依存関係が含まれるコンテナーを削除すると、次のエラーが表示されます。

![コンテナーのエラーを削除する](./media/backup-azure-delete-vault/error.png)

コンテナーを正常に削除するには、ご使用のセットアップに一致するシナリオを選択し、推奨される手順に従います。

シナリオ | コンテナーを削除するために依存関係を削除する手順 |
-- | --
Azure への Azure Backup エージェント (MARS) バックアップを使用して保護されている、オンプレミスのファイルとフォルダーがある | 「バックアップ データとバックアップ アイテムを削除する」の「[MARS エージェントの場合](#for-mars-agent)」にある手順を実行します
MABS (Microsoft Azure Backup Server) または Azure への DPM (System Center Data Protection Manager) を使用して保護されたオンプレミスのマシンがある | 「バックアップ データとバックアップ アイテムを削除する」の「[MABS エージェントの場合](#for-mabs-agent)」にある手順を実行します
保護されたアイテムがクラウドにある (例: laaS VM、Azure ファイル共有など)  | 「バックアップ データとバックアップ アイテムを削除する」の「[クラウドの保護されたアイテムの場合](#for-protected-items-in-cloud)」にある手順を実行します
保護されたアイテムがオンプレミスとクラウドの両方にある | 「バックアップ データとバックアップ アイテムを削除する」の手順を次の順番で実行します: <br> - [クラウドの保護されたアイテムの場合](#for-protected-items-in-cloud)<br> - [MABS エージェントの場合](#for-mars-agent) <br> - [MABS エージェントの場合](#for-mabs-agent)
保護されたアイテムはオンプレミスまたはクラウドのどちらにもないが、コンテナーの削除エラーが引き続き発生している | 「[Azure Resource Manager クライアントを使用して Recovery Services コンテナーを削除する](#delete-the-recovery-services-vault-using-azure-resource-manager-client)」の手順を実行します
元のオンプレミス サーバーがなくなり (紛失/使用停止)、Recovery Services コンテナーを削除したい | Microsoft サポートにお問い合わせください

## <a name="delete-backup-data-and-backup-items"></a>バックアップ データとバックアップ アイテムを削除する

作業を進める前に、 **[この](#before-you-start)** セクションを読み、依存関係とコンテナーの削除プロセスを理解してください。

### <a name="for-protected-items-in-cloud"></a>クラウドの保護されたアイテムの場合

保護を停止してバックアップ データを削除するには、以下の手順を実行します。

1. ポータル > [Recovery Services コンテナー] > [バックアップ アイテム] から、クラウドの保護されたアイテムを選択します。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. 各アイテムについて、右クリックして **[バックアップの停止]** を選択する必要があります。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/stop-backup-item.png)

3. **[バックアップの停止]**  >  **[オプションの選択]** の順に進み、 **[バックアップ データの削除]** を選択します。
4. アイテムの名前を入力し、 **[バックアップの停止]** をクリックします。
   - これで、コンテナーの削除を希望していることが確認されます。
   - 確認後、 **[バックアップの停止]** ボタンがアクティブになります。
   - データを保持し、データを削除しない場合、コンテナーを削除することはできません。

     ![[バックアップ データの削除]](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. **[通知]** ![バックアップデータの削除](./media/backup-azure-delete-vault/messages.png)を確認します。 完了すると、サービスにより次のメッセージが表示されます: **バックアップを停止し、"*バックアップ アイテム*" のバックアップ データを削除しています**。 **操作は正常に完了しました**。
6. **[バックアップ アイテム]** メニューで **[更新]** をクリックして、バックアップ アイテムが削除されたかを確認します。

      ![[バックアップ データの削除]](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>MARS エージェントの場合

保護を停止してバックアップ データを削除するには、次に示す順番で手順を実行します。

- [手順 1:MARS 管理コンソールからバックアップ アイテムを削除する](#step-1-delete-backup-items-from-mars-management-console)
- [手順 2:ポータルから Azure Backup エージェントを削除する](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>手順 1:MARS 管理コンソールからバックアップ アイテムを削除する

サーバーが利用できないためにこの手順を実行できない場合は、Microsoft サポートにお問い合わせください。

- MARS 管理コンソールを起動し、 **[操作]** ウィンドウに移動して **[バックアップのスケジュール]** を選択します。
- **[スケジュールされたバックアップの変更または停止]** ウィザードで、オプション **[このバックアップ スケジュールの使用を中止して、保存されているバックアップをすべて削除する]** を選択し、 **[次へ]** をクリックします。

    ![スケジュールされたバックアップの変更または停止](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- **[スケジュールされたバックアップの変更または停止]** ウィザードで **[完了]** をクリックします。

    ![スケジュールされたバックアップの停止](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- セキュリティ PIN の入力を求められます。 PIN を生成するには、次の手順を実行します。
  - Azure ポータルにサインインします。
  - **[Recovery Services コンテナー]**  >  **[設定]**  >  **[プロパティ]** の順に参照します。
  - **[セキュリティ PIN]** の下にある **[生成]** をクリックします。 この PIN をコピーします (この PIN は 5 分間だけ有効です)。
- 管理コンソール (クライアント アプリ) に PIN を貼り付けて **[OK]** をクリックします。

  ![セキュリティ PIN](./media/backup-azure-delete-vault/security-pin.png)

- **[バックアップの進行状況の変更]** ウィザードに "*削除されたバックアップ データは 14 日間保持されます。14 日が経過すると、バックアップ データが完全に削除されます。* " と表示されます。  

    ![バックアップ インフラストラクチャを削除する](./media/backup-azure-delete-vault/deleted-backup-data.png)

オンプレミスからバックアップ アイテムを削除したので、ポータルから次の手順を実行します。

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>手順 2:ポータルから Azure Backup エージェントを削除する

次に進む前に、[手順 1](#step-1-delete-backup-items-from-mars-management-console) が完了していることを確認します。

1. コンテナーのダッシュボード メニューの **[インフラストラクチャのバックアップ]** をクリックします。
2. インフラストラクチャ サーバーを表示するには、 **[保護されたサーバー]** をクリックします。

    ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. **[保護されたサーバー]** の一覧で、[Azure Backup エージェント] をクリックします。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Azure Backup エージェントを使用して保護されているサーバーの一覧内でサーバーをクリックします。

    ![特定の保護されたサーバーを選択する](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. 選択したサーバーのダッシュボードで、 **[削除]** をクリックします。

    ![選択したサーバーを削除する](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. **[削除]** メニューで、サーバーの名前を入力し、 **[削除]** をクリックします。

     ![[バックアップ データの削除]](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> 次のエラーが表示された場合は、まず「[管理コンソールからバックアップ項目を削除する](#step-1-delete-backup-items-from-mars-management-console)」に記載されている手順を実行します。
>
>![削除の失敗](./media/backup-azure-delete-vault/deletion-failed.png)
>
>管理コンソールでサーバーを使用できないなどの理由で管理コンソールからバックアップを削除する手順を実行できない場合は、Microsoft サポートにお問い合わせください。

7. **[通知]** ![バックアップデータの削除](./media/backup-azure-delete-vault/messages.png)を確認します。 完了すると、サービスにより次のメッセージが表示されます: **バックアップを停止し、"*バックアップ アイテム*" のバックアップ データを削除しています**。 **操作は正常に完了しました**。
8. **[バックアップ アイテム]** メニューで **[更新]** をクリックして、バックアップ アイテムが削除されたかを確認します。


### <a name="for-mabs-agent"></a>MABS エージェントの場合

保護を停止してバックアップ データを削除するには、次に示す順番で手順を実行します。

- [手順 1:MABS 管理コンソールからバックアップ アイテムを削除する](#step-1-delete-backup-items-from-mabs-management-console)
- [手順 2:ポータルから Azure Backup 管理サーバーを削除する](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>手順 1:MABS 管理コンソールからバックアップ アイテムを削除する

サーバーが利用できないためにこの手順を実行できない場合は、Microsoft サポートにお問い合わせください。

**方法 1** 保護を停止してバックアップ データを削除するには、以下の手順を実行します。

1.  DPM 管理者コンソールで、ナビゲーション バーの **[保護]** をクリックします。
2.  表示ウィンドウで、削除する保護グループのメンバーを選択します。 右クリックして **[Stop Protection of Group Members]\(グループ メンバーの保護を停止する\)** オプションを選択します。
3.  **[保護の停止]** ダイアログ ボックスで、 **[保護されるデータを削除する]**  >  **[オンライン記憶域を削除する]** チェックボックスをオンにして、 **[保護の停止]** をクリックします。

    ![オンラインでストレージを削除](./media/backup-azure-delete-vault/delete-storage-online.png)

保護されたメンバーの状態は、"**利用可能な非アクティブなレプリカ**" に変わりました。

5. 非アクティブな保護グループを右クリックして、 **[非アクティブな保護の削除]** を選択します。

    ![非アクティブな保護の削除](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. **[非アクティブな保護の削除]** ウィンドウで **[オンライン記憶域を削除する]** を選択して、 **[OK]** をクリックします。

    ![ディスクとオンライン上のレプリカを削除する](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**方法 2** **MABS 管理**コンソールを起動します。 **[データの保護方法の選択]** セクションで、 **[オンライン保護を利用する]** をオフにします。

  ![データ保護方法の選択](./media/backup-azure-delete-vault/data-protection-method.png)

オンプレミスからバックアップ アイテムを削除したので、ポータルから次の手順を実行します。

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>手順 2:ポータルから Azure Backup 管理サーバーを削除する

次に進む前に、[手順 1](#step-1-delete-backup-items-from-mabs-management-console) が完了していることを確認します。

1. コンテナーのダッシュボード メニューの **[インフラストラクチャのバックアップ]** をクリックします。
2. **[バックアップ管理サーバー]** をクリックしてサーバーを確認します。

    ![コンテナーを選択してダッシュボードを選択する](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. アイテムを右クリックして、 **[削除]** をクリックします。
4. **[削除]** メニューで、サーバーの名前を入力し、 **[削除]** をクリックします。

     ![[バックアップ データの削除]](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> 次のエラーが表示された場合は、まず「[管理コンソールからバックアップ項目を削除する](#step-2-from-portal-remove-azure-backup-management-servers)」に記載されている手順を実行します。
>
>![削除の失敗](./media/backup-azure-delete-vault/deletion-failed.png)
>
> 管理コンソールでサーバーを使用できないなどの理由で管理コンソールからバックアップを削除する手順を実行できない場合は、Microsoft サポートにお問い合わせください。

5. **[通知]** ![バックアップデータの削除](./media/backup-azure-delete-vault/messages.png)を確認します。 完了すると、サービスにより次のメッセージが表示されます: **バックアップを停止し、"*バックアップ アイテム*" のバックアップ データを削除しています**。 **操作は正常に完了しました**。
6. **[バックアップ アイテム]** メニューで **[更新]** をクリックして、バックアップ アイテムが削除されたかを確認します。


## <a name="delete-the-recovery-services-vault"></a>Recovery Services コンテナーを削除する

1. すべての依存関係が削除されたら、コンテナー メニュー内の **[Essentials]** ウィンドウにスクロールします。
2. 一覧に **[バックアップ アイテム]** 、 **[バックアップ管理サーバー]** 、 **[レプリケートされたアイテム]** のどれも表示されないことを確認します。 コンテナー内にまだアイテムが表示されている場合は、[それを削除します](#delete-backup-data-and-backup-items)。

3. コンテナーにアイテムがなくなったら、コンテナー ダッシュボードで **[削除]** をクリックします。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. コンテナーを削除することを確認するために、 **[はい]** をクリックします。 コンテナーが削除され、ポータルが **[新規]** サービス メニューに戻ります。

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Azure Resource Manager クライアントを使用して Recovery Services コンテナーを削除する

Recovery Services コンテナーを削除するこのオプションは、すべての依存関係が削除されても、*コンテナーの削除エラー*が引き続き発生する場合にのみ、お勧めします。



- コンテナー メニューの **[基本]** ウィンドウから、一覧に **[バックアップ アイテム]** 、 **[バックアップ管理サーバー]** 、 **[レプリケートされたアイテム]** のどれも表示されないことを確認します。 バックアップ アイテムがある場合は、「[バックアップ データとバックアップ アイテムを削除する](#delete-backup-data-and-backup-items)」にある手順を実行します。
- [ポータルからのコンテナーの削除](#delete-the-recovery-services-vault)を再試行します。
- すべての依存関係が削除されても、*コンテナーの削除エラー*が引き続き発生する場合は、ARMClient ツールを使用して次の手順を実行します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. [ここ](https://chocolatey.org/)から Chocolatey をインストールして、ARMClient をインストールするために、以下のコマンドを実行します。

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Azure アカウントにサインインして、次のコマンドを実行します。

    `ARMClient.exe login [environment name]`

3. Azure portal で、削除するコンテナーのサブスクリプション ID とリソース グループ名を収集します。

ARMClient コマンドの詳細については、この[ドキュメント](https://github.com/projectkudu/ARMClient/blob/master/README.md)を参照してください。

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Azure Resource Manager クライアントを使用して Recovery Services コンテナーを削除する

1. ご利用のサブスクリプション ID、リソース グループ名、コンテナー名を使用して、次のコマンドを実行します。 このコマンドを実行すると、依存関係がなければ、そのコンテナーが削除されます。

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. コンテナーが空ではない場合、"内部にリソースが存在するため、コンテナーを削除できません" という内容のエラーが発生します。 コンテナー内にある保護されたアイテムまたはコンテナーを削除するには、次の操作を行います。

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure portal で、値が削除されていることを確認します。


## <a name="next-steps"></a>次の手順

Recovery Services コンテナーの[詳細情報](backup-azure-recovery-services-vault-overview.md)。<br/>
Recovery Services コンテナーの監視と管理の[詳細情報](backup-azure-manage-windows-server.md)。
