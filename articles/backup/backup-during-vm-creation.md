---
title: 作成時に Azure VM のバックアップを有効にする
description: 作成プロセスの間に Azure 仮想マシンのバックアップを有効にする方法を説明します。
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780445"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>Azure 仮想マシンの作成時にバックアップを有効にする

Azure Virtual Machines (VM) をバックアップするには、Azure Backup サービスを使用します。 バックアップ ポリシーで指定されているスケジュールに従って VM がバックアップされ、バックアップから復旧ポイントが作成されます。 復旧ポイントは、Recovery Services コンテナーに格納されます。

この記事では、Azure portal で仮想マシン (VM) を作成するときにバックアップを有効にする方法について説明します。  

## <a name="sign-in-to-azure"></a>Azure へのサインイン

まだアカウントにサインインしていない場合は、[Azure portal](https://portal.azure.com) にサインインします。
 
## <a name="create-a-vm-with-backup-configured"></a>バックアップが構成された VM を作成する 

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。

1. **[Compute]** を選択し、VM のイメージを選択します。

1. VM の情報を入力します。 指定したユーザー名とパスワードは、VM にサインインするために使用されます。 完了したら、**[OK]** をクリックします。 

1. VM のサイズを選択します。  

1. **[設定]** > **[バックアップ]** で、**[有効]** を選択してバックアップの構成設定を開きます。

   - 既定値のままにして、**[設定]** ページで **[OK]** を選択します。 次に **[概要]** ページに移動し、VM を作成します。 ステップ 6 から 8 をスキップします。
   - バックアップの構成値を変更するには、次の手順のようにします。  

1. VM のバックアップを保持する Recovery Services コンテナーを作成するか選択します。 Recovery Services コンテナーを作成する場合は、コンテナーのリソース グループを選択できます。  

    ![VM 作成ページでのバックアップ構成設定](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Recovery Services コンテナーのリソース グループは、VM のリソース グループと異なっていてもかまいません。  

1. 既定では、VM を保護するためのバックアップ ポリシーが自動的に選択されます。 バックアップ ポリシーでは、バックアップ スナップショットを取得する頻度と、バックアップ コピーを保持する期間が指定されます。 

   - 既定のポリシーを受け入れることも、別のバックアップ ポリシーを作成または選択することもできます。 
   - バックアップ ポリシーを編集するには、**[バックアップ ポリシー]** を選択して、値を変更します。  

1. バックアップ構成の値の設定が終わったら、**[設定]** ページの **[OK]** を選択します。  

1. **[概要]** ページで検証に合格したら、**[作成]** を選択し、構成したバックアップ設定を使用する VM を作成します。 

## <a name="start-a-backup-after-creating-the-vm"></a>VM の作成後にバックアップを開始する 

VM のバックアップ ポリシーを構成した場合でも、初期バックアップを作成することをお勧めします。 

VM 作成テンプレートが完了した後、左側のメニューで **[操作]** に移動し、**[バックアップ]** を選択して仮想マシンのバックアップの詳細を表示します。 このページを使用して次のことができます。

- オンデマンド バックアップをトリガーする。
- 完全な VM またはそのすべてのディスクを復元する。
- VM のバックアップからファイルを復元する。
- VM に関連付けられているバックアップ ポリシーを変更する。  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Resource Manager テンプレートを使用して保護された VM をデプロイする

前の手順では、Azure portal を使用して仮想マシンを作成し、Recovery Services コンテナーにそれを保護する方法について説明しました。 仮想マシンをすばやくデプロイし、Recovery Services コンテナーでそれを保護するには、[Windows VM をデプロイしてバックアップを有効にする](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)ためのテンプレートをご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>VM 作成中のバックアップの構成がサポートされているのはどの VM イメージですか

Microsoft によって公開されている以下のコア イメージが、VM 作成時のバックアップの有効化に対してサポートされています。 他の VM の場合は、VM を作成した後でバックアップを有効にできます。 詳しくは、[VM 作成後のバックアップの有効化](quick-backup-vm-portal.md)に関するページをご覧ください。

- **Windows** - Windows Server 2016 Datacenter、Windows Server 2016 Datacenter Core、Windows Server 2012 Datacenter、Windows Server 2012 R2 Datacenter、Windows Server 2008 R2 SP1 
- **Ubuntu** - Ubuntu Server 17.10、Ubuntu Server 17.04、Ubuntu Server 16.04 (LTS)、Ubuntu Server 14.04 (LTS) 
- **Red Hat** - RHEL 6.7、6.8、6.9、7.2、7.3、7.4 
- **SUSE** - SUSE Linux Enterprise Server 11 SP4、12 SP2、12 SP3 
- **Debian** - Debian 8、Debian 9 
- **CentOS** - CentOS 6.9、CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7、6.8、6.9、7.2、7.3 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>バックアップのコストは VM のコストに含まれますか 

いいえ。 バックアップのコストは VM のコストとは別です。 バックアップの価格設定について詳しくは、「[Azure Backup の価格](https://azure.microsoft.com/pricing/details/backup/)」をご覧ください。
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>VM でバックアップを有効にするにはどのアクセス許可が必要ですか。 

VM の共同作成者の場合は、VM でバックアップを有効にできます。 カスタム ロールを使用している場合、VM でバックアップを有効にするには、次のアクセス許可が必要です。 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Recovery Services コンテナーと VM が異なるリソース グループに属している場合は、Recovery Services コンテナーのリソース グループへの書き込みアクセス許可があることを確認してください。  

## <a name="next-steps"></a>次の手順 

VM を保護したので、次の記事を参照して、VM を管理および復元する方法を学習してください。

- [仮想マシンの管理と監視](backup-azure-manage-vms.md) 
- [仮想マシンの復元](backup-azure-arm-restore-vms.md) 
