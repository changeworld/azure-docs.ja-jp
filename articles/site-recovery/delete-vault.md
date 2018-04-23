---
title: Site Recovery コンテナーを削除する
description: サイトの回復シナリオに基づいて、Azure Site Recovery コンテナーを削除する方法を説明します。
service: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 62792747b4efe2de4c22af6f0886503d7d63ed44
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="delete-a-site-recovery-vault"></a>Site Recovery コンテナーを削除する
依存関係により、Azure Site Recovery コンテナーが削除できない場合があります。 実行する必要のあるアクションは、サイトの回復シナリオ (VMware から Azure、Hyper-v (System Center Virtual Machine Manager あり/なし) から Azure と Azure Backup) によって異なります。 Azure Backup で使用されているコンテナーを削除するには、「[Azure のバックアップ コンテナーを削除する](../backup/backup-azure-delete-vault.md)」を参照してください。



## <a name="delete-a-site-recovery-vault"></a>Site Recovery コンテナーを削除する 
コンテナーを削除するには、シナリオに応じた推奨手順に従ってください。

### <a name="vmware-vms-to-azure"></a>VMware VM を Azureに

1. 保護されている VM をすべて削除するには、「[VMware VM の保護の無効化](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)」の手順に従います。

2. レプリケーション ポリシーをすべて削除するには、「[レプリケーション ポリシーを削除する](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)」の手順に従います。

3. vCenter への関連付けを削除するには、[vCenter Server の削除に関するページ](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)の手順に従います。

4. 構成サーバーを削除するには、「[Decommission a configuration server](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)」 (構成サーバーを使用停止にする) の手順に従います。

5. コンテナーを削除します。


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Hyper-V VM (Virtual Machine Manager あり) から Azure へ
1. 「[System Center VMM から Azure へのシナリオを使用して Azure にレプリケートしている Hyper-V 仮想マシンの保護の無効化](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)」の手順に従って、保護されているすべての VM を削除します。

2. コンテナー -> **[Site Recovery Infrastracture]\(Site Recovery インフラストラクチャ\)** - > **[For System Center VMM]\(System Center VMM\)** -> **[レプリケーション ポリシー]** にアクセスして、すべてのレプリケーション ポリシーを関連付け解除し、削除します。

3.  Virtual Machine Manager サーバーへの関連付けを削除するには、「[接続されている VMM サーバーの登録解除](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server)」の手順に従います。

4.  コンテナーを削除します。

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Hyper-V VM (Virtual Machine Manager なし) から Azure へ
1. [Hyper-V 仮想マシンの保護の無効化 (Hyper-V から Azure へ)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) に関するページの手順に従って、保護されている VM をすべて削除します。

2. コンテナー -> **[Site Recovery Infrastructure]\(Site Recovery インフラストラクチャ\)** - > **[For Hyper-V Sites]\(Hyper-V サイト\)** -> **[レプリケーション ポリシー]** にアクセスして、すべてのレプリケーション ポリシーを関連付け解除し、削除します。

3. HYPER-V サーバーへの関連付けを削除するには、「[HYPER-V ホストの登録を解除](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site)」の手順に従います。

4. Hyper-V サイトを削除します。

5. コンテナーを削除します。


## <a name="use-powershell-to-force-delete-the-vault"></a>コンテナーを強制削除するために PowerShell を使用する 

> [!Important]
> 製品をテストしていて、データ損失を心配する必要はない場合は、コンテナーとそのすべての依存関係を強制削除の方法で削除することができます。
> この PowerShell コマンドでは、コンテナーのすべてのコンテンツが削除され、**元に戻すことはできません**。

保護された項目がある場合でも、Site Recovery コンテナーを削除するには、次のコマンドを使用します。

    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault
