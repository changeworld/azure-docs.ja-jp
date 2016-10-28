<properties
	pageTitle="Recovery Services コンテナーへの Azure VM のバックアップ | Microsoft Azure"
	description="Azure 仮想マシン バックアップの手順で Azure 仮想マシンを検出および登録して Recovery Services コンテナーにバックアップします。"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="cfreeman"
	editor=""
	keywords="仮想マシンのバックアップ; バックアップ、仮想マシン; バックアップと障害復旧; ARM VM のバックアップ"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/29/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Recovery Services コンテナーへの Azure VM のバックアップ

> [AZURE.SELECTOR]
- [Recovery Services コンテナーへの VM のバックアップ](backup-azure-arm-vms.md)
- [バックアップ コンテナーへの VM のバックアップ](backup-azure-vms.md)

この記事では、Azure VM (Resource Manager デプロイとクラシック デプロイの両方) を Recovery Services コンテナーにバックアップする手順について説明します。VM をバックアップする作業の大半は準備です。VM をバックアップまたは保護する前に、[前提条件](backup-azure-arm-vms-prepare.md)の作業を行って、VM を保護するために環境を準備する必要があります。前提条件の作業が完了したら、バックアップ操作を開始して VM のスナップショットを作成できます。

>[AZURE.NOTE] Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。[Resource Manager デプロイメント モデルとクラシック デプロイメント モデル](../resource-manager-deployment-model.md)です。Resource Manager モデルでデプロイされた VM もクラシック モデルでデプロイされた VM も、Recovery Services コンテナーで保護することができます。クラシック デプロイメント モデル VM の操作方法の詳細については、「[Azure 仮想マシンのバックアップ](backup-azure-vms.md)」を参照してください。

詳細については、「[Azure における VM バックアップ インフラストラクチャの計画を立てる](backup-azure-vms-introduction.md)」と[Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) に関するページを参照してください。

## バックアップ ジョブのトリガー

Recovery Services コンテナーに関連付けられているバックアップ ポリシーでは、バックアップ操作を実行する頻度とタイミングを定義します。既定では、スケジュールされた最初のバックアップが初回バックアップとなります。初回バックアップが実行されるまで、**[バックアップ ジョブ]** ブレードの [前回のバックアップの状態] には、**[警告 (初回のバックアップが保留中)]** と表示されます。

![Backup pending](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

初回バックアップがすぐに開始される予定でない場合は、**[今すぐバックアップ]** を実行することをお勧めします。以下の手順は、コンテナー ダッシュボードから開始します。この手順では、すべての前提条件を完了した後で最初のバックアップ ジョブを実行します。最初のバックアップ ジョブが既に実行されている場合、この手順は使用できません。関連付けられているバックアップ ポリシーで、次のバックアップ ジョブが決定されます。

最初のバックアップ ジョブを実行するには:

1. コンテナー ダッシュボードの **[バックアップ]** タイルで、**[Azure Virtual Machines]** をクリックします。<br/> ![Settings icon](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    **[バックアップ項目]** ブレードが開きます。

2. **[バックアップ項目]** ブレードで、バックアップするコンテナーを右クリックし、**[今すぐバックアップ]** をクリックします。

    ![Settings icon](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    バックアップ ジョブがトリガーされます。<br/>

    ![Backup job triggered](./media/backup-azure-vms-first-look-arm/backup-triggered.png)

3. 初回バックアップが完了したことを確認するには、コンテナー ダッシュボードの **[バックアップ ジョブ]** タイルで **[Azure Virtual Machines]** をクリックします。

    ![Backup Jobs tile](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    [バックアップ ジョブ] ブレードが開きます。

4. **[バックアップ ジョブ]** ブレードでは、すべてのジョブの状態を確認できます。

    ![Backup Jobs tile](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

    >[AZURE.NOTE] Azure Backup サービスは、バックアップ操作の一部として、各仮想マシンのバックアップ拡張機能に対して、すべての書き込みをフラッシュし、整合性のあるスナップショットを作成するためのコマンドを発行します。

    バックアップ ジョブが完了すると、状態は *[完了]* になります。


## エラーのトラブルシューティング
仮想マシンのバックアップ中に問題が発生した場合は、[仮想マシンのトラブルシューティングに関する記事](backup-azure-vms-troubleshoot.md)を参照してください。

## 次のステップ

Vm の保護が済んだので、VM で実行できる追加管理タスクおよび VM の復元方法を以下の記事で確認してください。

- [仮想マシンの管理と監視](backup-azure-manage-vms.md)
- [仮想マシンの復元](backup-azure-arm-restore-vms.md)

<!---HONumber=AcomDC_0803_2016-->