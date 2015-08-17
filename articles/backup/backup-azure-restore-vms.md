
<properties
	pageTitle="Azure Backup - 仮想マシンの復元"
	description="Azure 仮想マシンを復元する方法について説明します。"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="07/17/2015" ms.author="trinadhk"; "jimpark"/>

# 仮想マシンの復元
復元操作を使用して、Azure バックアップ資格情報コンテナーに格納されているバックアップから新しい VM に仮想マシンを復元できます。

## 復元する項目を選択する

1. **[保護された項目]** タブに移動し、新しい VM に復元する仮想マシンを選択します。

    ![保護された項目](./media/backup-azure-restore-vms/protected-items.png)

    **[保護された項目]** ページの **[回復ポイント]** 列には、仮想マシンの回復ポイント数が表示されます。**[最新の回復ポイント]** 列には、仮想マシンを復元できる最新のバックアップ時刻が表示されます。

2. **[復元]** をクリックすると **[復元するアイテム]** ウィザードが開きます。

    ![項目を復元する](./media/backup-azure-restore-vms/restore-item.png)

## 回復ポイントを選択する

1. **[回復ポイントの選択]** 画面で、最新の回復ポイントまたは以前の特定の時点から復元できます。ウィザードが開いたときに選択されている既定のオプションは、*最新の回復ポイント*です。

    ![回復ポイントを選択する](./media/backup-azure-restore-vms/select-recovery-point.png)

2. 以前の特定の時点を選択するには、ドロップダウン リストで **[日付の選択]** オプションを選択し、**カレンダー アイコン**をクリックしてカレンダー コントロールから日付を選択します。コントロール内の回復ポイントが設定されているすべての日付は、淡い灰色の網掛けで表示され、選択可能です。

    ![日付を選択する](./media/backup-azure-restore-vms/select-date.png)

    カレンダー コントロールの日付をクリックすると、その日付の使用可能な回復ポイントが下の回復ポイント テーブルに表示されます。**[時間]** 列は、スナップショットが作成された日時を示します。**[種類]** 列は、回復ポイントの [整合性](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points)を示します。テーブル ヘッダーのかっこ内は、その日に使用可能な回復ポイントの数を示します。

    ![回復ポイント](./media/backup-azure-restore-vms/recovery-points.png)

3. **[回復ポイント]** テーブルから回復ポイントを選択し、次へ進む矢印をクリックして次の画面に移動します。

## コピー先の場所を指定する

1. **[Select restore instance]** 画面で、仮想マシンを復元する場所の詳細を指定します。

  - 仮想マシン名を指定する: 特定のクラウド サービスでは、仮想マシンの名前を一意にする必要があります。既存の仮想マシンを同じ名前に置き換える場合は、まず既存の仮想マシンとデータ ディスクを削除し、次に Azure バックアップからデータを復元します。
  - 仮想マシンのクラウド サービスを選択する: これは仮想マシンを作成するために必須です。既存のクラウド サービスを使用するか、新しいクラウド サービスを作成するかを選択できます。

        クラウド サービス名はグローバルに見て一意なものを選びます。一般にクラウド サービス名は、[cloudservice].cloudapp.net の形式の対外的な URL と関連付けられます。Azure では、新しいクラウドの名前がすでに使用されていると、クラウド サービスを作成できません。新しいクラウド サービスを作成する場合、選択された VM 名が関連クラウド サービスに適用できる一意の名前の場合には、仮想マシンと同じ名前が指定されます

        Azure では、リストア インスタンス詳細のどのアフィニティ グループに関連付けられていないクラウド サービスと仮想ネットワークのみが表示されます。[詳細](https://msdn.microsoft.com/ja-jp/library/azure/jj156085.aspx).

2. 仮想マシンのストレージ アカウントを選択する: これは仮想マシンを作成するために必須です。Azure Backup 資格情報コンテナーと同じリージョン内の既存のストレージ アカウントから選択できます。ゾーン冗長または Premium Storage タイプのストレージ アカウントはサポートされません。

    サポートされている構成のストレージ アカウントがない場合は、復元操作を開始する前に、サポートされている構成のストレージ アカウントを作成してください。

    ![仮想ネットワークを作成する](./media/backup-azure-restore-vms/restore-sa.png)

3. 仮想マシンを選択する: 仮想マシンの作成時に、仮想マシンの仮想ネットワーク (VNET) を選択する必要があります。復元 UI は、使用できるこのサブスクリプション内の VNET をすべて表示します。復元された仮想マシンの VNET を選択する必要はありません。VNET が適用されない場合でも、インターネット経由で復元された仮想マシンに接続できます。

    選択したクラウド サービスが仮想ネットワークに関連付けられている場合は、仮想ネットワークを変更できません。

    ![仮想ネットワークを作成する](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. サブネットを選択する: VNET にサブネットがある場合、既定では最初のサブネットが選択されます。ドロップダウン リストのオプションから、任意のサブネットを選択します。サブネットの詳細については、[ポータルのホーム ページ](https://manage.windowsazure.com/)の [Networks] 拡張機能から **[Virtual Networks]** に移動し、仮想ネットワークを選択して、[構成] をドリルダウンしてサブネットの詳細を表示します。

    ![サブネットを選択する](./media/backup-azure-restore-vms/select-subnet.png)

5. ウィザードの **[送信]** アイコンをクリックして詳細情報を送信し、復元ジョブを作成します。

## 復元操作を追跡する
復元ウィザードにすべての情報を入力して送信すると、Azure Backup は復元操作を追跡するジョブの作成を試みます。

![復元ジョブの作成](./media/backup-azure-restore-vms/create-restore-job.png)

ジョブの作成に成功すると、ジョブが作成されたことを示すトースト通知が表示されます。**[ジョブの表示]** ボタンをクリックすると、**[ジョブ]** タブに詳細情報が表示されます。

![作成された復元ジョブ](./media/backup-azure-restore-vms/restore-job-created.png)

復元操作が完了すると、**[ジョブ]** タブに完了マークが付けられます。

![復元ジョブの完了](./media/backup-azure-restore-vms/restore-job-complete.png)

仮想マシンを復元したら、元の仮想マシンにある拡張機能を再インストールし、Azure ポータルで仮想マシンの[エンドポイントを変更する](virtual-machines-set-up-endpoints)必要がある場合があります。

## エラーのトラブルシューティング
多くのエラーは、エラーの詳細に示す推奨される操作を実行できます。次にトラブルシューティングに役立つポイントをいくつか示します。

| バックアップ操作 | エラーの詳細 | 対処法 |
| -------- | -------- | -------|
| 復元 | クラウドの内部エラーの復元に失敗しました | <ol><li>復元を試みているクラウド サービスが DNS 設定で構成されています。<br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production" Get-AzureDns -DnsSettings $deployment.DnsSettings<br> を確認します。構成済みのアドレスがある場合は、DNS 設定が構成されていることを意味します。<br> <li>復元先のクラウド サービスに ReservedIP が構成されており、クラウド サービス内の既存 VM が停止状態になっています。<br>次の PowerShell コマンドレットを使用して、クラウド サービスに予約済み IP があることを確認できます。<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName</ol> |

## 次のステップ
- [仮想マシンの管理](backup-azure-manage-vms)

<!---HONumber=August15_HO6-->