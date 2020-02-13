---
title: Azure VMware Solutions (AVS) - ワークロード VM を AVS プライベート クラウドに移行する
description: オンプレミスの vCenter から AVS プライベート クラウドの vCenter に仮想マシンを移行する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: af02bd947c73b670306704a10a09ca981b34c9a9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019997"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-avs-private-cloud-vcenter-environment"></a>オンプレミスの vCenter から AVS プライベート クラウドの vCenter 環境にワークロード VM を移行する

オンプレミスのデータセンターから AVS プライベート クラウドに VM を移行するには、いくつかのオプションを使用できます。 AVS プライベート クラウドでは VMware vCenter へのネイティブ アクセスが提供されており、VMware でサポートされているツールをワークロードの移行に使用できます。 この記事では、vCenter のいくつかの移行オプションについて説明します。

## <a name="prerequisites"></a>前提条件

オンプレミスのデータセンターから VM とデータを移行するには、データセンターから AVS プライベート クラウド環境へのネットワーク接続が必要です。 ネットワーク接続を確立するには、次のいずれかの方法を使用します。

* オンプレミス環境と AVS プライベート クラウドの間の[サイト間 VPN 接続](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* オンプレミスの ExpressRoute 回線と AVS ExpressRoute 回線の間の ExpressRoute Global Reach 接続。

vMotion を使用した VM の移行のために、オンプレミスの vCenter 環境から AVS プライベート クラウドへのネットワーク パスが利用可能である必要があります。 オンプレミスの vCenter の vMotion ネットワークには、ルーティング機能が必要です。 オンプレミスの vCenter と AVS プライベート クラウドの vCenter の間のすべての vMotion トラフィックがファイアウォールで許可されていることを確認します。 (AVS プライベート クラウドでは、vMotion ネットワークでのルーティングは既定で構成されています)。

## <a name="migrate-isos-and-templates"></a>ISO とテンプレートを移行する

AVS プライベート クラウドに新しい仮想マシンを作成するには、ISO と VM テンプレートを使用します。 ISO とテンプレートを AVS プライベート クラウドの vCenter にアップロードして使用できるようにするには、次の方法を使用します。

1. vCenter UI から AVS プライベート クラウドの vCenter に ISO をアップロードします。
2. AVS プライベート クラウドの vCenter で[コンテンツ ライブラリを発行](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html)します。

    1. オンプレミスのコンテンツ ライブラリを発行します。
    2. AVS プライベート クラウドの vCenter に新しいコンテンツ ライブラリを作成します。
    3. 発行されたオンプレミスのコンテンツ ライブラリをサブスクライブします。
    4. サブスクライブしたコンテンツにアクセスするためにコンテンツ ライブラリを同期します。

## <a name="migrate-vms-using-powercli"></a>PowerCLI を使用して VM を移行する

オンプレミスの vCenter から AVS プライベート クラウドの vCenter に VM を移行するには、VMware PowerCLI または VMware Labs から入手できる Cross vCenter Workload Migration Utility を使用します。 次のサンプル スクリプトは、PowerCLI 移行コマンドを示しています。

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> 宛先の vCenter サーバーと ESXi ホストの名前を使用するには、オンプレミスから AVS プライベート クラウドへの DNS 転送を構成します。

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>NSX レイヤー 2 VPN を使用して VM を移行する

このオプションを使用すると、オンプレミスの VMware 環境から Azure の AVS プライベート クラウドへのワークロードのライブ マイグレーションを実行できます。 この拡張レイヤー 2 ネットワークを使用すると、オンプレミスからのサブネットを AVS プライベート クラウドで使用できるようになります。 移行後、VM に新しい IP アドレスの割り当ては必要ありません。

「[レイヤー 2 拡張済みネットワークを使用したワークロードの移行](migration-layer-2-vpn.md)」では、レイヤー 2 VPN を使用して、レイヤー 2 ネットワークをオンプレミス環境から AVS プライベート クラウドに拡張する方法について説明されています。

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>バックアップ ツールとディザスター リカバリー ツールを使用して VM を移行する

VM の AVS プライベート クラウドへの移行は、バックアップまたは復元ツールとディザスター リカバリー ツールを使用して行うことができます。 サードパーティのツールを使用して作成されたバックアップからの復元のターゲットとして、AVS プライベート クラウドを使用します。 また、AVS プライベート クラウドは、VMware SRM またはサードパーティのツールを使用したディザスター リカバリーのターゲットとして使用することもできます。

これらのツールの詳細な使用方法については、次のトピックを参照してください。

* [Veeam B&R を使用して AVS プライベート クラウドでワークロード仮想マシンをバックアップする](backup-workloads-veeam.md)
* [AVS プライベート クラウドをオンプレミスの VMware ワークロード用のディザスター リカバリー サイトとして設定する](disaster-recovery-zerto.md)
