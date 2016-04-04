<properties
	pageTitle="Site Recovery を使用した異なる Azure リージョン間での Azure IaaS 仮想マシンの移行 | Microsoft Azure"
	description="Azure Site Recovery を使用して、異なる Azure リージョン間で Azure IaaS 仮想マシンを移行します。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Azure Site Recovery を使用した Azure リージョン間での Azure IaaS 仮想マシンの移行

## 概要

この記事では、Site Recovery を使用して、Azure リージョン間で Azure VM を移行する方法について説明します。開始する前に、次のことに注意してください。

- 現時点でのみ移行できます。つまり、VM を Azure リージョン から 別の Azure リージョンにフェールオーバーすることはできますが、もう一度フェールバックすることはできません。
- この記事では、レプリケーションの設定に関する最新の拡張手順を提供する 「[VMware 仮想マシンまたは物理サーバーを Azure にレプリケートする](site-recovery-vmware-to-azure-classic.md)」で詳しく説明される数多くの手順を使用し、概要について説明します。移行の際の詳細な手順については、この記事を参照することをお勧めします。
- この[古い記事](site-recovery-vmware-to-azure-classic-legacy.md)の手順を**参照する必要はありません**。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。


## 前提条件

このデプロイで必要なものを次に示します。

- **管理サーバー**: 管理サーバーとして動作する、Windows Server 2012 R2 を実行するオンプレミス VM。このサーバーに Site Recovery コンポーネント (構成サーバーとプロセス サーバーを含む) をインストールします。詳細については、[管理サーバーの考慮事項](site-recovery-vmware-to-azure-classic.md#management-server-considerations)と[オンプレミスの前提条件](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites)を参照してください。
- **IaaS 仮想マシン**: 移行する VM。

## デプロイの手順

1. [コンテナーを作成します](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault)。
2. [管理サーバーをデプロイします](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server)。
3. 管理サーバーをデプロイしたら、移行する VM と通信できることを検証します。
4. [保護グループの作成](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group)。保護グループには、同じレプリケーション設定を共有する保護対象のコンピューターが含まれています。グループにレプリケーション設定を指定すると、そのグループに追加するすべてのコンピューターに適用されます。
5. [モビリティ サービスをインストールします](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service)。保護する各 VM には、モビリティ サービスがインストールされている必要があります。このサービスは、プロセス サーバーにデータを送信します。モビリティ サービスは、手動でインストールまたはプッシュすることができ、VM の保護が有効になっている場合はプロセス サーバーによって自動的にインストールされます。移行する IaaS 仮想マシンのファイアウォール規則で、このサービスのプッシュ インストールを許可するように構成する必要があります。
6. [コンピューターの保護を有効にします](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine)。保護対象のコンピューターをレプリケーション グループに追加します。 
7. 仮想マシンのプライベート IP アドレスを使用して、Azure に移行する IaaS 仮想マシンを検出できます。このアドレスは、Azure の仮想マシン ダッシュボードにあります。
8. 作成した保護グループのタブで、**[マシンの追加]** > **[物理マシン]** の順にクリックします。

	![EC2 の探索](./media/site-recovery-migrate-azure-to-azure/migrate-add-machines.png)

9. 仮想マシンのプライベート IP アドレスを指定します。

	![EC2 の探索](./media/site-recovery-migrate-azure-to-azure/migrate-machine-ip.png)
	
	コンピューターをグループに追加すると、保護が有効になり、保護グループ設定に従って最初のレプリケーションが実行されます。

10. [計画されていないフェールオーバーを実行します](site-recovery-failover.md#run-an-unplanned-failover)。初期レプリケーションが完了したら、異なる Azure リージョン間で計画されていないフェールオーバーを実行できます。必要に応じて、復旧計画を作成し、計画されていないフェールオーバーを実行して、複数の仮想マシンをリージョン間で移行できます。復旧計画については、[こちら](site-recovery-create-recovery-plans.md)をご覧ください。
		
## 次のステップ

その他のレプリケーション シナリオの詳細については、「[Azure Site Recovery とは](site-recovery-overview.md)」を参照してください。

<!---HONumber=AcomDC_0323_2016-->