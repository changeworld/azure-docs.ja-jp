---
title: Azure Migrate での VMware 移行のサポート
description: Azure Migrate での VMware VM 移行のサポートについて説明します。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e33811563063c0f8eb94b9927d07596d51cd45e4
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030926"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 移行のサポートマトリックス

この記事では、[Azure Migrate を使用した VMware VM 移行サポートの設定と制限事項について説明します。Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) Azure に移行するための VMware VM 評価に関する情報をお探しの場合は、[評価サポートマトリックス](migrate-support-matrix-vmware.md)を確認してください。


## <a name="migration-options"></a>移行オプション

VMware VM は、次のいくつかの方法で移行できます。

- エージェントレスの移行:VM に何もインストールする必要なく VM を移行します。 エージェントレスで移行するには、[Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。
- エージェントベースの移行:レプリケーションのために VM 上にエージェントをインストールします。 エージェントベースで移行するには、[レプリケーション アプライアンス](migrate-replication-appliance.md)をデプロイする必要があります。

[この記事](server-migrate-overview.md)を参照し、あなたが使用したい方法の詳細を確認してください。

## <a name="migration-limitations"></a>移行の上限

- レプリケーションでは、一度に最大 10 個の VM を選択できます。 より多くのマシンを移行する場合は、10 個単位のグループでレプリケートします。
- VMware のエージェントレス移行では、最大 100 個のレプリケーションを同時に実行できます。

## <a name="agentless-vmware-servers"></a>Agentless-VMware サーバー

**VMware** | **詳細**
--- | ---
**VMware vCenter サーバー** | バージョン 5.5、6.0、6.5、または 6.7。
**VMware vSphere ESXI ホスト** | バージョン 5.5、6.0、6.5、または 6.7。
**vCenter Server のアクセス許可** | エージェントレスの移行では、[Migrate Appliance](migrate-appliance.md) を使用します。 アプライアンスには、次のアクセス許可が必要です。<br/><br/> - **Datastore.Browse**:VM のログ ファイルの閲覧を許可して、スナップショットの作成と削除のトラブルシューティングを行います。<br/><br/> **Datastore.LowLevelFileOperations**:データストア ブラウザーでの読み取り、書き込み、削除、名前変更の各操作を許可して、スナップショットの作成と削除のトラブルシューティングを行います。<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking**:VM ディスクに対する変更のトラッキングの有効化/無効化を許可し、スナップショット間における変更済みのデータ ブロックをプルします。<br/><br/> - **VirtualMachine.Configuration.DiskLease**:VM のディスクのリース操作を許可し、VMware vSphere Virtual Disk Development Kit (VDDK) を使用してディスクを読み取ります。<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess**:VM 上のディスクを開くことを許可し、VDDK を使用してディスクを読み取ります。<br/><br/> - **VirtualMachine.Provisioning.AllowVirtualMachineDownload**:VM に関連付けられたファイルの読み取り操作を許可し、エラーが発生した場合にログをダウンロードして、トラブルシューティングを行います。<br/><br/> - **VirtualMachine.SnapshotManagement.***:レプリケーション用に VM スナップショットの作成と管理を許可します。<br/><br/> - **Virtual Machine.Interaction.Power Off**:Azure への移行中に VM の電源をオフにすることを許可します。



## <a name="agentless-vmware-vms"></a>Agentless-VMware VM

**サポート** | **詳細**
--- | ---
**サポートされているオペレーティング システム** | Azure でサポートされる [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システムは、エージェントレス移行を使用して移行できます。
**Azure に必要な変更** | 一部の VM は、Azure で実行できるように変更が必要な場合があります。 次のオペレーティング システムでは、これらの変更が Azure Migrate によって自動的に行われます。<br/> - Red Hat Enterprise Linux 6.5+、7.0+<br/> - CentOS 6.5+、7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS、16.04LTS、18.04LTS<br/> - Debian 7、8<br/><br/> その他のオペレーティング システムについては、移行前に手動で調整する必要があります。 関連する記事には、その手順が記載されています。
**Linux ブート** | /boot が専用パーティションに存在する場合は、OS ディスク上に存在する必要があり、複数のディスクに分散していてはいけません。<br/> /boot がルート (/) パーティションに含まれている場合は、"/" パーティションは OS ディスク上に存在する必要があり、他のディスクにまたがっていてはいけません。
**UEFI ブート** | UEFI ブートを使用した VM の移行はサポートされません。
**ディスク サイズ** | 2 TB の OS ディスク。データ ディスク用に 4 TB。
**ディスクの制限** |  VM あたり最大 60 台のディスク。
**暗号化されたディスクまたはボリューム** | 暗号化されたディスクまたはボリュームを含む VM の移行はサポートされません。
**共有ディスク クラスター** | サポートされていません。
**独立ディスク** | サポートされていません。
**RDM またはパススルー ディスク** | RDM またはパススルー ディスクが VM に存在する場合、これらのディスクは Azure にレプリケートされません。
**NFS** | VM 上のボリュームとしてマウントされた NFS ボリュームはレプリケートされません。
**iSCSI ターゲット** | iSCSI ターゲットを含む VM は、エージェントレス移行ではサポートされていません。
**マルチパス IO** | サポートされていません。
**ストレージ vMotion** | サポートされていません。 VM でストレージ vMotion を使用している場合、レプリケーションは機能しません。
**チーミングされた NIC** | サポートされていません。
**IPv6** | サポートされていません。
**ターゲット ディスク** | VM は、Azure 内のマネージド ディスク (Standard HDD、Premium SSD) にのみ移行できます。
**同時レプリケーション** | vCenter Server あたり 100 台の VM。 それ以上ある場合は、100 台単位のバッチで移行します。


## <a name="agentless-azure-migrate-appliance"></a>Agentless-Azure Migrate のアプライアンス 
エージェントレスの移行では、VMware VM にデプロイされた Azure Migrate アプライアンスを使用します。

- VMware の[アプライアンスの要件](migrate-appliance.md#appliance---vmware)を確認してください。
- アプライアンスがアクセスする必要のある [URL](migrate-appliance.md#url-access) を確認してください。

## <a name="agentless-ports"></a>エージェントレス - ポート

**[デバイス]** | **[接続]**
--- | ---
アプライアンス | ポート 443 で、レプリケートされたデータを Azure にアップロードしたり、レプリケーションや移行のために Azure Migrate と通信を行ったりするためのアウトバウンド接続。
vCenter サーバー | ポート 443 で、アプライアンスがレプリケーションを調整 (スナップショットの作成、データのコピー、スナップショットのリリース) するためのインバウンド接続
vSphere または EXSI ホスト | TCP ポート 902 で、アプライアンスがスナップショットからデータをレプリケートするためのインバウンド接続。


## <a name="agent-based-vmware-servers"></a>エージェントベース - VMware サーバー
この表は、VMware 仮想化サーバーに対する評価のサポートと制限をまとめたものです。

**VMware の要件** | **詳細**
--- | ---
**VMware vCenter サーバー** | バージョン 5.5、6.0、6.5、または 6.7。
**VMware vSphere ESXI ホスト** | バージョン 5.5、6.0、6.5、または 6.7。
**vCenter Server のアクセス許可** | vCenter Server 用の読み取り専用アカウント。

## <a name="agent-based-vmware-vms"></a>エージェントベース - VMware VM

この表は、エージェントベースの移行を使用して移行させたい VMware VM 向けの VMware VM サポートをまとめたものです。

**サポート** | **詳細**
--- | ---
**マシンのワークロード** | Azure Migrate は、サポートされているマシンで実行されている任意のワークロード (Active Directory、SQL サーバーなど) の移行をサポートします。
**オペレーティング システム** | 最新情報については、Site Recovery に関する[オペレーティング システムのサポート](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)を確認してください。 Azure Migrate でも同じ VM オペレーティング システムがサポートされます。
**Linux ファイル システムとゲストのストレージ** | 最新情報については、Site Recovery に関する [Linux ファイル システムのサポート](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)を確認してください。 Azure Migrate でも同じ Linux ファイル システムがサポートされます。
**ネットワークとストレージ** | 最新情報については、Site Recovery の[ネットワーク](../site-recovery/vmware-physical-azure-support-matrix.md#network)と[ストレージ](../site-recovery/vmware-physical-azure-support-matrix.md#storage)の前提条件を確認してください。 Azure Migrate の場合も、ネットワークとストレージの要件は同じです。
**Azure の要件** | 最新情報については、Site Recovery に使用する [Azure ネットワーク](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[ストレージ](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)、[コンピューティング](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)の要件を確認してください。 VMware の移行に関する要件は、Azure Migrate の場合も同じです。
**モビリティ サービス** | 移行対象となる各 VM に、Mobility Service エージェントがインストールされている必要があります。
**UEFI ブート** | Azure 内の移行された VM は、自動的に BIOS ブート VM に変換されます。<br/><br/> OS ディスクには最大 4 つのパーティションが必要で、ボリュームは NTFS でフォーマットされている必要があります。
**ターゲット ディスク** | VM は、Azure 内のマネージド ディスク (Standard HDD、Premium SSD) にのみ移行できます。
**ディスク サイズ** | 2 TB の OS ディスク。データ ディスク用に 8 TB。
**ディスクの制限** |  VM あたり最大 63 台のディスク。
**暗号化されたディスクまたはボリューム** | 暗号化されたディスクまたはボリュームを含む VM の移行はサポートされません。
**共有ディスク クラスター** | サポートされていません。
**独立ディスク** | サポートされています。
**パススルー ディスク** | サポートされています。
**NFS** | VM 上のボリュームとしてマウントされた NFS ボリュームはレプリケートされません。
**iSCSI ターゲット** | iSCSI ターゲットを含む VM は、エージェントレス移行ではサポートされていません。
**マルチパス IO** | サポートされていません。
**ストレージ vMotion** | サポートされています
**チーミングされた NIC** | サポートされていません。
**IPv6** | サポートされていません。




## <a name="agent-based-replication-appliance"></a>エージェント ベースのレプリケーション アプライアンス 

Azure Migrate ハブで提供されている OVA テンプレートを使用してレプリケーション アプライアンスを設定すると、そのアプライアンスは Windows Server 2016 を実行し、サポート要件に準拠しています。 物理サーバーにレプリケーション アプライアンスを手動で設定する場合は、サーバーが要件に準拠していることを確認してください。

- VMware の[レプリケーション アプライアンスの要件](migrate-replication-appliance.md#appliance-requirements)を確認してください。
- アプライアンスに MySQL がインストールされている必要があります。 [インストール オプション](migrate-replication-appliance.md#mysql-installation)を確認してください。
- レプリケーション アプライアンスがアクセスする必要のある [URL](migrate-replication-appliance.md#url-access) を確認してください。

## <a name="azure-vm-requirements"></a>Azure VM の要件

Azure にレプリケートされたオンプレミス VM はすべて、この表にまとめられている Azure VM の要件を満たしている必要があります。 Site Recovery がレプリケーションの前提条件確認を実行するとき、満たされていない要件がある場合には確認が失敗します。

**コンポーネント** | **必要条件** | **詳細**
--- | --- | ---
ゲスト オペレーティング システム | 移行のためにサポートされている VMware VM オペレーティン グシステムを検証します。<br/> サポートされているオペレーティング システム上で実行されているすべてのワークロードを移行できます。 | サポートされていない場合、確認は失敗します。
ゲスト オペレーティング システムのアーキテクチャ | 64 ビット。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク サイズ | 最大 2,048 GB。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク数 | 1 | サポートされていない場合、確認は失敗します。
データ ディスク数 | 64 以下。 | サポートされていない場合、確認は失敗します。
データ ディスク サイズ | 最大 4,095 GB | サポートされていない場合、確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
共有 VHD | サポートされていません。 | サポートされていない場合、確認は失敗します。
FC ディスク | サポートされていません。 | サポートされていない場合、確認は失敗します。
BitLocker | サポートされていません。 | マシンのレプリケーションを有効にする前に、BitLocker を無効にする必要があります。
VM 名 | 1 から 63 文字。<br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。<br/><br/> マシン名の最初と最後は、文字か数字とする必要があります。 |  Site Recovery でマシンのプロパティの値を更新します。
移行後の接続 - Windows | 移行後に、Windows が実行されている Azure VM に接続するには:<br/> - 移行前に、オンプレミスの VM で RDP を有効にします。 TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、 **[Windows ファイアウォール]**  >  **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。<br/> サイト間 VPN アクセスの場合は、RDP を有効にし、 **[Windows ファイアウォール]**  ->  **[許可されたアプリおよび機能]** で**ドメイン ネットワークとプライベート ネットワーク**の RDP を許可します。 さらに、オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細については、こちらを参照してください](prepare-for-migration.md)。 |
移行後の接続 - Linux | 移行後に、SSH を使用して Azure VM に接続するには:<br/> 移行前に、オンプレミスのマシンで、Secure Shell サービスが [開始] に設定されていることと、ファイアウォール規則で SSH 接続が許可されていることを確認します。<br/> フェールオーバー後の Azure VM で、フェールオーバーされた VM とその接続先の Azure サブネットのネットワーク セキュリティ グループ規則について、SSH ポートへの受信接続を許可します。 さらに、VM のパブリック IP アドレスを追加します。 |  


## <a name="next-steps"></a>次のステップ

VMware 移行のオプションを[選択](server-migrate-overview.md)します。
