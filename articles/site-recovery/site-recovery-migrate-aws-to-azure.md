<properties
	pageTitle="Azure Site Recovery を使用した Windows 仮想マシンの Amazon Web Services から Microsoft Azure への移行 | Microsoft Azure"
	description="この記事では、Azure Site Recovery を使用して、Amazon Web Services (AWA) で実行中の Windows 仮想マシンを Azure に移行する方法を説明します。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Azure Site Recovery を使用した Amazon Web Services (AWS) の Windows 仮想マシンの Azure への移行

## 概要

この記事では、Site Recovery を使用して、AWS で実行中の Windows インスタンスを Azure に移行する方法について説明します。開始する前に、次のことに注意してください。

- 現時点でのみ移行できます。つまり、AWS から Azure にフェールオーバーすることはできますが、もう一度フェールバックすることはできません。
- この記事では、レプリケーションの設定に関する最新の拡張手順を提供する 「[VMware 仮想マシンまたは物理サーバーを Azure にレプリケートする](site-recovery-vmware-to-azure-classic.md)」で詳しく説明される数多くの手順を使用し、概要について説明します。移行の際の詳細な手順については、この記事を参照することをお勧めします。
- この[古い記事](site-recovery-vmware-to-azure-classic-legacy.md)の手順を**参照する必要はありません**。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) で投稿してください。


## 前提条件

ここでは、開始する前に必要な項目を示します。

- **管理サーバー**: 管理サーバーとして動作する、Windows Server 2012 R2 を実行するオンプレミス VM。このサーバーに Site Recovery コンポーネント (構成サーバーとプロセス サーバーを含む) をインストールします。詳細については、[管理サーバーの考慮事項](site-recovery-vmware-to-azure-classic.md#management-server-considerations)と[オンプレミスの前提条件](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites)を参照してください。
- **EC2 VM インスタンス**: 移行してから保護するインスタンス。

## デプロイの手順

1. [コンテナーの作成](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault)
2. [管理サーバーをデプロイします](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server)。 
3. 管理サーバーをデプロイしたら、移行する EC2 インスタンスと通信できることを検証します。
4. [保護グループの作成](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group)。保護グループには、同じレプリケーション設定を共有する保護対象のコンピューターが含まれています。グループにレプリケーション設定を指定すると、そのグループに追加するすべてのコンピューターに適用されます。 
5. [モビリティ サービスをインストールします](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service)。保護する各 VM には、モビリティ サービスがインストールされている必要があります。このサービスは、プロセス サーバーにデータを送信します。モビリティ サービスは、手動でインストールまたはプッシュすることができ、VM の保護が有効になっている場合はプロセス サーバーによって自動的にインストールされます。移行する EC2 インスタンスのファイアウォール規則で、このサービスのプッシュ インストールを許可するように構成する必要があります。EC2 インスタンスのセキュリティ グループには、次の規則を適用する必要があります。

	![firewall rules](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [コンピューターの保護を有効にします](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine)。保護対象のコンピューターをレプリケーション グループに追加します。

	![保護を有効にする](./media/site-recovery-migrate-aws-to-azure/migrate-add-machines.png)

7. EC2 コンソールから取得できるインスタンスのプライベート IP アドレスを使用して、Azure に移行する EC2 インスタンスを検出できます。保護グループでは、各インスタンスのプライベート IP アドレスを追加できます。

	![保護を有効にする](./media/site-recovery-migrate-aws-to-azure/migrate-machine-ip.png)

	コンピューターをグループに追加すると、保護が有効になり、保護グループ設定に従って最初のレプリケーションが実行されます。

8. [計画されていないフェールオーバーの実行](site-recovery-failover.md#run-an-unplanned-failover)。初期レプリケーションが完了したら、各 VM に対して、AWS から Azure への計画されていないフェールオーバーを実行できます。必要に応じて、復旧計画を作成し、計画されていないフェールオーバーを実行して、複数の仮想マシンを AWS から Azure に移行できます。復旧計画については、[こちら](site-recovery-create-recovery-plans.md)をご覧ください。
		
## 次のステップ

その他のレプリケーション シナリオの詳細については、「[Azure Site Recovery とは](site-recovery-overview.md)」を参照してください。

<!---HONumber=AcomDC_0323_2016-->