---
title: Azure Backup で Recovery Services コンテナーに Azure VM をバックアップする
description: Azure Backup で Recovery Services コンテナーに Azure VM をバックアップする方法について説明します
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: e782afb971f95a654119d9817edeef02642bee9e
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447567"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Recovery Services コンテナーに Azure VM をバックアップする

この記事では、Recovery Services コンテナーにバックアップをデプロイして有効にすることにより、[Azure Backup](backup-overview.md) を使用して Azure VM をバックアップする方法を説明します。 

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * サポートされるシナリオと前提条件を確認する。
> * Azure VM を準備する。 必要に応じて Azure VM エージェントをインストールし、VM に対する発信アクセスを確認する。
> * コンテナーを作成する。
> * コンテナー用のストレージをセットアップする
> * VM を検出し、バックアップの設定とポリシーを構成する。
> * Azure VM のバックアップを有効にする


> [!NOTE]
   > この記事では、コンテナーを設定し、バックアップする VM を選択して、Azure VM をバックアップする方法について説明します。 これは複数の VM をバックアップする場合に便利です。 VM の設定から直接 [Azure VM をバックアップする](backup-azure-vms-first-look-arm.md)こともできます。

## <a name="before-you-start"></a>開始する前に

Azure Backup では、マシンで実行されている Azure VM エージェントに拡張機能をインストールすることで、Azure VM がバックアップされます。

1. Azure VM のバックアップ アーキテクチャを[確認](backup-architecture.md#architecture-direct-backup-of-azure-vms)する。
Azure VM のバックアップとバックアップ拡張機能の[詳細を確認](backup-azure-vms-introduction.md)する。
2. Azure VM バックアップの[サポート マトリックスを確認](backup-support-matrix-iaas.md)する。
3. Azure VM を準備する。 インストールされていない場合は VM エージェントをインストールし、バックアップする VM に対する発信アクセスを確認する。


## <a name="prepare-azure-vms"></a>Azure VM を準備する

必要に応じて VM エージェントをインストールし、VM からの発信アクセスを確認します。

### <a name="install-the-vm-agent"></a>VM エージェントのインストール 
必要に応じて、次のようにエージェントをインストールします。

**VM** | **詳細**
--- | ---
**Windows VM** | エージェント MSI ファイルを[ダウンロードしてインストール](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)します。 マシンでの管理者権限でインストールします。<br/><br/> インストールを確認するには、VM の *C:\WindowsAzure\Packages* で WaAppAgent.exe を右クリックして、**[プロパティ]**、**[詳細]** タブの順に選択します。**製品バージョン**が 2.6.1198.718 以降であることを確認します。<br/><br/> エージェントを更新する場合は、バックアップ操作が実行されていないことを確認し、[エージェントを再インストール](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)します。
**Linux VM** | 使用しているディストリビューションのパッケージのリポジトリから RPM または DEB パッケージを使用してインストールする方法は、Azure Linux エージェントのインストールおよびアップグレードとしてお勧めする方法です。 すべての[動作保証済みディストリビューション プロバイダー](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)Azure Linux エージェント パッケージをイメージとリポジトリに統合します。 エージェントは [GitHub](https://github.com/Azure/WALinuxAgent) から入手できますが、そこからインストールすることはお勧めできません。<br/><br/> エージェントを更新する場合は、バックアップ操作が実行されていないことを確認し、バイナリを更新します。 


### <a name="establish-network-connectivity"></a>ネットワーク接続を確立する

VM で実行されているバックアップ拡張機能には、Azure パブリック IP アドレスへの発信アクセスが必要です。

- Azure VM が Azure Backup サービスと通信するために、明示的な送信ネットワーク アクセスは必要ありません。
- ただし、特定の古い仮想マシンでは、接続しようとすると問題が発生し、**ExtensionSnapshotFailedNoNetwork** エラーで失敗する可能性があります。 この場合、次のいずれかのオプションを使用して、バックアップ拡張機能がバックアップ トラフィック用の Azure パブリック IP アドレスに通信できるようにします。

   **オプション** | **アクション** ** | **長所** | **短所**
   --- | --- | --- | ---
   **NSG ルールを設定する** | [Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を許可します。<br/><br/>  すべてのアドレス範囲を個別に許可して管理するのではなく、[サービス タグ](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)を使用して Azure Backup サービスへのアクセスを許可するルールを追加することができます。 サービス タグについての[詳細](../virtual-network/security-overview.md#service-tags)を参照してください。 | 追加のコストが発生しない。 サービス タグを使用して簡単に管理できる
   **プロキシをデプロイする** | トラフィックをルーティングする HTTP プロキシ サーバーをデプロイする。 | Storage だけでなく、Azure 全体へのアクセスを提供することになる。 ストレージ URL に対する詳細な制御が可能。<br/><br/> VM に対するインターネット アクセスを単一の場所で実現。<br/><br/> プロキシの追加のコスト。<br/><br/> 
   **Azure Firewall を設定する** | Azure Backup サービスの FQDN タグを使用して、VM で Azure Firewall を経由したトラフィックを許可します。|  VNet サブネットで Azure Firewall が設定されている場合は、簡単に使用できる | 独自の FQDN タグを作成したり、タグで FQDN を変更したりすることはできない。<br/><br/> Azure Managed Disks を使用する場合、ファイアウォールで別途ポート (ポート 8443) が開放されている必要があります。

#### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Azure への発信アクセスを許可する NSG ルールを設定する

Azure VM に NSG によって管理されるアクセス権がある場合は、バックアップ ストレージの必要な範囲とポートへの送信アクセスを許可します。

1. VM で **[ネットワーク]**、**[送信ポートの規則を追加する]** の順にクリックします。
2. **[送信セキュリティ規則の追加]** で、**[詳細]** をクリックします。
3. **[ソース]** で **[VirtualNetwork]** を選択します。
4. **[ソース ポート範囲]** で、任意のポートからの発信アクセスを許可するアスタリスク (*) を入力します。
5. **[宛先]** で **[サービス タグ]** を選択します。 一覧から、**Storage.region** を選択します。 リージョンは、コンテナーとバックアップする VM が配置されている領域です。
6. **[宛先ポート範囲]** でポートを選択します。
    - アンマネージド VM と暗号化されていないストレージ アカウント:80
    - アンマネージド VM と暗号化されているストレージ アカウント:443 (既定の設定)
    - マネージド VM:8443
7. **[プロトコル]** で、**[TCP]** を選択します。
8. **[優先度]** で、上位の拒否ルールより低い優先度値を指定します。 アクセスを拒否するルールがある場合、新しい許可ルールを上位にする必要があります。 たとえば、優先順位 1000 で **Deny_All** (すべて拒否) ルール セットがある場合、新しいルールは、1000 未満に設定する必要があります。
9. ルールの名前と説明を指定して、**[OK]** をクリックします。

NSG ルールを複数の VM に適用して、発信アクセスを許可することができます。

このビデオでは、その手順について説明します。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


#### <a name="route-backup-traffic-through-a-proxy"></a>プロキシ経由のバックアップ トラフィックのルーティング

プロキシ経由でバックアップ トラフィックをルーティングし、必要な Azure 範囲へのプロキシ アクセスを付与できます。
次を許可するように、プロキシ VM を構成する必要があります。

- Azure VM は、パブリック インターネット宛てのすべての HTTP トラフィックをプロキシ VM 経由でルーティングする必要があります。
- プロキシは、該当する仮想ネットワーク (VNet) 内の VM から受信トラフィックを許可する必要があります。
- NSG **NSF ロックダウン**には、プロキシ VM からの発信インターネット トラフィックを許可するルールが必要です。

##### <a name="set-up-the-proxy"></a>プロキシを設定する
システム アカウントのプロキシがない場合は、次の手順で設定します。

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553) をダウンロードします。

2. **PsExec.exe -i -s cmd.exe** を実行して、システム アカウントでコマンド プロンプトを実行します。
3. システム コンテキストでブラウザーを実行します。 例: **%PROGRAMFILES%\Internet Explorer\iexplore.exe** (Internet Explorer の場合)。  
4. プロキシ設定を定義します。
    - Linux マシンで:
        - **/etc/environment** ファイルに次の行を追加します。
            - **http_proxy=http://proxy IP address:proxy port**
        - **/etc/waagent.conf** ファイルに次の行を追加します。
            - **HttpProxy.Host=proxy IP address**
            - **HttpProxy.Port=proxy port**
    - Windows マシンのブラウザー設定で、プロキシを使用する必要があることを指定します。 ユーザー アカウントで現在、プロキシを使用している場合、次のスクリプトを使用してシステム アカウント レベルで設定を適用することができます。
        ```powershell
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

##### <a name="allow-incoming-connections-on-the-proxy"></a>プロキシで着信接続を許可する

プロキシ設定で着信接続を許可します。

- たとえば、**セキュリティが強化された Windows ファイアウォール**を開きます。
    - **[受信の規則]** を右クリックし、 > **[新しい規則]** をクリックします。
    - **[規則の種類]** で、**[カスタム]** > **[次へ]** を選択します。
    - **[プログラム]** で、**[すべてのプログラム]** > **[次へ]** を選択します。
    - **[プロトコルおよびポート]** で、種類を **[TCP]**、**[ローカル ポート]** を **[特定のポート]**、**[リモート ポート]** を **[すべてのポート]** に設定します。
    - ウィザードを終了し、ルールの名前を指定します。

##### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>プロキシの NSG に例外規則を追加する

NSG **NSF ロックダウン**で、10.0.0.5 の任意のポートから、ポート 80 (HTTP) または 443 (HTTPS) 上の任意のインターネット アドレスへのトラフィックを許可します。

- 次の PowerShell スクリプトでは、トラフィックを許可する例を示します。
- すべてのパブリック インターネット アドレスへの送信を許可する代わりに、IP アドレス範囲を指定する (-DestinationPortRange) ことも、storage.region サービス タグを使用することもできます。   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>FQDN タグを使用してファイアウォール アクセスを許可する

Azure Backup へのネットワーク トラフィックの送信アクセスを許可するように Azure Firewall を設定することができます。

- Azure Firewall のデプロイに関する[詳細情報を参照してください](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)。
- FQDN タグについて[お読みください](https://docs.microsoft.com/azure/firewall/fqdn-tags)。

## <a name="create-a-vault"></a>コンテナーの作成

コンテナーには、経時的に作成されたバックアップと復旧ポイントと、バックアップしたマシンに関連付けられたバックアップ ポリシーが格納されます。 次の手順に従ってコンテナーを作成します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **[ハブ]** メニューで **[参照]** を選択し、「**Recovery Services**」と入力します。 **[Recovery Services コンテナー]** を選択します。

    ![ボックスに入力し、結果の [Recovery Services コンテナー] を選択します](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. **[Recovery Services コンテナー]** メニューの **[追加]** を選択します。

    ![Create Recovery Services Vault step 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    ![[Recovery Services コンテナー] ウィンドウ](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. **[Recovery Services コンテナー]** >  **[名前]** に、コンテナーを識別するフレンドリ名を入力します。
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


## <a name="configure-a-backup-policy"></a>バックアップ ポリシーを構成する

サブスクリプションで VM を検出して、バックアップを構成します。

1. コンテナーの **[概要]** で、**[+ バックアップ]** をクリックします。

   ![[バックアップ] ボタン](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **[バックアップ]** ウィンドウと **[バックアップの目標]** ウィンドウが開きます。

2. **[バックアップの目標]**> **[ワークロードはどこで実行されていますか?]** で **[Azure]** を選択します。 **[What do you want to backup?]\(何をバックアップしますか?\)** で、**[仮想マシン]** >  **[OK]** の順に選択します。 これにより、VM 拡張機能がコンテナーに登録されます。

   ![[バックアップ] ウィンドウと [バックアップの目標] ウィンドウ](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   この手順で、VM 拡張機能がコンテナーに登録されます。 **[バックアップの目標]** ウィンドウが閉じ、**[バックアップ ポリシー]** ウィンドウが開きます。

3. **[バックアップ ポリシー]** で、コンテナーに関連付けるポリシーを選択します。 次に、 **[OK]** をクリックします
    - 既定のポリシーの詳細がドロップダウン メニューの下に一覧表示されます。
    - **[新規作成]** をクリックしてポリシーを作成します。 ポリシーの定義に関する[詳細情報を参照してください](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)。
    

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

- バックアップ スケジュールに従って初回バックアップが実行されます。
- バックアップ拡張機能は、VM が実行されているかどうかにかかわらず、Backup サービスによってインストールされます。
    - VM が実行されている場合は、アプリケーション整合性復旧ポイントを取得できる可能性が最も高くなります。
    -  ただし、VM がオフになっていて拡張機能がインストールできない場合でも、VM はバックアップされます。 これは*オフライン VM* と呼ばれます。 この場合、復旧ポイントは、" *クラッシュ整合性*" 復旧ポイントになります。
    Azure Backup では、Azure VM バックアップの夏時間変更に対する時計の自動調整はサポートされていないことに注意してください。 必要に応じて手動でバックアップ ポリシーを変更します。
  
 ## <a name="run-the-initial-backup"></a>初回バックアップを実行する

手動ですぐに実行しない限り、初回バックアップはスケジュールに従って実行されます。 手動で実行する方法は次のとおりです。

1. コンテナー メニューで **[バックアップ アイテム]** をクリックします。
2. **[バックアップ アイテム]** で、**[Azure Virtual Machines]** をクリックします。
3. **[バックアップ アイテム]** の一覧で、省略記号 **[...]** をクリックします。
4. **[今すぐバックアップ]** をクリックします。
5. **[今すぐバックアップ]** で、カレンダー コントロールを使用して復旧ポイントを保持する最終日を選択し、**[OK]** をクリックします。
6. ポータルの通知を監視します。 コンテナー ダッシュボードの **[バックアップ ジョブ]** > **[進行中]** でジョブの進行状況を監視できます。 VM のサイズによっては、最初のバックアップの作成に時間がかかる場合があります。



## <a name="next-steps"></a>次の手順

- [Azure VM エージェント](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)または[Azure VM バックアップ](backup-azure-vms-troubleshoot.md)で発生する問題のトラブルシューティング。
- [Azure VM のバックアップ](backup-azure-vms-first-look-arm.md)
