---
title: Azure Migrate での移行に向けてマシンを準備する
description: Azure Migrate での移行に向けてオンプレミス マシンを準備する方法について説明します。
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "78927471"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Azure への移行に向けてオンプレミスのマシンの準備を整える

この記事では、オンプレミスのマシンを Azure に移行する前にマシンに対して必要になる準備作業の手順を紹介します (移行には、[Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) を使用)。

この記事では、次の内容について説明します。
> [!div class="checklist"]
> * 移行に関する制約を確認する。
> * オペレーティング システムの要件とサポートに関する制約を確認する。
> * 移行するマシンの URL およびポートへのアクセスを確認する。
> * 移行を開始する前に必要になることがある変更を確認する。
> * 移行後もドライブ文字が保持されるように設定を構成する。
> * 移行後の Azure VM に接続できるようにマシンの準備を整える。

## <a name="verify-migration-limitations"></a>移行に関する制約を確認する

- Azure Migrate Server Migration を使用した Azure Migrate プロジェクトでは、プロジェクト 1 件につき VMware VM または Hyper-V VM を 35,000 台まで評価できます。 VMware VM と Hyper-V VM は、それぞれの上限に達するまでは任意に組み合わせてプロジェクトに含めることができます。
- 1 回の移行の対象として選択できる VM の数は 10 台までです。 レプリケートが必要な数がこれよりも多い場合には、10 台のグループ単位でレプリケートしてください。
- VMware のエージェントレス移行では、最大 100 個のレプリケーションを同時に実行できます。

## <a name="verify-operating-system-requirements"></a>オペレーティング システムの要件を確認する

- お使いの [Windows オペレーティング システム](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)が Azure でサポートされていることを確認します。
- お使いの [Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md)が Azure でサポートされていることを確認します。

## <a name="see-whats-supported"></a>サポート対象

VMware VM については、Server Migration で[エージェントレスとエージェントベースの 2 種類の移行](server-migrate-overview.md)がサポートされています。

- **VMware VM**:VMware VM の[移行の要件とサポート](migrate-support-matrix-vmware-migration.md)を確認してください。
- **Hyper-V VM**:Hyper-V VM については、[移行の要件とサポート](migrate-support-matrix-hyper-v-migration.md)を確認してください。
- **物理マシン**: オンプレミスの物理マシンおよび他の仮想化されたサーバーについては、[移行の要件とサポート](migrate-support-matrix-physical-migration.md)を確認してください。 

## <a name="review-url-and-port-access"></a>URL およびポートに対するアクセスを確認する

移行中にマシンがインターネットにアクセスすることが必要になることがあります。

- **Azure Migrate アプライアンス**: エージェントレス移行中に Azure Migrate アプライアンスがアクセスする必要がある [URL](migrate-appliance.md#url-access) と[ポート](migrate-support-matrix-vmware-migration.md#agentless-ports)を確認してください。
- **VMware VM エージェントベースの移行**: VMware VM エージェントベースの移行中にレプリケーション アプライアンスが使用する [URL](migrate-replication-appliance.md#url-access) と[ポート](migrate-replication-appliance.md#port-access)を確認します。 
- **Hyper-V ホスト**: 移行中に Hyper-V ホストがアクセスする必要がある [URL とポート](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts)を確認します。 
- **物理サーバー**:物理サーバーの移行中にレプリケーション アプライアンスが使用する [URL](migrate-replication-appliance.md#url-access) と[ポート](migrate-replication-appliance.md#port-access)を確認します。

## <a name="verify-required-changes-before-migrating"></a>移行前に必要な変更を確認する

一部の VM は、Azure で実行できるように変更が必要な場合があります。 ここに挙げたオペレーティング システムが稼働している VM では、必要な変更が Azure Migrate によって自動的に行われます。

- Red Hat Enterprise Linux 7.0+、6.5+
- CentOS 7.0+、6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS、16.04LTS、14.04LTS
- Debian 8、7

その他のオペレーティング システムについては、移行前に手動でマシンの準備を整える必要があります。 

### <a name="prepare-windows-machines"></a>Windows マシンの準備作業

Windows マシンを移行する場合は、移行の前に次の変更を行ってください。 これらの変更せずに VM を移行すると、Azure で VM が起動しなくなることがあります。

1. Azure VM の [Azure シリアル コンソールを有効](../virtual-machines/troubleshooting/serial-console-windows.md)にします。 コンソールを有効にすることはトラブルシューティングに役立ちます。 VM を再起動する必要はありません。 Azure VM は、ディスク イメージを使用して起動します。 ディスク イメージ ブートは、新しい VM の再起動に相当します。 
2. Windows Server 2003 を実行中のマシンを移行する場合は、Hyper-V ゲスト統合サービスを VM のオペレーティング システムにインストールしてください。 [詳細については、こちらを参照してください](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)。

### <a name="prepare-linux-machines"></a>Linux マシンの準備作業

1. Hyper-V Linux 統合サービスをインストールします。 新しいバージョンの Linux ディストリビューションには、ほとんどの場合、Hyper-V Linux 統合サービスが既定で含まれています。
2. 必要な Hyper-V ドライバーが含まれるように Linux の init イメージをリビルドします。 init イメージをリビルドすることにより、Azure で確実に VM が起動します。これが必要なのは一部のディストリビューションだけです。
3. [Azure シリアル コンソールのログ記録を有効にします](../virtual-machines/troubleshooting/serial-console-linux.md)。 コンソールのログ記録を有効にすることはトラブルシューティングに役立ちます。 VM を再起動する必要はありません。 Azure VM は、ディスク イメージを使用して起動します。 ディスク イメージ ブートは、新しい VM の再起動に相当します。
4. 永続的なデバイス識別子を使用するよう、デバイス名とボリュームの関連付けが含まれているデバイスのマップ ファイルを更新します。
5. 永続的なボリューム識別子を使用するよう、fstab ファイルのエントリを更新します。
6. MAC アドレスなどに基づいてインターフェイス名を予約する udev ルールを削除します。
7. DHCP から IP アドレスを受け取るよう、ネットワーク インターフェイスを更新します。

詳細については、[Azure 上で Linux VM を稼働させるための手順](../virtual-machines/linux/create-upload-generic.md)のページを参照してください。同ページでは、一部の人気 Linux ディストリビューションを対象とした手順も紹介しています。

## <a name="preserve-drive-letters-after-migration"></a>移行後もドライブ文字を保持する

オンプレミスのマシンを Microsoft Azure に移行すると、追加のデータ ディスクのドライブ文字が元の値から変化することがあります。 

既定では、Azure VM に一時ストレージとして使用するドライブ D が割り当てられます。 このドライブが割り当てられると、アタッチされている他のストレージ ドライブに割り当てられている文字が 1 文字ずつ後ろにずれることになります。 たとえば、オンプレミスのインストールでデータ ディスクを使用しており、そのディスクにアプリケーションのインストール用にドライブ D が割り当てられていた場合に、その VM を Azure に移行すると、ドライブに割り当てられている文字が 1 つ後ろにずれ、ドライブ E に変わります。 この自動的な割り当てを回避し、Azure により一時ボリュームに割り当てられるドライブ文字を未使用のアルファベットのうち順番が最も前のものにするには、記憶域ネットワーク (SAN) ポリシーを **OnlineAll** に設定します。

1. (ホストサーバーではなく) オンプレミスのマシンで、管理者特権でのコマンド プロンプトを開きます。
2. 「**diskpart**」と入力します。
3. 「**SAN**」と入力します。 ゲスト オペレーティング システムのドライブ文字が維持されていない場合には、**Offline All** または **Offline Shared** が返されます。
4. **DISKPART** プロンプトで、「**SAN Policy=OnlineAll**」と入力します。 この設定により、ディスクがオンラインになり、両方のディスクの読み取りと書き込みが可能になります。
5. テスト移行中に、ドライブ文字が保持されていることを確認できます。

## <a name="check-azure-vm-requirements"></a>Azure VM の要件を確認する

オンプレミスのマシンを Azure にレプリケートする場合には、オペレーティング システムとアーキテクチャ、ディスク、ネットワーク設定、および VM 名に関する Azure VM の要件を順守している必要があります。 移行前に、[VMware VM と物理サーバー](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)の要件および [Hyper-V VM](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) の要件を確認してください。

## <a name="prepare-to-connect-after-migration"></a>移行後に接続するための準備を整える

Azure への移行中には、Azure VM が作成されます。 移行後に、この新しい Azure VM に接続できるようにする必要があります。 正常に接続するためには、いくつかの手順を踏む必要があります。

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Azure の Windows VM に対する接続を準備する

オンプレミスの Windows マシンで、次の手順を実行します。

1. Windows 設定を構成します。 設定には、すべての静的な固定ルートまたは WinHTTP プロキシを削除することが含まれます。
2. [必要なサービス](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)が実行されていることを確認します。
3. オンプレミスのマシンへのリモート接続を許可するために、リモート デスクトップ (RDP) を有効にします。 [PowerShell を使用して RDP を有効にする](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)方法に関するページを参照してください。
4. 移行後にインターネット経由で Azure VM にアクセスする場合は、オンプレミスのマシンの Windows ファイアウォールにおいて、パブリック プロファイルで TCP と UDP を許可したうえで、すべてのプロファイルで許可されたアプリとして RDP を設定します。
5. 移行後にサイト間 VPN 経由で Azure VM にアクセスする場合は、オンプレミスのマシンの Windows ファイアウォールにおいて、ドメイン プロファイルとプライベート プロファイルで RDP を許可します。 [RDP トラフィックを許可する](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)方法に関するページを参照してください。 
6. 移行する際は、オンプレミスの VM に保留中の Windows 更新プログラムがないことを確認してください。 更新プログラムがあると、移行後に Azure VM 上で更新プログラムのインストールが始まることがあり、更新が完了するまで VM にサインインできなくなります。

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Linux の Azure VM に対する接続を準備する

オンプレミスの Linux マシンで、次の手順を実行します。

1. システム起動時に Secure Shell サービスが自動的に開始される設定になっていることを確認します。
2. ファイアウォール規則で SSH 接続が許可されていることを確認します。

### <a name="configure-azure-vms-after-migration"></a>移行後に Azure VM を構成する

移行後は、作成された Azure VM で次の手順を実行します。

1. インターネット経由で VM に接続できるように、VM にパブリック IP アドレスを割り当てます。 Azure VM には、オンプレミスのマシンとは異なるパブリック IP アドレスを使用する必要があります。 [詳細については、こちらを参照してください](../virtual-network/virtual-network-public-ip-address.md)。
2. VM 上のネットワーク セキュリティ グループ (NSG) 規則で RDP または SSH ポートへの受信接続が許可されていることを確認します。
3. [[ブート診断]](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) をオンにして VM を表示します。

> [!NOTE]
> Azure Bastion サービスでは、Azure VM へのプライベート RDP および SSH アクセスが提供されます。 このサービスの詳細については、[こちら](../bastion/bastion-overview.md)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure に [VMware VM を移行する](server-migrate-overview.md)のか、[Hyper-V VM ](tutorial-migrate-hyper-v.md) を移行するのか、あるいは[物理サーバー、仮想化された VM、またはクラウドの VM](tutorial-migrate-physical-virtual-machines.md) を移行するのかに応じて、使用する方法を決めます。
