---
title: CloudSimple による Azure VMware ソリューション クイック スタート - Azure で VMware VM を使用する
description: このクイック スタートでは、CloudSimple による Azure VMware ソリューションを使用して、Azure portal から VMware VM を構成して使用する方法を学習します
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dd4faf6df54d478654c59ffc18bf8c5873d576b9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816655"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>クイック スタート:Azure での VMware VM の使用

Azure portal で仮想マシンを作成するには、プライベート クラウド vCenter で利用可能な仮想マシン テンプレートを使用できます。 vCenter の管理者は、プライベート クラウドで追加のテンプレートを作成できます。

## <a name="create-a-vm-template"></a>VM テンプレートを作成する

まず、プライベート クラウドで vCenter UI を使用して仮想マシンを作成します。 テンプレートを作成するには、VMware の記事「[Clone a Virtual Machine to a Template in the vSphere Web Client (vSphere Web Client でテンプレートに仮想マシンを複製する)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)」の手順に従います。 プライベート クラウド vCenter に VM テンプレートを格納します。

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Azure portal で仮想マシンを作成する

1. **[すべてのサービス]** を選択します。

2. **CloudSimple 仮想マシン**を検索します。

3. **[追加]** を選択します。

    ![[追加] を選択する](media/create-cloudsimple-virtual-machine.png)

4. 仮想マシンに関する以下の情報を入力し、 **[次へ: サイズ]** を選択します。

    ![CloudSimple 仮想マシンを作成する - 基本](media/create-cloudsimple-virtual-machine-basic-info.png)

    | フィールド | 説明 |
    | ------------ | ------------- |
    | **サブスクリプション** | プライベート クラウドに関連付けられている Azure サブスクリプション。  |
    | **リソース グループ** | VM が割り当てられるリソース グループ。 既存のグループを選択することも、新しいものを作成することもできます。 |
    | **Name** | VM を識別する名前。  |
    | **Location** | VM がホストされている Azure リージョン。  |
    | **プライベート クラウド** | VM を作成する CloudSimple プライベート クラウド。 |
    | **ResourcePool** | VM のマップされたリソース プール。 使用可能なリソース プールから選択します。 |
    | **vSphere テンプレート** | VM の vSphere テンプレート。  |
    | **ユーザー名** | VM 管理者のユーザー名 (Windows テンプレートの場合)。|
    | **パスワード** |  VM 管理者のパスワード (Windows テンプレートの場合)。 |
    | **パスワードの確認** | 前のフィールドで指定したパスワード。 |

5. VM のコア数とメモリ容量を選択します。 ゲスト オペレーティング システムに完全な CPU 仮想化を公開する場合は、 **[Expose to Guest OS]\(ゲスト OS に公開\)** をオンにします。 ハードウェアの仮想化を必要とするアプリケーションは、バイナリ変換や準仮想化をせずに、仮想マシン上で実行できます。 詳細については、VMware の記事「<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Expose VMware Hardware Assisted Virtualization</a>」 (VMware ハードウェア アシストによる仮想化の公開) を参照してください。 終了したら、 **[次へ: 構成]** を選択します。

    ![CloudSimple 仮想マシンを作成する - サイズ](media/create-cloudsimple-virtual-machine-size.png)

6. 以下の表の説明に従って、ネットワーク インターフェイスとディスクを構成し、 **[確認および作成]** を選択します。

    ![CloudSimple 仮想マシンを作成する - 構成](media/create-cloudsimple-virtual-machine-configurations.png)

    ネットワーク インターフェイスについては、 **[Add network interface]\(ネットワーク インターフェイスの追加\)** を選択して、以下の設定を構成します。
    
    | Setting | 説明 |
    | ------------ | ------------- |
    | **Name** | インターフェイスを識別する名前を入力します。  |
    | **ネットワーク** | プライベート クラウド vSphere で構成されている分散ポート グループの一覧から選択します。  |
    | **アダプター** | VM 用に構成されている使用可能な種類の一覧から vSphere アダプターを選択します。 詳細については、VMware の記事の「<a href="https://kb.vmware.com/s/article/1001805" target="_blank">Choosing a network adapter for your virtual machine (仮想マシンのネットワーク アダプターの選択)</a>」を参照してください。 |
    | **Power on at Boot (起動時の電源投入)** | VM が起動したときに NIC ハードウェアを有効にするかどうかを選択します。 既定値は **[有効化]** です。 |

    ディスクについては、 **[ディスクの追加]** を選択し、以下の設定を構成します。

    | Setting | 説明 |
    | ------------ | ------------- |
    | **Name** | ディスクを識別する名前を入力します。  |
    | **サイズ** | 使用可能なサイズのいずれかを選択します。  |
    | **SCSI コントローラー** | ディスクの SCSI コントローラーを選択します。  |
    | **モード** | モードは、ディスクがスナップショットに参加する方法を指定します。 次のいずれかのオプションを選択します。 <br> **[Independent persistent]\(独立永続\)** : ディスクに書き込まれたすべての変更が永続的に書き込まれます。<br> **[Independent non-persistent]\(独立非永続\)** : ディスクに書き込まれた変更は、仮想マシンの電源をオフにしたとき、またはリセットしたときに破棄されます。 独立非永続モードでは、常に同じ状態で VM を再起動できます。 詳細については、<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware のドキュメント</a>を参照してください。

7. 検証の完了後、設定を確認し、 **[作成]** を選択します。 変更するには、上部にあるタブを選択するか、 **[前へ: 構成]** を選択します。

    ![CloudSimple 仮想マシンを作成する - 確認と作成](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>次の手順

* [CloudSimple 仮想マシンのリストを表示する](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Azure から CloudSimple 仮想マシンを管理する](https://docs.azure.cloudsimple.com/azure-manage-vm/)
