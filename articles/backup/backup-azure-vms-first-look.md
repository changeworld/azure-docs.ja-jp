<properties
	pageTitle="Azure Backup を使用して Azure の VM を保護する | Microsoft Azure"
	description="Azure Backup サービスを使用して Azure VM を保護します。チュートリアルでは、Azure におけるコンテナーの作成、VM の登録、ポリシーの作成、VM の保護について説明します。"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/14/2016"
	ms.author="markgal; jimpark"/>


# 最初に: Azure 仮想マシンのバックアップ

この記事は、Azure 仮想マシン (VM) をバックアップする Azure 環境を準備するための一連の手順を説明するチュートリアルです。このチュートリアルでは、既に Azure サブスクリプション内に VM があることと、バックアップ サービスが VM にアクセスできるようにしてあることを前提としています。手順の概要は次のとおりです。

![High-level view of VM backup process](./media/backup-azure-vms-first-look/BackupAzureVM.png)


1. Azure サブスクリプションを作成するか、Azure サブスクリプションにサインインします。
2. バックアップ コンテナーを作成するか、*VM と同じリージョンにある*既存のバックアップ コンテナーを特定します。
3. Azure ポータルを使用して、仮想マシンを検索し、サブスクリプションに登録します。
4. 仮想マシンに VM エージェントをインストールします (Azure ギャラリーから作成した VM を使用している場合は、VM エージェントが既に存在します)。
5. 仮想マシンを保護するためのポリシーを作成します。
6. バックアップを実行します。

>[AZURE.NOTE] Azure には、リソースの作成と操作に関して 2 種類のデプロイ モデルがあります。[Resource Manager デプロイ モデルとクラシック デプロイ モデル](../resource-manager-deployment-model.md)です。現時点で、Azure Backup サービスは、Azure Resource Manager (ARM) ベースの仮想マシン (IaaS V2 仮想マシンとも呼ばれます) をサポートしていません。IaaS V2 VM は新しい Azure ポータルのリリースで導入されたため、このチュートリアルは Azure クラシック ポータルで作成できる種類の VM を使用するように設計されています。


## 手順 1. VM 用のバックアップ コンテナーの作成

バックアップ コンテナーは、経時的に作成されたすべてのバックアップと復旧ポイントを格納するエンティティです。バックアップ コンテナーには、バックアップ対象の仮想マシンに適用されるバックアップ ポリシーも含まれています。

次の図は、さまざまな Azure Backup エンティティの関係を示しています。![Azure Backup のエンティティとの関係](./media/backup-azure-vms-prepare/vault-policy-vm.png)

バックアップ資格情報コンテナーを作成するには:

1. [Azure ポータル](http://manage.windowsazure.com/)にサインインします。

2. Azure ポータルで、**[新規]**、**[Data Services]**、**[Recovery Services]**、**[バックアップ コンテナー]**、**[簡易作成]** の順にクリックします (下図を参照)。

    ![バックアップ資格情報コンテナーの作成](./media/backup-azure-vms-first-look/backup-vaultcreate.png)

3. **[名前]** ボックスに、コンテナーを識別するための表示名を入力します。名前は Azure サブスクリプションに対して一意である必要があります。2 ～ 50 文字の名前を入力します。名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。

4. **[リージョン]** ボックスで、コンテナーのリージョンを選択します。コンテナーは、保護する仮想マシンと同じリージョンにある**必要があります**。

    VM がどのリージョンに存在するかが不明な場合は、コンテナーを作成するダイアログを閉じて、ポータルで仮想マシンの一覧に移動します。仮想マシンが複数のリージョンにある場合は、各リージョンにバックアップ コンテナーを作成する必要があります。次の手順に進む前に、最初のリージョンでコンテナーを作成しておいてください。バックアップ データを格納するストレージ アカウントを指定する必要はありません。バックアップ資格情報コンテナーと Azure Backup サービスはこれを自動的に処理します。

5. **[サブスクリプション]** で、バックアップ コンテナーに関連付けるサブスクリプションを選択します。組織のアカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。

6. **[資格情報コンテナーの作成]** をクリックします。バックアップ資格情報コンテナーが作成されるまで時間がかかることがあります。ポータルの下部にある状態通知を監視します。

    ![資格情報コンテナーのトースト通知の作成](./media/backup-azure-vms-first-look/create-vault-demo.png)

    コンテナーが正常に作成されたことを確認するメッセージが表示されます。**[Recovery Services]** ページに、コンテナーが **[アクティブ]** と表示されます。

    ![資格情報コンテナーのトースト通知の作成](./media/backup-azure-vms-first-look/create-vault-demo-success.png)

7. **[Recovery Services]** ページのコンテナーの一覧で、作成したコンテナーをクリックして **[クイック スタート]** ページを起動します。

    ![バックアップ資格情報コンテナーの一覧](./media/backup-azure-vms-first-look/active-vault-demo.png)

8. **[クイック スタート]** ページで、**[構成]** をクリックして、[ストレージのレプリケーション] オプションを開きます。![バックアップ資格情報コンテナーの一覧](./media/backup-azure-vms-first-look/configure-storage.png)

9. **[ストレージのレプリケーション]** オプションで、コンテナーのレプリケーション オプションを選択します。

    ![バックアップ資格情報コンテナーの一覧](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    既定では、コンテナーには geo 冗長ストレージがあります。プライマリ バックアップ ストレージ エンドポイントとして Azure を使用している場合は、引き続き geo 冗長ストレージを使用することをお勧めします。プライマリ以外のバックアップ ストレージ エンドポイントとして Azure を使用している場合は、ローカル冗長ストレージを選択することも検討できます。ローカル冗長ストレージを選択すると、Azure にデータを格納するコストが削減されます。[geo 冗長](../storage/storage-redundancy.md#geo-redundant-storage)ストレージ オプションと[ローカル冗長](../storage/storage-redundancy.md#locally-redundant-storage)ストレージ オプションの詳細については、「[Azure Storage のレプリケーション](../storage/storage-redundancy.md)」を参照してください。

コンテナーのストレージ オプションを選択したら、VM をコンテナーに関連付けることができます。関連付けを開始するには、Azure 仮想マシンを検出して登録する必要があります。

## 手順 2. Azure 仮想マシンの検出と登録
VM をコンテナーに登録する前に、サブスクリプションに追加された新しい仮想マシンが特定されるように検出プロセスを実行してください。このプロセスでは、サブスクリプションに含まれる仮想マシンの一覧を、クラウド サービス名、リージョンなどの追加情報と共に Azure に照会します。

1. [Azure ポータル](http://manage.windowsazure.com/)にサインインします。

2. Azure クラシック ポータルで、**[Recovery Services]** をクリックして Recovery Services コンテナーの一覧を開きます。![Select workload](./media/backup-azure-vms-first-look/recovery-services-icon.png)

3. **Recovery Services** コンテナーの一覧で、VM のバックアップに使用するコンテナーを選択します。

    選択したコンテナーが、**[クイック スタート]** ページで開きます。

4. ページの上部にあるコンテナー メニューで、**[登録済みの項目]** をクリックします。

5. **[種類]** メニューの **[Azure 仮想マシン]** を選択します。

    ![Select workload](./media/backup-azure-vms/discovery-select-workload.png)

6. ページの下部にある **[検出]** をクリックします。![Discover button](./media/backup-azure-vms/discover-button-only.png)

    仮想マシンが集計されるまで、この検出プロセスに数分かかる場合があります。プロセスが実行中であることを知らせる通知が画面の下部に表示されます。

    ![Discover VMs](./media/backup-azure-vms/discovering-vms.png)

    プロセスが完了すると、通知が変更されます。

    ![Discovery done](./media/backup-azure-vms-first-look/discovery-complete.png)

7. ページの下部にある **[登録]** をクリックします。![Register button](./media/backup-azure-vms-first-look/register-icon.png)

8. **[項目の登録]** ショートカット メニューで、登録する仮想マシンを選択します。同じ名前の仮想マシンが 2 つ以上ある場合は、クラウド サービスを使用して仮想マシンを区別します。

    >[AZURE.TIP] 一度に複数の仮想マシンを登録することができます。

    選択した仮想マシンごとにジョブが作成されます。

9. 通知内の **[ジョブの表示]** をクリックして **[ジョブ]** ページに移動します。

    ![Register job](./media/backup-azure-vms/register-create-job.png)

    仮想マシンが登録済みの項目の一覧にも、登録操作の状態と共に表示されます。

    ![登録状態 1](./media/backup-azure-vms/register-status01.png)

    操作が完了すると、*登録済み*の状態を反映するように状態が変更されます。

    ![登録状態 2](./media/backup-azure-vms/register-status02.png)

## 手順 3. 仮想マシンへの VM エージェントのインストール

バックアップ拡張機能を動作させるには、Azure VM エージェントを Azure 仮想マシンにインストールする必要があります。VM を Azure ギャラリーから作成した場合、VM エージェントは既に仮想マシンに存在します。しかし、オンプレミスのデータセンターから移行された VM には、VM エージェントがインストールされていません。このような場合は、VM エージェントを明示的にインストールする必要があります。Azure VM のバックアップを実行する前に、Azure VM エージェントが仮想マシンに正しくインストールされていることを確認してください (以下の表を参照)。カスタム VM を作成している場合は、仮想マシンをプロビジョニングする前に、[**[VM エージェントのインストール]** チェック ボックスがオンになっていることを確認](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md)してください。

詳細については、「[VM エージェント](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)」と「[インストール方法](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md)」に関するページをご覧ください。

次の表に、Windows VM と Linux VM の VM エージェントに関する追加情報をまとめています。

| **操作** | **Windows** | **Linux** |
| --- | --- | --- |
| VM エージェントのインストール | <li>[エージェント MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) をダウンロードしてインストールします。インストールを実行するには、管理者特権が必要です。<li>[VM プロパティを更新](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)して、エージェントがインストールされていることを示します。 | <li>GitHub から最新の [Linux エージェント](https://github.com/Azure/WALinuxAgent)をインストールします。インストールを実行するには、管理者特権が必要です。<li>[VM プロパティを更新](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)して、エージェントがインストールされていることを示します。 |
| VM エージェントの更新 | VM エージェントを更新するには、単純に [VM エージェント バイナリ](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)を再インストールします。<br>VM エージェントの更新中にバックアップ操作が実行されないようにする必要があります。 | [Linux VM エージェントの更新](../virtual-machines-linux-update-agent.md)に関する手順に従います。<br>VM エージェントの更新中にバックアップ操作が実行されないようにする必要があります。 |
| VM エージェントのインストールの検証 | <li>Azure VM で *C:\\WindowsAzure\\Packages* フォルダーに移動します。<li>WaAppAgent.exe ファイルを探します。<li> このファイルを右クリックして、**[プロパティ]** をクリックした後、**[詳細]** タブを選択します。[製品バージョン] が 2.6.1198.718 以上であることを確認します。 | 該当なし |


### バックアップ拡張機能

VM エージェントが仮想マシンにインストールされると、Azure Backup サービスによって VM エージェントにバックアップ拡張機能がインストールされます。Azure Backup サービスは、バックアップ拡張機能のアップグレードと修正プログラムの適用をシームレスに自動実行します。

バックアップ拡張機能は、VM が実行されているかどうかにかかわらず、Backup サービスによってインストールされます。VM が実行されている場合は、アプリケーション整合性復旧ポイントを取得できる可能性が最も高くなります。Azure Backup サービスは、VM がオフになっている場合でも VM のバックアップを続行しますが、拡張機能はインストールされない可能性があります。これはオフライン VM と呼ばれます。この場合、復旧ポイントは、*クラッシュ整合性*復旧ポイントになります。


## 手順 4. Azure 仮想マシンの保護
この手順で、仮想マシンのバックアップおよび保持に関するポリシーを設定でます。1 回の保護操作で複数の仮想マシンを保護できます。2015 年 5 月以降に作成された Azure Backup コンテナーには、既定のポリシーが組み込まれています。この既定のポリシーには、30 日間の既定の保持期間と 1 日 1 回のバックアップ スケジュールが含まれています。

1. Azure ポータルの **[Recovery Services]** にあるバックアップ コンテナーに移動し、**[登録済みの項目]** をクリックします。
2. ドロップダウン メニューから **[Azure 仮想マシン]** を選択します。

    ![ポータルでのワークロードの選択](./media/backup-azure-vms/select-workload.png)

3. ページの下部にある **[保護]** をクリックします。![Click Protect](./media/backup-azure-vms-first-look/protect-icon.png)

    **項目の保護ウィザード**が表示され、登録済みで保護されていない仮想マシン*のみ*が一覧表示されます。

    ![保護をスケールで構成](./media/backup-azure-vms/protect-at-scale.png)

4. 保護する仮想マシンを選択します。

    同じ名前の仮想マシンが 2 つ以上ある場合は、クラウド サービスを使用して仮想マシンを区別します。

5. **[保護の構成]** で、特定した仮想マシンを保護するための既存のポリシーを選択するか、新しいポリシーを作成します。

    各バックアップ ポリシーには、複数の仮想マシンを関連付けることができます。ただし、仮想マシンは常に 1 つのポリシーにしか関連付けることができません。

    ![新しいポリシーで保護](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] バックアップ ポリシーには、スケジュールされたバックアップの保持スキーマが含まれています。既存のバックアップ ポリシーを選択した場合は、次の手順で保持期間オプションを変更することができません。

6. **[リテンション期間]** で、特定のバックアップ ポイントの日、週、月、および年単位の期間を定義します。

    ![フレキシブルな保持期間で保護](./media/backup-azure-vms/policy-retention.png)

    バックアップを保存する期間は保持ポリシーで指定します。バックアップが作成されたタイミングに応じて異なる保持ポリシーを指定することができます。たとえば、各四半期の最後に実行されるバックアップ ポイントは、(監査のために) 長期間保持する必要がありますが、毎日実行されるバックアップ ポイントは、運用上の復旧ポイントとして機能するため、保持は 90 日間だけで済みます。

    ![復旧ポイントを作成した後の仮想マシンのバックアップ](./media/backup-azure-vms/long-term-retention.png)

    この画像の例の場合:

    - **日ごとの保持ポリシー**: バックアップは毎日作成され、30 日間保持されます。
    - **週ごとの保持ポリシー**: バックアップは毎週日曜日に作成され、104 週間保持されます。
    - **月ごとの保持ポリシー**: バックアップは各月の最後の日曜日に作成され、120 か月保持されます。
    - **年ごとの保持ポリシー**: バックアップは 1 月の最初の日曜日に作成され、99 年間保持されます。

    選択した仮想マシンごとに、保護ポリシーを構成して仮想マシンをポリシーに関連付けるためのジョブが作成されます。

6. **[ジョブ]** をクリックし、適切なフィルターを選択して、**保護の構成**ジョブの一覧を表示します。

    ![保護の構成ジョブ](./media/backup-azure-vms/protect-configureprotection.png)


## 手順 5. 初回バックアップ

仮想マシンをポリシーを使用して保護すると、その関係が **[保護された項目]** タブに表示されます。VM の初回バックアップが行われるまで、**[保護の状態]** には **[保護済み (初回のバックアップが保留中)]** と表示されます。既定では、スケジュールされた最初のバックアップが*初回バックアップ*となります。

![Backup pending](./media/backup-azure-vms-first-look/protection-pending-border.png)

保護を構成した直後に初回バックアップをトリガーするには:

1. **[保護された項目]** ページの下部にある **[今すぐバックアップ]** ボタンをクリックします。![Backup Now icon](./media/backup-azure-vms-first-look/backup-now-icon.png)

    Azure Backup サービスによって、初回バックアップ操作用にバックアップ ジョブが作成されます。

2. **[ジョブ]** タブをクリックしてジョブの一覧を表示します。

    ![バックアップが進行中](./media/backup-azure-vms-first-look/protect-inprogress.png)

    >[AZURE.NOTE] Azure Backup サービスは、バックアップ操作の一部として、各仮想マシンのバックアップ拡張機能に対して、すべての書き込みをフラッシュし、整合性のあるスナップショットを作成するためのコマンドを発行します。

    初回バックアップが完了すると、**[保護された項目]** タブの仮想マシンの状態が *[保護済み]* になります。

    ![復旧ポイントを作成した後の仮想マシンのバックアップ](./media/backup-azure-vms/protect-backedupvm.png)

    >[AZURE.NOTE] 仮想マシンのバックアップはローカルな処理です。あるリージョンの仮想マシンを別のリージョンのバックアップ コンテナーにバックアップすることはできません。そのため、バックアップが必要な VM がある Azure リージョンごとに、そのリージョン内に少なくとも 1 つのバックアップ コンテナーを作成する必要があります。

## 次のステップ
これで、VM が正常にバックアップされました。さらに、関心がありそうなステップがいくつかあります。最も必然的なステップは、VM へのデータの復元を理解することですが、データを安全に保持し、コストを削減する方法を理解するために役立つ管理タスクもあります。

- [仮想マシンの管理と監視](backup-azure-manage-vms.md)
- [仮想マシンの復元](backup-azure-restore-vms.md)
- [トラブルシューティング ガイダンス](backup-azure-vms-troubleshoot.md)


## 疑問がある場合
ご不明な点がある場合や今後搭載を希望する機能がある場合は、[フィードバックをお送りください](http://aka.ms/azurebackup_feedback)。

<!---HONumber=AcomDC_0323_2016-->