<properties
	pageTitle="Azure Virtual Machines をバックアップする環境の準備 | Microsoft Azure"
	description="Azure Virtual Machines をバックアップする環境を準備します"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# Azure 仮想マシンをバックアップする環境の準備
Azure 仮想マシンをバックアップする環境を準備するには、次の前提条件を満たす必要があります。前提条件を満たしている場合は、[VM をバックアップ](backup-azure-vms.md)できます。満たしていない場合は、次の手順で環境を準備してください。


## 1\.バックアップ資格情報コンテナー

![バックアップ資格情報コンテナー](./media/backup-azure-vms-prepare/step1.png)

Azure 仮想マシンのバックアップを開始するには、まずバックアップ資格情報コンテナーを作成する必要があります。資格情報コンテナーは、時間の経過と共に作成されるすべてのバックアップと復旧ポイントを格納するエンティティです。資格情報コンテナーには、バックアップ対象の仮想マシンに適用されるバックアップ ポリシーも含まれています。

次の図は、さまざまな Azure Backup エンティティの関係を示しています。![Azure Backup のエンティティとの関係](./media/backup-azure-vms-prepare/vault-policy-vm.png)

バックアップ資格情報コンテナーを作成するには:

1. [管理ポータル](http://manage.windowsazure.com/)にサインインします。

2. **[新規]** > **[Data Services]** > **[Recovery Services]** > **[バックアップ資格情報コンテナー]** > **[簡易作成]** の順にクリックします。組織のアカウントに関連付けられたサブスクリプションが複数ある場合は、正しいアカウントを選択してバックアップ資格情報コンテナーに関連付けてください。各 Azure サブスクリプションに複数のバックアップ資格情報コンテナーを保有し、保護する仮想マシンを編成できます。

3. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。これは、サブスクリプションごとに一意である必要があります。

4. **[リージョン]** ボックスで、コンテナーのリージョンを選択します。資格情報コンテナーは、保護する仮想マシンと同じリージョンにある必要があります。仮想マシンが別のリージョンにある場合は、資格情報コンテナーをそれぞれ作成します。バックアップ データを格納するストレージ アカウントを指定する必要はありません。バックアップ資格情報コンテナーと Azure Backup サービスはこれを自動的に処理します。

    ![バックアップ資格情報コンテナーの作成](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. **[資格情報コンテナーの作成]** をクリックします。バックアップ資格情報コンテナーが作成されるまで時間がかかることがあります。ポータルの下部にある状態通知を監視します。

    ![資格情報コンテナーのトースト通知の作成](./media/backup-azure-vms-prepare/creating-vault.png)

6. 資格情報コンテナーが正常に作成されたことを示すメッセージが表示され、[Recovery Services] ページに [アクティブ] と表示されます。コンテナーを作成したら、適切なストレージの冗長オプションが選択されていることを確認してください。詳細については、「[setting the storage redundancy option in the backup vault (バックアップ資格情報コンテナーのストレージ冗長オプションの設定)](backup-configure-vault.md#azure-backup---storage-redundancy-options)」をご覧ください。

    ![バックアップ資格情報コンテナーの一覧](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. バックアップ資格情報コンテナーをクリックして **[クイック スタート]** ページに進むと、Azure 仮想マシンのバックアップ手順が表示されます。

    ![ダッシュボード ページの仮想マシンのバックアップ手順](./media/backup-azure-vms-prepare/vmbackup-instructions.png)



## 2\.ネットワーク接続

![ネットワーク接続](./media/backup-azure-vms-prepare/step2.png)

バックアップ拡張機能が正常に機能するには、Azure パブリック IP への接続が必要です。これは、Azure Storage エンドポイント (HTTP URL) に対して、VM のスナップショットを管理するコマンドを送信するためです。適切なインターネット接続を利用できない場合、VM からの HTTP 要求はタイムアウトになり、バックアップ操作は失敗します。

### NSG を使用したネットワーク制限

(たとえば、ネットワーク セキュリティ グループを使用して) デプロイにアクセス制限が適用されている場合、Azure Backup 資格情報コンテナーへのバックアップ トラフィックが影響を受けないようにするには、追加の手順を実行する必要があります。

バックアップ トラフィック用のパスを指定するには、次の 2 つの方法があります。

1. [Azure データ センターの IP 範囲](http://www.microsoft.com/ja-JP/download/details.aspx?id=41653)をホワイトリストに登録する。
2. トラフィックをルーティングする HTTP プロキシをデプロイする。

管理の容易さ、細かな制御、およびコストの間のトレードオフは次のとおりです。

|オプション|長所|短所|
|------|----------|-------------|
|オプション 1: IP 範囲のホワイトリストへの登録| 追加のコストが発生しない。<br><br>NSG でアクセスを開くには、<i>Set-AzureNetworkSecurityRule</i> コマンドレットを使用する。 | 影響を受ける IP 範囲が時間の経過と共に変化するため、管理が複雑。<br>Storage だけでなく Azure 全体へのアクセスを提供。|
|オプション 2: HTTP プロキシ| 許可するストレージ URL をプロキシで詳細に制御可能。<br>VM への単一ポイントのインターネット アクセス。<br>Azure の IP アドレスの変更による影響を受けない。| プロキシ ソフトウェアで VM を実行するための追加のコストが発生する。|

### VM のバックアップに HTTP プロキシを使用する
VM をバックアップする際、HTTPS API を使用してスナップショット管理コマンドがバックアップ拡張機能から Azure Storage に送信されます。パブリック インターネットにアクセスできるように構成されるのはプロキシのみのため、このトラフィックは、拡張機能からプロキシを介してルーティングされる必要があります。

>[AZURE.NOTE]使用するプロキシ ソフトウェアについて推奨事項はありません。以降に説明する構成手順と互換性があるプロキシを選択してください。

次の例では、パブリック インターネットに送信されるすべての HTTP トラフィックにプロキシ VM を使用するようにアプリ VM を構成する必要があります。プロキシ VM は、VNET 内の VM からの着信トラフィックを許可するように構成する必要があります。最後に、NSG (*NSG-lockdown*) には、プロキシ VM からの発信インターネット トラフィックを許可する新しいセキュリティ規則が必要です。

![NSG with HTTP proxy deployment diagram](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

**A) 発信方向のネットワーク接続を許可する:**

1. Windows コンピューターの場合、管理者特権でのコマンド プロンプトで、次のコマンドを実行します。

	```
	netsh winhttp set proxy http://<proxy IP>:<proxy port>
	```

	これにより、コンピューター全体のプロキシ構成が設定され、すべての発信 HTTP/ HTTPS トラフィックに使用されます。

2. Linux コンピューターの場合、次の行を ```/etc/environment``` ファイルに追加します。

 	```
 	http_proxy=http://<proxy IP>:<proxy port>
 	```

	次の行を ```/etc/waagent.conf``` ファイルに追加します。

	```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

**B) プロキシ サーバーで着信接続を許可する:**

1. プロキシ サーバー上で Windows ファイアウォールを開き、*[受信の規則]* を右クリックし、**[新しい規則]** をクリックします。

	![ファイアウォールを開く](./media/backup-azure-vms-prepare/firewall-01.png)

	![新しいルールの作成](./media/backup-azure-vms-prepare/firewall-02.png)
2. *新規の受信の規則ウイザード*の *[規則の種類]* で **[カスタム]** を選択し、[次へ] をクリックします。*プログラム*の選択画面で、**[すべてのプログラム]** を選択し、[次へ] をクリックします。

3. *[プロトコルおよびポート]* 画面で、次の表の入力値を使用して、[次へ] をクリックします。

	![新しいルールの作成](./media/backup-azure-vms-prepare/firewall-03.png)

| 入力フィールド | 値 |
| --- | --- |
| プロトコルの種類 | TCP |
| ローカル ポート | ドロップダウン リストの *[特定のポート]* を選択し、構成済みの ```<Proxy Port>``` をテキスト ボックスに入力します。 |
| リモート ポート | ドロップダウン リストの *[すべてのポート]* を選択します。 |

ウィザードの残りの部分では、[次へ] をクリックして最後まで進んだら、この規則に名前を付けます。

**C) NSG に例外の規則を追加する:**

Azure PowerShell コマンド プロンプトで、次のコマンドを入力します。

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

このコマンドでは、NSG に例外を追加します。これにより、10.0.0.5 の任意のポートから、ポート 80 (HTTP) または 443 (HTTPS) 上の任意のインターネット アドレスに TCP トラフィックを送信できます。パブリック インターネット上の特定のポートを対象にする必要がある場合は、そのポートも ```-DestinationPortRange``` に追加します。

*例で使用されている名前は、デプロイに適した詳細情報に置き換えてください。*

## 3\.VM エージェント

![VM エージェント](./media/backup-azure-vms-prepare/step3.png)

Azure 仮想マシンのバックアップを開始する前に、Azure VM エージェントが仮想マシンに正しくインストールされていることを確認してください。VM エージェントは仮想マシンを作成する際のオプション コンポーネントであるため、仮想マシンをプロビジョニングする前に、VM エージェントのチェック ボックスがオンになっていることを確認します。

### 手動によるインストールと更新

VM エージェントは、Azure ギャラリーから作成された仮想マシン内に既に存在しています。しかし、オンプレミスのデータセンターから移行された仮想マシンには VM エージェントがインストールされていません。このような仮想マシンについては、VM エージェントを明示的にインストールする必要があります。既存の仮想マシンに VM エージェントをインストールする方法については、[こちら](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)を参照してください。

| **操作** | **Windows** | **Linux** |
| --- | --- | --- |
| VM エージェントのインストール | <li>[エージェント MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) をダウンロードしてインストールします。インストールを実行するには、管理者特権が必要です。<li>[VM プロパティを更新](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)して、エージェントがインストールされていることを示します。 | <li>GitHub から最新の [Linux エージェント](https://github.com/Azure/WALinuxAgent)をインストールします。インストールを実行するには、管理者特権が必要です。<li>[VM プロパティを更新](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)して、エージェントがインストールされていることを示します。 |
| VM エージェントの更新 | VM エージェントを更新するには、単純に [VM エージェント バイナリ](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)を再インストールします。<br><br>VM エージェントの更新中にバックアップ操作が実行されないようにする必要があります。 | [Linux VM エージェントの更新](../virtual-machines-linux-update-agent.md)に関する手順に従ってください。<br><br>VM エージェントの更新中にバックアップ操作が実行されないようにする必要があります。 |
| VM エージェントのインストールの検証 | <li>Azure VM で *C:\\WindowsAzure\\Packages* フォルダーに移動します。<li>WaAppAgent.exe ファイルを探します。<li> このファイルを右クリックして、**[プロパティ]** をクリックした後、**[詳細]** タブを選択します。[製品バージョン] が 2.6.1198.718 以上であることを確認します。| - |


詳細については、「[VM エージェント](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)」と「[インストール方法](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)」に関するページをご覧ください。

### バックアップ拡張機能

仮想マシンをバックアップするために、Azure Backup サービスは VM エージェントの拡張機能をインストールします。Azure Backup サービスは、バックアップ拡張機能のアップグレードと修正プログラムの適用をシームレスに自動実行します。

VM が実行されている場合、バックアップ拡張機能がインストールされます。また、VM が実行されている場合は、アプリケーション整合性復旧ポイントを取得できる可能性が最も高くなります。Azure Backup サービスは、VM がオフであり、拡張機能をインストールできない場合 (オフライン VM の場合) でも、VM のバックアップを続行します。この場合、復旧ポイントは、前述した*クラッシュ整合性*復旧ポイントになります。


## 制限事項

- Azure リソース マネージャーに基づく (別名 IaaS V2) 仮想マシンのバックアップはサポートされません。
- 16 台以上のデータ ディスクを搭載した仮想マシンのバックアップはサポートされません。
- Premium Storage を使用した仮想マシンのバックアップはサポートされません。
- 予約済み IP はあるがエンドポイントが定義されていない仮想マシンのバックアップはサポートされません。
- 復元中に既存の仮想マシンを置き換えることはサポートされません。まず既存の仮想マシンに関連付けられているディスクを削除し、次にバックアップからデータを復元します。
- リージョン間のバックアップと復元はサポートされません。
- Azure Backup サービスを使用した仮想マシンのバックアップは、Azure のすべてのパブリック リージョンでサポートされます。サポートされるリージョンについては、「[リージョン別のサービス](http://azure.microsoft.com/regions/#services)」を参照してください。目的のリージョンが現在サポートされていない場合は、資格情報コンテナーの作成時にドロップダウン リストに表示されません。
- オペレーティング システムのバージョンの選択でサポートされるのは、Azure Backup サービスを使用した仮想マシンのバックアップのみです。
  - **Linux**: Azure で動作保証済みのディストリビューションの一覧は、[こちら](../virtual-machines-linux-endorsed-distributions.md)でご確認ください。他の個人所有の Linux ディストリビューションも、仮想マシン上で VM エージェントが動作する限り使用できます。
  - **Windows Server**: Windows Server 2008 R2 より前のバージョンはサポートされていません。
- マルチ DC 構成の一部であるドメイン コントローラー VM の復元は、PowerShell を通じてのみサポートされます。詳細については、「[ドメイン コントローラーの VM の復元](backup-azure-restore-vms.md#restoring-domain-controller-vms)」を参照してください。

## 疑問がある場合
ご不明な点がある場合や確認したい機能が含まれている場合は、[フィードバックをお送りください](http://aka.ms/azurebackup_feedback)。

## 次のステップ

- [VM のバックアップ インフラストラクチャの計画](backup-azure-vms-introduction.md)
- [仮想マシンのバックアップ](backup-azure-vms.md)
- [仮想マシンのバックアップを管理する](backup-azure-manage-vms.md)

<!---HONumber=Nov15_HO4-->