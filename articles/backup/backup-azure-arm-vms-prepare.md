---
title: Azure Backup サービスを使用して Azure VM をバックアップする準備をする
description: Azure Backup サービスを使用して Azure VM のバックアップの準備方法について説明します
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: ee7a9c407a26f9334a854c98793db8fc01244e2a
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994676"
---
# <a name="prepare-to-back-up-azure-vms"></a>Azure VM をバックアップする準備をする

この記事では、[Azure Backup](backup-introduction-to-azure-backup.md) Recovery Services コンテナーを使用して Azure VM のバックアップの準備方法について説明します。 バックアップの準備には、以下が含まれます。


> [!div class="checklist"]
> * 開始する前に、サポートされるシナリオと制限事項を確認する。
> * Azure VM の要件やネットワーク接続などの前提条件を確認する。
> * コンテナーを作成する。
> * ストレージのレプリケート方法を選択する。
> * VM を検出し、バックアップの設定とポリシーを構成する。
> * 選択した VM のバックアップを有効にする。


> [!NOTE]
   > この記事では、コンテナーを設定し、バックアップする VM を選択して、Azure VM をバックアップする方法について説明します。 これは複数の VM をバックアップする場合に便利です。 Azure VM をその VM の設定から直接バックアップすることもできます。 [詳細情報](backup-azure-vms-first-look-arm.md)

## <a name="before-you-start"></a>開始する前に

1. Azure VM の Azure Backup の[概要を確認](backup-azure-vms-introduction.md)します。
2. 以下のサポートの詳細と制限事項を確認します。

   **サポート/制限事項** | **詳細**
   --- | ---
   **Windows OS** | Windows Server 2008 R2 64 ビット以降。<br/><br/> Windows Client 7 64 ビット以降。
   **Linux OS** | [Azure でサポートされている](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 64 ビットの Linux ディストリビューションをバックアップできます。ただし、CoreOS Linux は除きます。<br/><br/> [ファイル復元をサポートしている](backup-azure-restore-files-from-vm.md#for-linux-os) Linux オペレーティング システムを確認します。<br/><br/> 他の Linux ディストリビューションは、VM 上で VM エージェントが動作し、かつ Python がサポートされていれば使用できます。 ただし、これらのディストリビューションはサポートされません。
   **[リージョン]** | すべての[サポートされているリージョン](https://azure.microsoft.com/regions/#services)で Azure VM をバックアップできます。 リージョンがサポートされていない場合は、コンテナーを作成するときにそれを選択することはできません。<br/><br/> Azure リージョン間でバックアップして復元することはできません。 1 つのリージョン内でのみ可能です。
   **データ ディスクの制限** | 16 を超えるデータ ディスクを持つ VM をバックアップすることはできません。
   **共有ストレージ** | CSV またはスケール アウト ファイル サーバーを使用して VM をバックアップすることはお勧めできません。 CSV ライターが失敗する可能性があります。
   **Linux の暗号化** | Linux Unified Key Setup (LUKS) を使用して暗号化された Linux VM のバックアップはサポートされていません。
   **VM の整合性** | Azure Backup では、マルチ VM 整合性をサポートしていません。
   **ネットワーク** | バックアップされたデータには、ネットワーク経由でマウントされて VM に接続されているドライブは含まれません。<br/><br/>
   **スナップショット** | 書き込みアクセラレータを有効にしたディスクでスナップショットを作成することはサポートされていません。 これにより Azure Backup によるすべての VM ディスクのアプリ整合性スナップショットの作成がブロックされます。
   **PowerShell** | PowerShell でのみ使用可能なアクションは数多くあります。<br/><br/> - 内部/外部ロード バランサーで管理されている VM、または複数の予約済み IP アドレスまたはアダプターを持つ VM を復元します。 [詳細情報](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)<br/><br/> - 複数のドメイン コントローラー構成でドメイン コントローラー VM を復元します。 [詳細情報](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)。
   **システム時刻** | Azure Backup では、Azure VM バックアップの夏時間変更に対する時計の自動調整はサポートされていません。 必要に応じて手動でバックアップ ポリシーを変更します。
   **ストレージ アカウント** | ネットワーク制限付きストレージ アカウントを使用している場合、Azure Backup サービスがアカウントにアクセスできるように、**[Allow trusted Microsoft services to access this storage account]\(信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します\)** が有効になっていることを確認します。 ネットワーク制限付きストレージ アカウントでは、アイテム レベルの回復はサポートされていません。<br/><br/> ストレージ アカウントで、**[ファイアウォールと仮想ネットワーク]** の設定で **[すべてのネットワーク]** からのアクセスが許可されていることを確認してください。


## <a name="prerequisites"></a>前提条件

- バックアップを作成する Azure VM と同じリージョンにコンテナーを作成する必要があります。
- 開始する前に、Azure VM のリージョンを確認します。
    - 複数のリージョンに VM がある場合は、リージョンごとにコンテナーを作成します。
    - バックアップ データを格納するためのストレージ アカウントを指定する必要はありません。 コンテナーと Azure Backup サービスで自動的に処理されます。
- バックアップを作成する Azure VM に VM エージェントがインストールされていることを確認します。



### <a name="install-the-vm-agent"></a>VM エージェントのインストール

バックアップを有効にするため、Azure Backup によってバックアップ拡張機能 (VM スナップショットまたは VM スナップショット Linux) が Azure VM で実行される VM エージェントにインストールされます。
    -  Azure VM エージェントは、Azure Marketplace イメージからデプロイされたすべての Windows VM に既定でインストールされます。 Azure VM エージェントは、ポータル、PowerShell、CLI、Azure Resource Manager テンプレートから Azure Marketplace イメージをデプロイしてもインストールされます。
    - オンプレミスから VM を移行した場合、エージェントはインストールされないため、VM のバックアップを有効にするには、先にエージェントをインストールする必要があります。

必要に応じて、次のようにエージェントをインストールします。

**VM** | **詳細**
--- | ---
**Windows VM** | 管理者権限を使用してエージェントをコンピューターに[ダウンロードしてインストール](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)します。<br/><br/> インストールを確認するには、VM の *C:\WindowsAzure\Packages* で WaAppAgent.exe を右クリックして、**[プロパティ]**、**[詳細]** タブの順に選択します。**製品バージョン**が 2.6.1198.718 以降であることを確認します。
**Linux VM** | 使用しているディストリビューションのパッケージのリポジトリから RPM または DEB パッケージを使用してインストールする方法は、Azure Linux エージェントのインストールおよびアップグレードとしてお勧めする方法です。 すべての[動作保証済みディストリビューション プロバイダー](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)Azure Linux エージェント パッケージをイメージとリポジトリに統合します。 エージェントは [GitHub](https://github.com/Azure/WALinuxAgent) から入手できますが、そこからインストールすることはお勧めできません。
Azure VM のバックアップで問題が発生する場合は、次の表を参照して Azure VM エージェントが仮想マシンに正しくインストールされていることを確認してください。 次の表に、Windows VM と Linux VM の VM エージェントに関する追加情報をまとめています。

### <a name="establish-network-connectivity"></a>ネットワーク接続を確立する

VM で実行されているバックアップ拡張機能には、Azure パブリック IP アドレスへの発信アクセスが必要です。 次の方法でアクセスを許可できます。


- **NSG ルール**:[Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を許可します。 すべてのアドレス範囲を個別に許可して、それらを経時的に管理するのではなく、[サービス タグ](../virtual-network/security-overview.md#service-tags)を使用して Azure Backup サービスへのアクセスを許可するルールを追加することができます。
- **プロキシ**:トラフィックをルーティングする HTTP プロキシ サーバーをデプロイする。
- **Azure Firewall**:Azure Backup サービスの FQDN タグを使用して、VM で Azure Firewall を経由したトラフィックを許可します。

オプションを決定する際は、トレードオフを検討します。

**オプション** | **長所** | **短所**
--- | --- | ---
**NSG** | 追加のコストが発生しない。 サービス タグを使用して簡単に管理できる | Storage だけでなく、Azure 全体へのアクセスを提供することになる。 |
**HTTP プロキシ** | ストレージ URL に対する詳細な制御が可能。<br/><br/> VM に対するインターネット アクセスを単一の場所で実現。<br/><br/> プロキシの追加のコスト。
**FQDN タグ** | VNet サブネットで Azure Firewall が設定されている場合は、簡単に使用できる | 独自の FQDN タグを作成したり、タグで FQDN を変更したりすることはできない。



Azure Managed Disks を使用する場合、ファイアウォールで別途ポート (ポート 8443) が開放されている必要があります。



### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Azure への発信アクセスを許可する NSG ルールを設定する

Azure VM に NSG によって管理されるアクセス権がある場合は、バックアップ ストレージの必要な範囲とポートへの送信アクセスを許可します。



1. VM で **[ネットワーク]**、**[送信ポートの規則を追加する]** の順にクリックします。
- アクセスを拒否するルールがある場合、新しい許可ルールを上位にする必要があります。 たとえば、優先順位 1000 で **Deny_All** (すべて拒否) ルール セットがある場合、新しいルールは、1000 未満に設定する必要があります。
2. **[送信セキュリティ規則の追加]** で、**[詳細]** をクリックします。
3. [ソース] で **[VirtualNetwork]** を選びます。
4. **[ソース ポート範囲]** で、任意のポートからの発信アクセスを許可するアスタリスク (*) を入力します。
5. **[宛先]** で **[サービス タグ]** を選択します。 一覧から、[ストレージ] を選択します。<region> リージョンは、コンテナーとバックアップする VM が配置されている領域です。
6. **[宛先ポート範囲]** でポートを選択します。

    - アンマネージド ディスクと暗号化されていないストレージ アカウントを持つ VM:80
    - アンマネージド ディスクと暗号化されたストレージ アカウントを持つ VM:443 (既定の設定)
    - マネージド VM:8443
1. **[プロトコル]** で、**[TCP]** を選択します。
2. **[優先度]** で、上位の拒否ルールより低い優先度値を指定します。
3. ルールの名前と説明を指定して、**[OK]** をクリックします。

NSG ルールを複数の VM に適用して、Azure Backup の Azure への発信アクセスを許可することができます。

このビデオでは、その手順について説明します。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]



### <a name="route-backup-traffic-through-a-proxy"></a>プロキシ経由のバックアップ トラフィックのルーティング

プロキシ経由でバックアップ トラフィックをルーティングし、必要な Azure 範囲へのプロキシ アクセスを付与できます。

次を許可するように、プロキシ VM を構成する必要があります。

- Azure VM は、パブリック インターネット宛てのすべての HTTP トラフィックをプロキシ VM 経由でルーティングする必要があります。
- プロキシは、該当する仮想ネットワーク (VNet) 内の VM から受信トラフィックを許可する必要があります。
- NSG **NSF ロックダウン**には、プロキシ VM からの発信インターネット トラフィックを許可するルールが必要です。

プロキシの設定方法を以下に示します。 例の値を使用します。 これらの値は実際の値に置き換える必要があります。

#### <a name="set-up-a-system-account-proxy"></a>システム アカウント プロキシの設定
システム アカウントのプロキシがない場合は、次の手順で設定します。

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553) をダウンロードします。
2. **PsExec.exe -i -s cmd.exe** を実行して、システム アカウントでコマンド プロンプトを実行します。
3. システム コンテキストでブラウザーを実行します。 例: **PROGRAMFILES%\Internet Explorer\iexplore.exe** (Internet Explorer の場合)。  
4. プロキシ設定を定義します。
    - Linux マシンで:
        - **/etc/environment** ファイルに次の行を追加します。
            - **http_proxy=http://proxy IP address:proxy port**
        - **/etc/waagent.conf** ファイルに次の行を追加します。
            - **HttpProxy.Host=proxy IP address**
            - **HttpProxy.Port=proxy port**
    - Windows マシンのブラウザー設定で、プロキシを使用する必要があることを指定します。 ユーザー アカウントで現在、プロキシを使用している場合、次のスクリプトを使用してシステム アカウント レベルで設定を適用することができます。
        ```
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

#### <a name="allow-incoming-connections-on-the-proxy"></a>プロキシで着信接続を許可する

1. プロキシ設定で着信接続を許可します。
2. たとえば、**セキュリティが強化された Windows ファイアウォール**を開きます。
    - **[受信の規則]** を右クリックし、 > **[新しい規則]** をクリックします。
    - **[規則の種類]** で、**[カスタム]** > **[次へ]** を選択します。
    - **[プログラム]** で、**[すべてのプログラム]** > **[次へ]** を選択します。
    - **[プロトコルおよびポート]** で、種類を **[TCP]**、**[ローカル ポート]** を **[特定のポート]**、**[リモート ポート]** を **[すべてのポート]** に設定します。
    - ウィザードを終了し、ルールの名前を指定します。

#### <a name="add-an-exception-rule-to-the-nsg"></a>NSG に例外の規則を追加する

NSG **NSF ロックダウン**で、10.0.0.5 の任意のポートから、ポート 80 (HTTP) または 443 (HTTPS) 上の任意のインターネット アドレスへのトラフィックを許可します。

- 次の PowerShell スクリプトでは、トラフィックを許可する例を示します。
- すべてのパブリック インターネット アドレスへの送信を許可する代わりに、IP アドレス範囲を指定する (-DestinationPortRange) ことも、storage.region サービス タグを使用することもできます。   

    ```
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```
### <a name="allow-firewall-access-with-fqdn-tag"></a>FQDN タグを使用してファイアウォール アクセスを許可する

Azure Backup へのネットワーク トラフィックの送信アクセスを許可するように Azure Firewall を設定することができます。

- Azure Firewall のデプロイに関する[詳細情報を参照してください](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)。
- FQDN タグについて[お読みください](https://docs.microsoft.com/azure/firewall/fqdn-tags)。


## <a name="create-a-vault"></a>コンテナーの作成

バックアップ用の Recovery Services コンテナーには、経時的に作成されたバックアップと復旧ポイントと、バックアップしたマシンに関連付けられたバックアップ ポリシーが格納されます。 次の手順に従ってコンテナーを作成します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **[ハブ]** メニューで **[参照]** を選択し、「**Recovery Services**」と入力します。 入力を開始すると、リソースのリストが絞り込まれます。 **[Recovery Services コンテナー]** を選択します。

    ![ボックスに入力し、結果の [Recovery Services コンテナー] を選択します](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Recovery Services コンテナーの一覧が表示されます。
3. **[Recovery Services コンテナー]** メニューの **[追加]** を選択します。

    ![Create Recovery Services Vault step 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    **[Recovery Services コンテナー]** ウィンドウが開きます。 **[名前]**、**[サブスクリプション]**、**[リソース グループ]**、**[場所]** の情報を入力するように求められます。

    ![[Recovery Services コンテナー] ウィンドウ](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。
    - 名前は Azure サブスクリプションに対して一意である必要があります。
    - 2 から 50 文字を含めることができます。
    - 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。
5. **[サブスクリプション]** を選択して、使用可能なサブスクリプションの一覧を表示します。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (または推奨) のサブスクリプションを使用してください。 職場または学校アカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。
6. **[リソース グループ]** を選択して、使用可能なリソース グループを表示するか、**[新規]** を選択して、新しいリソース グループを作成します。 リソース グループに関する[詳細情報を参照してください](../azure-resource-manager/resource-group-overview.md)。
7. **[場所]** を選択して、コンテナーの地理的リージョンを選択します。 コンテナーは、バックアップする VM と同じリージョンにある*必要があります*。
8. **作成**を選択します。
    - コンテナーが作成されるまで時間がかかることがあります。
    - ポータルの右上の領域で、状態の通知を監視します。
    ![バックアップ資格情報コンテナーの一覧](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。 コンテナーが表示されない場合は、**[最新の情報に更新]** を選択します。

## <a name="set-up-storage-replication"></a>ストレージ レプリケーションの設定

既定では、コンテナーには [geo 冗長ストレージ (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) があります。 プライマリ バックアップには GRS をお勧めしますが、コストを抑えるオプションとして[ローカル冗長ストレージ](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)を使用することもできます。 

ストレージ レプリケーションを変更にするには、次の手順に従います。

1. コンテナーの **[バックアップ インフラストラクチャ]** で、**[バックアップ構成]** をクリックします。

   ![バックアップ コンテナーの一覧](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. **[バックアップ構成]** で、必要に応じてストレージ冗長化の方法を変更して、**[保存]** を選択します。


## <a name="configure-backup"></a>バックアップの構成

サブスクリプションで VM を検出して、バックアップを構成します。

1. コンテナーの **[概要]** で、**[+ バックアップ]** をクリックします。

   ![[バックアップ] ボタン](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **[バックアップ]** ウィンドウと **[バックアップの目標]** ウィンドウが開きます。

2. **[バックアップの目標]**> **[ワークロードはどこで実行されていますか?]** で **[Azure]** を選択します。 **[What do you want to backup?]\(何をバックアップしますか?\)** で、**[仮想マシン]** >  **[OK]** の順に選択します。 これにより、VM 拡張機能がコンテナーに登録されます。

   ![[バックアップ] ウィンドウと [バックアップの目標] ウィンドウ](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   この手順で、VM 拡張機能がコンテナーに登録されます。 **[バックアップの目標]** ウィンドウが閉じ、**[バックアップ ポリシー]** ウィンドウが開きます。

3. **[バックアップ ポリシー]** で、コンテナーに関連付けるポリシーを選択します。 次に、 **[OK]** をクリックします
    - 既定のポリシーの詳細がドロップダウン メニューの下に一覧表示されます。
    - **[新規作成]** をクリックしてポリシーを作成します。 ポリシーの定義に関する[詳細情報を参照してください](backup-azure-vms-first-look-arm.md#defining-a-backup-policy)。

    ![[バックアップ] ウィンドウと [バックアップ ポリシー] ウィンドウ](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. **[仮想マシンの選択]** ウィンドウで、指定されたバックアップ ポリシーを使用する VM を選択し、**[OK]** を選択します。

    - 選択した VM が検証されます。
    - コンテナーと同じリージョンにある VM のみを選択できます。 VM は、1 つのコンテナーでのみバックアップできます。

   ![[仮想マシンの選択] ウィンドウ](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. **[バックアップ]** で、**[バックアップの有効化]** を選択します。

   - これにより、ポリシーがコンテナーと VM にデプロイされ、Azure VM で実行されている VM エージェントにバックアップ拡張機能がインストールされます。
   - この手順では、VM の最初の復旧ポイントは作成されません。

   ![[バックアップの有効化] ボタン](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

バックアップの有効化後:

- バックアップ スケジュールに従ってバックアップ ポリシーが実行されます。
- バックアップ拡張機能は、VM が実行されているかどうかにかかわらず、Backup サービスによってインストールされます。
    - VM が実行されている場合は、アプリケーション整合性復旧ポイントを取得できる可能性が最も高くなります。
    -  ただし、VM がオフになっていて拡張機能がインストールできない場合でも、VM はバックアップされます。 これは*オフライン VM* と呼ばれます。 この場合、復旧ポイントは、" *クラッシュ整合性*" 復旧ポイントになります。
- すぐに VM のオンデマンド バックアップを生成する場合は、**[バックアップ項目]** で、VM の横にある省略記号 (...)、**[今すぐバックアップ]** の順にクリックします。


## <a name="next-steps"></a>次の手順

- [Azure VM エージェント](/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)または[Azure VM バックアップ](backup-azure-vms-troubleshoot.md)で発生する問題のトラブルシューティング。
- [Azure VM のバックアップ](backup-azure-vms-first-look-arm.md)
