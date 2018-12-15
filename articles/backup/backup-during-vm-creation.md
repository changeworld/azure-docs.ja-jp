---
title: 作成時に Azure VM のバックアップを有効にする
description: 作成プロセスの間に Azure 仮想マシンのバックアップを有効にする手順を説明します。
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 518d171c96b9c4f9bf3e195a7130f4c022b7ad07
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879878"
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Azure 仮想マシンの作成時にバックアップを有効にする 

Azure Backup サービスでは、クラウドへのバックアップを作成して構成するインターフェイスが提供されています。 定期的にバックアップ (復旧ポイントと呼ばれます) を取得することで、データを保護します。 Azure Backup によって、geo 冗長 Recovery コンテナーに保存できる復元ポイントが作成されます。 この記事では、Azure Portal で仮想マシン (VM) を作成するときにバックアップを有効にする方法について説明します。  

## <a name="log-in-to-azure"></a>Azure にログインする 

まだアカウントにサインインしていない場合は、[Azure Portal](http://portal.azure.com) にサインインします。
 
## <a name="create-virtual-machine-with-backup-configured"></a>バックアップを構成して仮想マシンを作成する 

1. Azure Portal の左上隅にある **[新規]** をクリックします。 

2. **[コンピューティング]** を選び、仮想マシンのイメージを選びます。   

3. 仮想マシンの情報を入力します。 ここで指定したユーザー名とパスワードが、仮想マシンへのサインインに使われます。 完了したら、**[OK]** をクリックします。 

4. VM のサイズを選択します。  

5. **[設定] > [バックアップ]** で、**[有効]** をクリックして、バックアップの構成設定を表示します。 設定ページでは既定値のままにして **[OK]** をクリックし、VM を作成する [概要] ページに進むことができます。 値を変更する場合は、次の手順に従います。  

6. 仮想マシンのバックアップを保持する Recovery Services コンテナーを作成するか選びます。 Recovery Services コンテナーを作成する場合は、コンテナーのリソース グループを選ぶことができます。  

    ![VM 作成でのバックアップ構成ページ](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Recovery Services コンテナーのリソース グループは、仮想マシンのリソース グループと異なっていてもかまいません。  
    > 
    > 

7. 既定では、VM をすぐに保護できるよう、バックアップ ポリシーが自動的に選択されます。 バックアップ ポリシーでは、バックアップ スナップショットを取得する頻度と、バックアップ コピーを保持する期間が指定されます。 既定のポリシーを受け入れることも、別のバックアップ ポリシーを作成または選択することもできます。 バックアップ ポリシーを編集するには、**[バックアップ ポリシー]** を選んで、ポリシーの値を変更します。  

8. バックアップの構成値に問題がなければ、[設定] ページで **[OK]** をクリックします。  

9. [概要] ページで検証に合格したら、**[作成]** をクリックして、構成済みのバックアップ設定を使う仮想マシンを作成します。 

## <a name="initiate-a-backup-after-creating-the-vm"></a>VM の作成後にバックアップを開始する 

バックアップ ポリシーが作成されていたとしても、初期バックアップを作成するのがよい方法です。 VM 作成テンプレートが完了した後で仮想マシンのバックアップの詳細を確認するには、左側のメニューの **[操作]** の設定から、**[バックアップ]** をクリックします。 これを使って、オンデマンド バックアップの開始、完全な VM またはすべてのディスクの復元、VM のバックアップからのファイルの復元、または仮想マシンに関連付けられているバックアップ ポリシーの変更を、行うことができます。  

## <a name="using-a-resource-manager-template-to-deploy-a-protected-vm"></a>Resource Manager テンプレートを使用して保護された VM を展開する

前の手順では、Azure Portal を使って仮想マシンを作成し、Recovery Services コンテナーにそれを保護する方法について説明しました。 仮想マシンをすばやく展開し、Recovery Services コンテナーにそれを保護したい場合は、[Windows VM を展開してバックアップを有効にする](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)ためのテンプレートをご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>VM の作成時には、どの VM イメージでバックアップが有効になりますか。 

Microsoft によって公開されている以下のコア イメージの一覧が、VM 作成時のバックアップの有効化に対してサポートされています。 他の VM の場合は、VM を作成した後でバックアップを有効にできます。 詳しくは、[VM 作成後のバックアップの有効化](quick-backup-vm-portal.md)に関するページをご覧ください 

- **Windows** - Windows Server 2016 Datacenter、Windows Server 2016 Data Center core、Windows Server 2012 DataCenter、Windows Server 2012 R2 DataCenter、Windows Server 2008 R2 SP1 
- **Ubuntu** - Ubuntu Server 1710、Ubuntu Server 1704、UUbuntu Server 1604(LTS)、Ubuntu Server 1404(LTS) 
- **Red Hat** - RHEL 6.7、6.8、6.9、7.2、7.3、7.4 
- **SUSE** - SUSE Linux Enterprise Server 11 SP4、12 SP2、12 SP3 
- **Debian** - Debian 8、Debian 9 
- **CentOS** - CentOS 6.9、CentOS 7.3 
- **Oracle Linux** - Oracle Linux 6.7、6.8、6.9、7.2、7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>バックアップのコストは VM のコストに含まれますか。 

いいえ、バックアップのコストは仮想マシンのコストとは別にかかります。 バックアップの料金について詳しくは、[Backup の価格のサイト](https://azure.microsoft.com/pricing/details/backup/)をご覧ください。
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>VM でバックアップを有効にするにはどのアクセス許可が必要ですか。 

仮想マシンの共同作成者の場合は、VM でバックアップを有効にできます。 カスタム ロールを使っている場合は、VM でバックアップを正常に有効にするには、次のアクセス許可が必要です。 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Recovery Services コンテナーと仮想マシンが異なるリソース グループに属している場合は、Recovery Services コンテナーのリソース グループへの書き込みアクセス許可があることを確認してください。  

## <a name="next-steps"></a>次の手順 

VM を保護したので、VM の管理タスクおよび VM の復元方法の詳細については、次の記事を参照してください。 

- [仮想マシンの管理と監視](backup-azure-manage-vms.md) 
- [仮想マシンの復元](backup-azure-arm-restore-vms.md) 
