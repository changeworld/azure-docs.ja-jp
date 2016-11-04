---
title: サーバーの削除と保護の無効化 | Microsoft Docs
description: この記事では、Site Recovery コンテナーからサーバーの登録を解除して、仮想マシンと物理サーバーの保護を無効にする方法を説明します。
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/05/2016
ms.author: raynew

---
# <a name="remove-servers-and-disable-protection"></a>サーバーの削除と保護の無効化
Azure Site Recovery サービスは、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。 コンピューターを Azure に、またはオンプレミスのセカンダリ データ センターにレプリケートできます。 簡単な概要については、「 [Site Recovery とは](site-recovery-overview.md)

## <a name="overview"></a>概要
この記事では、Site Recovery コンテナーからサーバーの登録を解除する方法と Site Recovery によって保護される仮想マシンの保護を無効にする方法について説明します。 

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="unregister-a-vmm-server"></a>VMM サーバーの登録解除
コンテナーから VMM サーバーの登録を解除するには、Azure Site Recovery ポータルの **[サーバー]** タブでサーバーを削除します。 以下の点に注意してください。

* **接続されている VMM サーバー**：Azure に接続されているときに、VMM サーバーの登録を解除することをお勧めします。 これにより、オンプレミスの VMM サーバーの設定、およびオンプレミスの VMM サーバーと関連付けられている VMM サーバー (削除するサーバー上のクラウドにマップされているクラウドを含む VMM サーバー) の設定が正しくクリーンアップされます。 接続に永続的な問題がある場合は、接続されていないサーバーのみを削除することをお勧めします。
* **接続されていない VMM サーバー**: VMM サーバーを削除するときに、VMM サーバーが接続されていない場合は、手動でスクリプトを実行して、クリーンアップを実行する必要があります。 スクリプトは、 [Microsoft ギャラリー](http://aka.ms/asr-cleanup-script-vmm)で使用可能です。 手動のクリーンアップ プロセスを完了するために、サーバーの VMM ID を書き留めてください。
* **クラスター内の VMM サーバー**: クラスターにデプロイされている VMM サーバーの登録を解除する必要がある場合は、次の操作を行います。
  
  * サーバーが接続されている場合は、 **[サーバー]** タブで、接続されている VMM サーバーを削除します。 サーバー上のプロバイダーをアンインストールするには、各クラスター ノードにログインし、コントロール パネルからアンインストールします。 登録エントリを削除するには、クラスター内のすべてのパッシブ ノードで、前のセクションに記載されたクリーンアップ スクリプトを実行します。
  * サーバーが接続されていない場合は、すべてのクラスター ノードでクリーンアップ スクリプトを実行する必要があります。

### <a name="unregister-an-unconnected-vmm-server"></a>接続されていない VMM サーバーの登録解除
削除する VMM サーバーで、次の手順を実行します。

1. Azure ポータルから VMM サーバーの登録を解除します。
2. VMM サーバーで、クリーンアップ スクリプトをダウンロードします。
3. [管理者として実行] オプションを使用して PowerShell を開き、既定 (LocalMachine) のスコープの実行ポリシーを変更します。
4. スクリプトの手順に従います。 

削除するサーバー上のクラウドとペアになっているクラウドが存在する VMM サーバーで、次の手順を実行します。

1. クリーンアップ スクリプトを実行し、ステップ 2 ～ 4 に従います。
2. 登録が解除されている VMM サーバーの VMM ID を指定します。 
3. このスクリプトは、VMM サーバーの登録情報とクラウドのペアリング情報を削除します。

## <a name="unregister-a-hyper-v-server-in-a-hyper-v-site"></a>Hyper-V サイトでの Hyper-V サーバーの登録解除
(VMM サーバーがない) Hyper-V サイトの Hyper-V サーバー上にある仮想マシンを保護するために Azure Site Recovery がデプロイされている場合、次のように、コンテナーから Hyper-V サーバーの登録を解除できます。

1. Hyper-V サーバーに配置された仮想マシンの保護を無効にします。
2. Azure Site Recovery ポータルの **[サーバー]** タブで、サーバーを選択してから、[削除] を選択します。 この操作を実行するときに、このサーバーが Azure に接続されている必要はありません。
3. 次のスクリプトを実行して、サーバー上の設定をクリーンアップし、コンテナーからサーバーの登録を解除します。 
   
        pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }
   
            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host
   
            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }
   
            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }
   
            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
   
            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."  
                    Remove-Item -Recurse -Path $registrationPath
                }
   
                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }
   
                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }
   
            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {   
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0] 
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd

## <a name="stop-protecting-a-hyper-v-virtual-machine"></a>Hyper-V 仮想マシンの保護を停止する
Hyper-V 仮想マシンの保護を停止する場合は、保護を削除する必要があります。 保護を削除する方法によっては、コンピューターで手動で保護設定をクリーンアップする必要があります。 

### <a name="remove-protection"></a>保護を削除する
1. クラウドのプロパティの **[仮想マシン]** タブで、仮想マシンを選択してから、**[削除]** を選択します。
2. **[仮想マシンの削除の確認]** ページには、2 つのオプションがあります。
   
   * **[保護を無効にする]**—このオプションを有効にして保存すると、仮想マシンは Site Recovery によって保護されなくなります。 仮想マシンの保護設定は、自動的にクリーンアップされます。
   * **[コンテナーから削除する]**—このオプションを選択すると、Site Recovery のコンテナーからのみ仮想マシンが削除されます。 仮想マシンのオンプレミスの保護設定には影響しません。 設定を手動でクリーンアップして保護設定を削除し、Azure サブスクリプションから仮想マシンを削除する必要があります。クリーンアップする必要がある保護設定を手動で削除するには、次の手順を使用します。

仮想マシンとそのハード ディスクを削除することを選択した場合は、それらはターゲットの場所から削除されます。

### <a name="clean-up-protection-settings-manually-(between-vmm-sites)"></a>保護設定を手動でクリーンアップする (VMM サイト間)
**[仮想マシンの管理の停止]**を選択した場合は、次のように手動で設定をクリーンアップします。

1. プライマリ サーバーで VMM コンソールからこのスクリプトを実行し、プライマリ仮想マシンの設定をクリーンアップします。 VMM コンソールで、[PowerShell] ボタンをクリックし、VMM PowerShell コンソールを開きます。 SQLVM1 を仮想マシンの名前に置き換えます。
   
         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection
2. 次のように、セカンダリ VMM サーバーでこのスクリプトを実行して、セカンダリ仮想マシンの設定をクリーンアップします。
   
        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
3. セカンダリ VMM サーバーで、スクリプトを実行した後に Hyper-V ホスト サーバー上の仮想マシンを更新し、セカンダリ仮想マシンが VMM コンソールで再検出されるようにします。
4. 上記の手順によって、VMM サーバーのみのレプリケーション設定がクリアされます。 仮想マシンの仮想マシン レプリケーションを削除する場合、 プライマリおよびセカンダリ仮想マシンの両方に対して以下の手順を実行する必要があります。 以下のスクリプトを実行してレプリケーションを削除し、SQLVM1 を仮想マシンの名前に置き換えます。
   
        Remove-VMReplication –VMName “SQLVM1”

### <a name="clean-up-protection-settings-manually-(between-on-premises-vmm-sites-and-azure)"></a>保護設定を手動でクリーンアップする (オンプレミスの VMM サイトと Azure の間)
1. 次のように、ソース VMM サーバーでこのスクリプトを実行して、プライマリ仮想マシンの設定をクリーンアップします。
   
        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
2. 上記の手順によって、VMM サーバーのみのレプリケーション設定がクリアされます。 VMM サーバーからレプリケーションを削除したら、このスクリプトを使用して Hyper-V ホスト サーバー上で実行されている仮想マシンのレプリケーションを削除してください。 SQLVM1 を仮想マシンの名前で、host01.contoso.com を Hyper-V ホスト サーバーの名前に置き換えます。
   
        $vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

### <a name="clean-up-protection-settings-manually-(between-hyper-v-sites-and-azure)"></a>保護設定を手動でクリーンアップする (Hyper-V サイトと Azure の間)
1. ソース Hyper-V ホスト サーバーで仮想マシンのレプリケーションを削除するには、このスクリプトを使用します。 SQLVM1 を仮想マシンの名前に置き換えます。
   
        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

## <a name="stop-protecting-a-vmware-virtual-machine-or-a-physical-server"></a>VMware 仮想マシンまたは物理サーバーの保護を停止する
VMware 仮想マシンまたは物理サーバーの保護を停止する場合は、保護を削除する必要があります。 保護を削除する方法によっては、コンピューターで手動で保護設定をクリーンアップする必要があります。 

### <a name="remove-protection"></a>保護を削除する
1. クラウドのプロパティの **[仮想マシン]** タブで、仮想マシンを選択してから、**[削除]** を選択します。
2. **[仮想マシンの削除]** で次のいずれかのオプションを選択します。
   
   * **[保護を無効にする (復旧ドリルとボリューム サイズ変更に使用)]**—このオプションを表示して有効化できるのは、次の場合のみです。
     
     * **仮想マシンのボリュームのサイズを変更している**—ボリュームのサイズを変更すると、仮想マシンが重大な状態になります。 このような場合、このオプションを選択します。 Azure での復旧ポイントを維持したまま、保護が無効になります。 マシンの保護を再度有効にすると、サイズが変更されたボリュームのデータが Azure に転送されます。
     * **フェールオーバーを実行している**—オンプレミスの VMware 仮想マシンまたは物理サーバーから Azure にフェールオーバーを実行して環境をテストした後、このオプションを選択してオンプレミスの仮想マシンの保護をもう一度開始します。 このオプションにより各仮想マシンが無効になり、各マシンの保護を再度有効にしなければならなくなります。 以下の点に注意してください。
       * この設定を使用して仮想マシンを無効にしても、Azure のレプリカ仮想マシンには影響しません。
       * 仮想マシンからモビリティ サービスをアンインストールしないでください。
   * **[保護を無効にする]**—このオプションを有効にして保存すると、マシンは Site Recovery によって保護されなくなります。 マシンの保護設定は、自動的にクリーンアップされます。
   * **[コンテナーから削除する]**—このオプションを選択すると、Site Recovery のコンテナーからのみマシンが削除されます。 マシンのオンプレミスの保護設定には影響しません。 マシンの設定を削除したり、Azure のサブスクリプションから仮想マシンを削除したりするには、モビリティ サービスをアンインストールして、設定をクリーンアップする必要があります。
     
       ![オプションを削除する](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

<!--HONumber=Oct16_HO2-->


