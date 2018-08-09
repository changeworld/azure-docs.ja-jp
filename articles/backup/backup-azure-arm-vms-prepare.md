---
title: 'Azure Backup: 仮想マシンをバックアップする準備'
description: Azure で仮想マシンをバックアップする環境が整っていることを確認します。
services: backup
author: markgalioto
manager: carmonm
keywords: バックアップ, バックアップする,
ms.service: backup
ms.topic: conceptual
ms.date: 6/21/2018
ms.author: markgal
ms.openlocfilehash: 40a83b93443ebe1482f89a114505a1ba27b93bd2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445745"
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Resource Manager でデプロイされた仮想マシンをバックアップする環境の準備

この記事では、Azure Resource Manager でデプロイされた仮想マシン (VM) をバックアップできるように環境を準備する手順について説明します。 手順の各ステップでは、Azure ポータルを使用します。 仮想マシンをバックアップするときに、バックアップ データまたは回復ポイントは Recovery Services コンテナーに格納されます。 Recovery Services コンテナーには、クラシック仮想マシンおよび Resource Manager でデプロイされた仮想マシンのバックアップ データが格納されます。

> [!NOTE]
> Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。[Resource Manager デプロイメント モデルとクラシック デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md)です。

Resource Manager でデプロイされた仮想マシンを保護 (またはバックアップ) する前に、次の前提条件を満たしておく必要があります。

* *使用している仮想マシンと同じリージョンに* Recovery Services コンテナーを作成または識別します。
* シナリオを選択し、バックアップ ポリシーを定義し、保護する項目を定義します。
* 仮想マシンに VM エージェント (拡張機能) がインストールされていることを確認します。
* ネットワーク接続を確認します。
* Linux VM の場合、アプリケーション整合性バックアップのバックアップ環境をカスタマイズする場合は、[プリスナップショット スクリプトおよびポストスナップショット スクリプトを構成するための手順](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)を実行してください。

これらの条件が既に環境内で満たされている場合は、[VM のバックアップに関する記事](backup-azure-arm-vms.md)に進んでください。 これらの前提条件のいずれかをセットアップまたは確認する必要がある場合は、この記事の手順を参照してください。

## <a name="supported-operating-systems-for-backup"></a>バックアップでサポートされるオペレーティング システム

 * **Linux**: Azure Backup は、[Azure で承認されている一連のディストリビューション](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をサポートしています (Core OS Linux を除く)。 復元ファイルをサポートする Linux オペレーティング システムの一覧については、[仮想マシンのバックアップからのファイルの復元](backup-azure-restore-files-from-vm.md#for-linux-os)に関する記事を参照してください。

    > [!NOTE] 
    > 他の個人所有の Linux ディストリビューションは、仮想マシン上で VM エージェントが動作し、かつ Python がサポートされていれば使用できます。 ただし、それらのディストリビューションはサポートされません。
    >
 * **Windows Server**、**Windows client**: Windows Server 2008 R2 または Windows 7 より前のバージョンはサポートされていません。


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>VM のバックアップと復元に関する制限
環境を準備する前に、制限事項を把握してください。

* データ ディスクを 16 個よりも多く搭載した仮想マシンのバックアップはサポートされません。
* 予約済み IP アドレスはあるがエンドポイントが定義されていない仮想マシンのバックアップはサポートされません。
* Linux Unified Key Setup (LUKS) 暗号化を使用して暗号化された Linux VM のバックアップはサポートされていません。
* クラスターの共有ボリューム (CSV) またはスケールアウト ファイル サーバー構成を含む VM のバックアップはお勧めしません。 行った場合、CSV ライターの障害が発生します。 スナップショット タスク中は、クラスター構成に含まれるすべての VM が必要になります。 Azure Backup では、マルチ VM 整合性をサポートしていません。 
* バックアップ データには、ネットワーク経由でマウントされて VM に接続されているドライブは含まれません。
* 復元中に既存の仮想マシンを置き換えることはサポートされません。 VM が存在している場合に VM の復元を試みると、復元操作は失敗します。
* リージョン間のバックアップと復元はサポートされません。
* バックアップを構成するときに、ストレージ アカウントの**ファイアウォールと仮想ネットワーク**の設定で、すべてのネットワークからのアクセスが許可されていることを確認してください。
* 選択したネットワークについて、ファイアウォールと仮想ネットワークの設定を構成した後、例外として **[信頼された Microsoft サービスによるこのストレージアカウントに対するアクセスを許可します]** を選択し、Azure Backup サービスからネットワーク制限付きストレージ アカウントにアクセスできるようにします。 ネットワーク制限付きストレージ アカウントでは、アイテム レベルの回復はサポートされていません。
* Azure のすべてのパブリック リージョンに仮想マシンをバックアップすることができます (サポートされているリージョンの[チェックリスト](https://azure.microsoft.com/regions/#services)を参照してください)。目的のリージョンが現在サポートされていない場合は、資格情報コンテナーの作成時にドロップダウン リストに表示されません。
* マルチ DC 構成の一部であるドメイン コントローラー (DC) VM の復元は、PowerShell を通じてのみサポートされます。 詳細については、[マルチ DC ドメイン コントローラーの復元](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)に関するページを参照してください。
* 書き込みアクセラレータを有効にしたディスクでのスナップショットは、サポートされていません。 この制限により、仮想マシンのすべてのディスクのアプリケーション整合スナップショットを実行する Azure Backup サービスの機能はブロックされます。
* 次のような特殊なネットワーク構成を持つ仮想マシンの復元は、PowerShell でのみサポートされています。 復元操作の完了後、UI の復元ワークフローを使用して作成された VM には、これらのネットワーク構成は含まれません。 詳細については、「 [特別なネットワーク構成を持つ VM の復元](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)」を参照してください。
  * ロード バランサー構成 (内部および外部の) での仮想マシン
  * 複数の予約済み IP アドレスを持つ仮想マシン
  * 複数のネットワーク アダプターを持つ仮想マシン

## <a name="create-a-recovery-services-vault-for-a-vm"></a>VM 用の Recovery Services コンテナーを作成する
Recovery Services コンテナーは、経時的に作成されたバックアップと復旧ポイントを格納するエンティティです。 Recovery Services コンテナーには、保護される仮想マシンに関連付けられたバックアップ ポリシーも含まれます。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. **[ハブ]** メニューの **[参照]** をクリックし、「**Recovery Services**」と入力します。 入力を開始すると、リソースのリストが絞り込まれます。 **[Recovery Services コンテナー]** を選択します。

    ![ボックスに入力し、結果の [Recovery Services コンテナー] を選択します](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Recovery Services コンテナーの一覧が表示されます。
1. **[Recovery Services コンテナー]** メニューの **[追加]** を選択します。

    ![Create Recovery Services Vault step 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    **[Recovery Services コンテナー]** ウィンドウが開きます。 **[名前]**、**[サブスクリプション]**、**[リソース グループ]**、**[場所]** の情報を入力するように求められます。

    ![[Recovery Services コンテナー] ウィンドウ](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
1. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 ～ 50 文字の名前を入力します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。
1. **[サブスクリプション]** を選択して、使用可能なサブスクリプションの一覧を表示します。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (または推奨) のサブスクリプションを使用してください。 職場または学校アカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。
1. **[リソース グループ]** を選択して、使用可能なリソース グループを表示するか、**[新規]** を選択して、新しいリソース グループを作成します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。
1. **[場所]** を選択して、コンテナーの地理的リージョンを選択します。 コンテナーは、保護する仮想マシンと同じリージョンにある *必要があります* 。

   > [!IMPORTANT]
   > VM がどの場所に存在するかが不明な場合は、コンテナーを作成するダイアログ ボックスを閉じて、ポータルで仮想マシンの一覧に移動します。 複数のリージョンに仮想マシンがある場合は、各リージョンで Recovery Services コンテナーを作成する必要があります。 最初の場所でコンテナーを作成してから、次の場所に移動してください。 バックアップ データを格納するためにストレージ アカウントを指定する必要はありません。 Recovery Services コンテナーと Azure Backup サービスで自動的に処理されます。
   >
   >

1. **作成**を選択します。 Recovery Services コンテナーの作成に時間がかかることがあります。 ポータルの右上の領域で、状態の通知を監視します。 コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。 コンテナーが表示されない場合は、**[最新の情報に更新]** を選択します。

    ![バックアップ コンテナーの一覧](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

これで、コンテナーが作成されました。次は、ストレージ レプリケーションを設定する方法について説明します。

## <a name="set-storage-replication"></a>ストレージ レプリケーションの設定
ストレージ レプリケーション オプションでは、geo 冗長ストレージとローカル冗長ストレージのどちらかを選択できます。 既定では、コンテナーには geo 冗長ストレージがあります。 プライマリ バックアップの場合、オプション設定は geo 冗長ストレージのままにします。 耐久性を犠牲にしても低コストなバックアップが必要な場合は、ローカル冗長ストレージを選択します。

ストレージ レプリケーション設定を編集するには、次の手順を実行します。

1. **[Recovery Services コンテナー]** ウィンドウでコンテナーを選択します。
    コンテナーを選択すると、[設定] ウィンドウ (**上部にコンテナー名が表示**) とコンテナーの詳細ウィンドウが開きます。

   ![バックアップ コンテナーの一覧からコンテナーを選択します](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

1. **[設定]** ウィンドウで、垂直スライダーを使って下へスクロールして **[管理]** セクションに移動し、**[バックアップ インフラストラクチャ]** を選択します。 **[全般]** セクションで **[バックアップ構成]** を選択します。 **[バックアップ構成]** ウィンドウで、コンテナーのストレージ レプリケーション オプションを選択します。 既定では、コンテナーには geo 冗長ストレージがあります。

   ![バックアップ コンテナーの一覧](./media/backup-azure-arm-vms-prepare/full-blade.png)

   プライマリ バックアップ ストレージ エンドポイントとして Azure を使用している場合は、引き続き geo 冗長ストレージを使用します。 プライマリ以外のバックアップ ストレージ エンドポイントとして Azure を使用している場合は、ローカル冗長ストレージを選択します。 ストレージ オプションの詳細については、[Azure Storage のレプリケーションの概要](../storage/common/storage-redundancy.md)に関する記事を参照してください。

1. ストレージのレプリケーション タイプを変更するには、**[保存]** を選択します。
    
コンテナーのストレージ オプションを選択したら、VM をコンテナーに関連付けることができます。 関連付けを開始するには、Azure 仮想マシンを検出して登録する必要があります。

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>バックアップの目標を選択し、ポリシーを設定し、保護する項目の定義する
仮想マシンを Recovery Services コンテナーに登録する前に、検出プロセスを実行して、サブスクリプションに追加されたすべての新しい仮想マシンを特定します。 検出プロセスでは、サブスクリプションに含まれる仮想マシンの一覧を Azure に照会します。 新しい仮想マシンが見つかった場合、クラウド サービス名と関連付けられているリージョンがポータルに表示されます。 Azure Portal の "*シナリオ*" は、Recovery Services コンテナーに何を格納するのかを示しています。 *ポリシー*は、復旧ポイントを作成する頻度と時期のスケジュールです。 ポリシーには、復旧ポイントの保持期間も含まれます。

1. 既に Recovery Services コンテナーが開かれている場合は、手順 2. に進みます。 Recovery Services コンテナーを開いていない場合は、[Azure Portal](https://portal.azure.com/) を開きます。 **[ハブ]** メニューで **[その他のサービス]** を選択します。

   a. リソース ボックスに「 **Recovery Services**」と入力します。 入力を開始すると、リストが絞り込まれます。 **[Recovery Services コンテナー]** が表示されたら、それを選択します。

      ![ボックスに入力し、結果の [Recovery Services コンテナー] を選択します](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Recovery Services コンテナーの一覧が表示されます。 サブスクリプション内にコンテナーがない場合、この一覧は空になります。

      ![View of the Recovery Services vaults list](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Recovery Services コンテナーの一覧で、コンテナーを選択します。

      **[設定]** ウィンドウと、選択したコンテナーのコンテナー ダッシュボードが開きます。

      ![[設定] ウィンドウとコンテナー ダッシュボード](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
1. コンテナー ダッシュボード メニューで **[バックアップ]** を選択します。

   ![[バックアップ] ボタン](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **[バックアップ]** ウィンドウと **[バックアップの目標]** ウィンドウが開きます。

1. **[バックアップの目標]** ウィンドウで、**[ワークロードはどこで実行されていますか?]** を **[Azure]** に、**[何をバックアップしますか?]** を **[仮想マシン]** に設定します。 **[OK]** をクリックします。

   ![[バックアップ] ウィンドウと [バックアップの目標] ウィンドウ](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   この手順で、VM 拡張機能がコンテナーに登録されます。 **[バックアップの目標]** ウィンドウが閉じ、**[バックアップ ポリシー]** ウィンドウが開きます。

   ![[バックアップ] ウィンドウと [バックアップ ポリシー] ウィンドウ](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
1. **[バックアップ ポリシー]** ウィンドウで、コンテナーに適用するバックアップ ポリシーを選択します。

   ![Select backup policy](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   既定のポリシーの詳細がドロップダウン メニューの下に一覧表示されます。 新しいポリシーを作成する場合は、ドロップダウン メニューの **[新規作成]** を選択します。 バックアップ ポリシーを定義する手順については、「 [バックアップ ポリシーの定義](backup-azure-vms-first-look-arm.md#defining-a-backup-policy)」を参照してください。
    **[OK]** を選択して、バックアップ ポリシーをコンテナーに関連付けます。

   **[バックアップ ポリシー]** ウィンドウが閉じ、**[仮想マシンの選択]** ウィンドウが開きます。
1. **[仮想マシンの選択]** ウィンドウで、指定したポリシーに関連付ける仮想マシンを選択し、**[OK]** を選択します。

   ![[仮想マシンの選択] ウィンドウ](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   選択した仮想マシンが検証されます。 期待された仮想マシンが表示されない場合は、その仮想マシンが、Recovery Services コンテナーと同じ Azure リージョンに存在することを確認します。 まだ仮想マシンが表示されない場合は、それらが他のコンテナーで保護されていないことを確認します。 コンテナー ダッシュボードには、Recovery Services コンテナーのリージョンが表示されます。

1. コンテナーの設定をすべて定義したところで、**[バックアップ]** ウィンドウで、**[バックアップの有効化]** を選択します。 この手順で、ポリシーがコンテナーと VM にデプロイされます。 この手順では、仮想マシンの最初の復旧ポイントは作成されません。

   ![[バックアップの有効化] ボタン](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

バックアップが有効になると、バックアップ ポリシーがスケジュールに従って実行されます。 仮想マシンをすぐにバックアップするオンデマンド バックアップ ジョブを生成するには、「[バックアップ ジョブのトリガー](./backup-azure-vms-first-look-arm.md#initial-backup)」を参照してください。

仮想マシンの登録に問題がある場合は、VM エージェントのインストールやネットワーク接続に関するこの後の情報を参照してください。 Azure によって作成された仮想マシンを保護している場合、次の情報はおそらく必要ないでしょう。 ただし、仮想マシンを Azure に移行した場合は、VM エージェントを正しくインストールしていることと、仮想マシンが仮想ネットワークで通信できることを確認してください。

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>仮想マシンに VM エージェントをインストールする
バックアップ拡張機能を動作させるには、Azure [VM エージェント](../virtual-machines/extensions/agent-windows.md)を Azure 仮想マシンにインストールする必要があります。 VM を Azure Marketplace から作成した場合、VM エージェントは既に仮想マシンに存在します。 

Azure Marketplace から作成した VM を*使用していない*状況のために、以下の情報が提供されています。 **たとえば、オンプレミスのデータセンターから VM を移行したとします。このような場合、仮想マシンを保護するためには VM エージェントをインストールする必要があります。**

**注**: VM エージェントのインストール後、Azure PowerShell を使用して ProvisionGuestAgent プロパティを更新し、VM がインストールされたことが Azure により認識されるようにする必要があります。 

Azure VM のバックアップで問題が発生する場合は、次の表を参照して Azure VM エージェントが仮想マシンに正しくインストールされていることを確認してください。 次の表に、Windows VM と Linux VM の VM エージェントに関する追加情報をまとめています。

| **操作** | **Windows** | **Linux** |
| --- | --- | --- |
| VM エージェントのインストール |[エージェント MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をダウンロードしてインストールします。 インストールを実行するには、管理者特権が必要です。 |<li> 最新の [Linux エージェント](../virtual-machines/extensions/agent-linux.md)をインストールします。 インストールを実行するには、管理者特権が必要です。 ディストリビューション リポジトリからエージェントをインストールすることをお勧めします。 GitHub から直接 Linux VM エージェントをインストールすることは**お勧めしません**。  |
| VM エージェントの更新 |VM エージェントを更新するには、単純に [VM エージェント バイナリ](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)を再インストールします。 <br>VM エージェントの更新中にバックアップ操作が実行されないようにする必要があります。 |[Linux VM エージェントの更新 ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する手順に従います。 ディストリビューション リポジトリからエージェントを更新することをお勧めします。 GitHub から直接 Linux VM エージェントを更新することは**お勧めしません**。<br>VM エージェントの更新中にバックアップ操作が実行されないようにする必要があります。 |
| VM エージェントのインストールの検証 |<li>Azure VM で *C:\WindowsAzure\Packages* フォルダーに移動します。 <li>WaAppAgent.exe ファイルを探します。<li> このファイルを右クリックして **[プロパティ]** をクリックし、**[詳細]** タブを選択します。[製品バージョン] が 2.6.1198.718 以上であることを確認します。 |該当なし |

### <a name="backup-extension"></a>バックアップ拡張機能
VM エージェントが仮想マシンにインストールされると、Azure Backup サービスによって VM エージェントにバックアップ拡張機能がインストールされます。 Backup サービスは、バックアップ拡張機能のアップグレードとパッチの適用をシームレスに実行します。

バックアップ拡張機能は、VM が実行されているかどうかにかかわらず、Backup サービスによってインストールされます。 VM が実行されている場合は、アプリケーション整合性復旧ポイントを取得できる可能性が最も高くなります。 ただし、Backup サービスは、VM がオフになっている場合でも VM のバックアップを続行しますが、拡張機能はインストールされない可能性があります。 これは*オフライン VM* と呼ばれます。 この場合、復旧ポイントは、" *クラッシュ整合性*" 復旧ポイントになります。

## <a name="establish-network-connectivity"></a>ネットワーク接続を確立する
拡張機能が VM スナップショットを管理するためには、Azure のパブリック IP アドレスへの接続が必要です。 適切なインターネット接続を利用できない場合、仮想マシンからの HTTP 要求はタイムアウトになり、バックアップ操作は失敗します。 (たとえば、ネットワーク セキュリティ グループ (NSG) を使用して) デプロイにアクセス制限が適用されている場合は、次のいずれかのオプションを選択して、バックアップ トラフィックの明確なパスを指定する必要があります。

* [Azure データ センターの IP の範囲をホワイトリストに登録する](http://www.microsoft.com/en-us/download/details.aspx?id=41653)。
* トラフィックをルーティングする HTTP プロキシ サーバーをデプロイする。

どのオプションを使用するか決める場合は、次に示す管理の容易さ、細かな制御、およびコストの間のトレードオフを考慮します。

| オプション | 長所 | 短所 |
| --- | --- | --- |
| IP 範囲をホワイトリストに登録する |追加のコストが発生しない。<br><br>NSG でアクセスを開くには、**Set-AzureNetworkSecurityRule** コマンドレットを使用する。 |影響を受ける IP の範囲が時間の経過と共に変化するため、管理が複雑である。<br><br>Storage だけでなく、Azure 全体へのアクセスを提供することになる。 |
| HTTP プロキシを使用する |許可するストレージ URL をプロキシで詳細に制御可能。<br><br>VM に対するインターネット アクセスを単一の場所で実現。<br><br>Azure の IP アドレスの変更の影響を受けない。 |プロキシ ソフトウェアで VM を実行するための追加のコストが発生する。 |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Azure データ センターの IP の範囲をホワイトリストに登録する
Azure データ センターの IP 範囲をホワイトリストに登録する場合、IP 範囲の詳細と手順については、[Azure の Web サイト](http://www.microsoft.com/en-us/download/details.aspx?id=41653)を参照してください。

[サービス タグ](../virtual-network/security-overview.md#service-tags)を使用して、特定のリージョンのストレージに接続できます。 ストレージ アカウントへのアクセスを許可するルールが、インターネット アクセスをブロックするルールよりも優先度が高いことを確認してください。 

![リージョンのストレージ タグが与えられた NSG](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

次のビデオでは、サービス タグを構成する手順について説明します。 

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!WARNING]
> ストレージ サービス タグは特定のリージョンでのみ利用できます。また、これはプレビュー版です。 リージョンの一覧については、「[ストレージのサービス タグ](../virtual-network/security-overview.md#service-tags)」を参照してください。

### <a name="use-an-http-proxy-for-vm-backups"></a>VM のバックアップに HTTP プロキシを使用する
VM をバックアップする際、バックアップ拡張機能は HTTPS API を使用してスナップショット管理コマンドを Azure Storage に送信します。 パブリック インターネットにアクセスできるように構成されたコンポーネントは HTTP プロキシのみであるため、HTTP プロキシ経由でバックアップ拡張機能のトラフィックをルーティングします。

> [!NOTE]
> Microsoft では、特定のプロキシ ソフトウェアを使用することをお勧めしていません。 以降の構成手順と互換性があるプロキシを選択してください。
>
>

次の図は、HTTP プロキシを使用するために必要な 3 つの構成手順を示しています。

* アプリケーション VM は、パブリック インターネット宛てのすべての HTTP トラフィックをプロキシ VM 経由でルーティングします。
* プロキシ VM では、仮想ネットワーク内の VM からの着信トラフィックを許可します。
* NSF ロックダウンと呼ばれるネットワーク セキュリティ グループには、プロキシ VM からの発信インターネット トラフィックを許可するセキュリティ規則が必要です。

HTTP プロキシを使用してパブリック インターネットと通信するには、次の手順を実行します。

> [!NOTE]
> これらの手順では、この例の特定の名前と値を使用しています。 実際のコードに対して詳細の入力 (または貼り付け) を行う場合は、実際のデプロイの名前と値を使用してください。

#### <a name="step-1-configure-outgoing-network-connections"></a>手順 1: 発信方向のネットワーク接続を構成する
###### <a name="for-windows-machines"></a>Windows マシンの場合
この手順では、ローカル システム アカウントのプロキシ サーバー構成を設定します。

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553) をダウンロードします。
1. 管理者特権のプロンプトから次のコマンドを実行して Internet Explorer を開きます。

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

1. Internet Explorer で、**[ツール]** > **[インターネット オプション]** > **[接続]** > **[LAN の設定]** の順に移動します。
1. システム アカウントのプロキシ設定を確認します。 プロキシの IP アドレスとポートを設定します。
1. Internet Explorer を閉じます。

次のスクリプトで、コンピューター全体のプロキシ構成が設定され、すべての発信 HTTP または HTTPS トラフィックに使用されます。 現在のユーザー アカウント (ローカル システム アカウントではなく) にプロキシ サーバーを設定した場合は、次のスクリプトを使用して、SYSTEMACCOUNT にそれらを適用します。

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> プロキシ サーバーのログに "(407) プロキシ認証が必要です" というメッセージが記録されている場合は、認証が正しく設定されているか確認します。
>
>

###### <a name="for-linux-machines"></a>Linux マシンの場合
次の行を ```/etc/environment``` ファイルに追加します。

```
http_proxy=http://<proxy IP>:<proxy port>
```

次の行を ```/etc/waagent.conf``` ファイルに追加します。

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>手順 2: プロキシ サーバーで着信接続を許可する
1. プロキシ サーバーで Windows ファイアウォールを開きます。 ファイアウォールにアクセスする最も簡単な方法は、"**セキュリティが強化された Windows ファイアウォール**" を検索することです。
1. **[セキュリティが強化された Windows ファイアウォール]** ダイアログ ボックスで、**[受信の規則]** を右クリックし、**[新しい規則]** を選択します。
1. 新規の受信の規則ウィザードの **[規則の種類]** ページで、**[カスタム]** オプションを選択し、**[次へ]** を選択します。
1. **[プログラム]** ページで **[すべてのプログラム]** を選択し、**[次へ]** を選択します。
1. **[プロトコルおよびポート]** ページで、次の情報を入力して、**[次へ]** を選択します。
   * **[プロトコルの種類]** では、**[TCP]** を選択します。
   * **[ローカル ポート]** では **[特定のポート]** を選択します。 次のボックスで、構成されているプロキシ ポートの番号を指定します。
   * **[リモート ポート]** では、**[すべてのポート]** を選択します。

以降のウィザードでは、既定の設定のまま最後の画面に進みます。 この規則に名前を付けます。 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>手順 3: NSG に例外の規則を追加する
次のコマンドは、例外を NSG に追加します。 この例外により、10.0.0.5 の任意のポートから、ポート 80 (HTTP) または 443 (HTTPS) 上の任意のインターネット アドレスに TCP トラフィックを送信できます。 パブリック インターネットで特定のポートが必要な場合は、必ずそのポートを ```-DestinationPortRange``` に追加します。

Azure PowerShell コマンド プロンプトで、次のコマンドを入力します。

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>疑問がある場合
ご不明な点がある場合や今後搭載を希望する機能がある場合は、[フィードバックをお送りください](http://aka.ms/azurebackup_feedback)。

## <a name="next-steps"></a>次の手順
これで VM をバックアップするために環境の準備が整いました。次のステップとして、バックアップの作成を行ってください。 計画に関する記事で、VM のバックアップについて詳細を説明します。

* [仮想マシンのバックアップ](backup-azure-arm-vms.md)
* [VM のバックアップ インフラストラクチャの計画](backup-azure-vms-introduction.md)
* [仮想マシンのバックアップを管理する](backup-azure-manage-vms.md)
