---
title: "Recovery Services コンテナーの削除"
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 32fcab0c9e4665d07691dc3792bdee90fb01fe66
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---
# <a name="delete-recovery-services-vault"></a>Recovery Services コンテナーの削除
依存関係があると、Recovery Services コンテナーを削除できません。実行する必要のある操作は、Azure Site Recovery シナリオの種類 (VMWare から Azure、Hyper-V (VMM あり/なし) から Azure、Azure Backup) によって異なります。 Azure Backup で使用されているコンテナーを削除するには、[この](../backup/backup-azure-delete-vault.md)のリンク先を確認してください。

>[!Important]
>製品をテストしていて、コンテナーをすばやく削除する必要があるものの、データ損失を心配する必要はない場合は、強制削除方法を使用してコンテナーとそのすべての依存関係を削除できます。

> この PowerShell コマンドでは、コンテナーのすべてのコンテンツが削除され、元に戻す手順がないことに注意してください。

## <a name="force-delete-vault-using-powershell"></a>Powershell を使用したコンテナーの強制削除

以下の手順に従うと、保護された項目がある場合でも、Site Recovery コンテナーが削除されます。

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault



コンテナーを削除するには、シナリオに応じた推奨手順 (指定された順序) に従ってください。

## <a name="delete-vault-used-in-site-recovery-for-protecting-vmware-vms-to-azure"></a>VMWare VM を Azure に保護するために Site Recovery で使用されているコンテナーの削除:
1. すべての保護対象 VM が削除されていることを確認します。方法は、[こちら](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)を参照してください。
2.  すべてのレプリケーション ポリシーが削除されていることを確認します。方法は、[こちら](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)を参照してください。
3.  vCenter への参照が削除されていることを確認します。方法は、[こちら](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery)を参照してください。
4. 構成サーバーが削除されていることを確認します。方法は、[こちら](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server)を参照してください。
5. この時点で、コンテナーを削除してみます。


## <a name="delete-vault-used-in-site-recovery-for-protecting-hyper-v-vms-with-vmm-to-azure"></a>Hyper-V VM (VMM あり) を Azure に保護するために Site Recovery で使用しているコンテナーの削除:
1.  すべての保護対象 VM が削除されていることを確認します。方法は、[こちら](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)を参照してください。
- すべてのレプリケーション ポリシーが削除されていることを確認します。方法は、[こちら](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)を参照してください。
-   VMM サーバーへの参照を削除します。方法は、[こちら](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server)を参照してください。
-   この時点で、コンテナーを削除してみます。

## <a name="delete-vault-used-in-site-recovery--for-protecting-hyper-v-vms-without-vmm-to-azure"></a>Hyper-V VM (VMM なし) を Azure に保護するために Site Recovery で使用されているコンテナーの削除:
1. すべての保護対象 VM が削除されていることを確認します。方法は、[こちら](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)を参照してください。
- すべてのレプリケーション ポリシーが削除されていることを確認します。方法は、[こちら](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)を参照してください。
-   Hyper-V サーバーへの参照を削除します。方法は、[こちら](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site)を参照してください。
-   Hyper-V サイトを削除します。
-   この時点で、コンテナーを削除してみます。

