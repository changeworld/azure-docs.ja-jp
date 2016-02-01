<properties
	pageTitle="Azure Site Recovery を使用した Windows 仮想マシンの Amazon Web Services から Microsoft Azure への移行 | Microsoft Azure"
	description="Azure Site Recovery を使用して、Amazon Web Services (AWA) で実行中の Windows 仮想マシンを Azure に移行します。"
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
	ms.date="12/14/2015"
	ms.author="raynew"/>

#  Azure Site Recovery を使用した Amazon Web Services (AWS) の Windows 仮想マシンの Azure への移行


## 概要

Azure Site Recovery は、さまざまなデプロイでの仮想マシンのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 計画に貢献します。デプロイ シナリオのすべての一覧については、「[Azure Site Recovery の概要](site-recovery-overview.md)」を参照してください。

この記事では、Site Recovery を使用して、AWS で実行中の Windows インスタンスを Azure に移行またはフェールオーバーする方法について説明します。この記事のほとんどは、「[オンプレミスの VMware 仮想マシンまたは物理サーバーと Azure 間の保護の設定](site-recovery-vmware-to-azure-classic-legacy.md)」で説明した手順を使用しています。デプロイの各手順の詳細については、そちらの記事をお読みいただくことをお勧めします。

## 作業開始

ここでは、開始する前に必要な項目を示します。

- **構成サーバー**: 構成サーバーとして機能する Azure 仮想マシン。構成サーバーは、オンプレミスのマシンと Azure サーバー間のやり取りを調整します。
- **マスター ターゲット サーバー**: マスター ターゲット サーバーとして機能する Azure 仮想マシン。このサーバーは、保護されたコンピューターからレプリケートされたデータを受信して保持します。
- **プロセス サーバー**: Windows Server 2012 R2 を実行する仮想マシン。保護された仮想マシンは、このサーバーにレプリケーション データを送信します。
- **EC2 VM インスタンス**: 移行してから保護するインスタンス。

- 詳細については、「[必要なもの](site-recovery-vmware-to-azure-classic-legacy.md#what-do-i-need)」にあるコンポーネントをご覧ください。
- また、「[容量計画](site-recovery-vmware-to-azure-classic-legacy.md#capacity-planning)」のガイドラインを読み、開始する前に[デプロイの前提条件](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start)を満たしていることを確認します。

## デプロイの手順

1. [コンテナーの作成](site-recovery-vmware-to-azure-classic-legacy.md#step-1-create-a-vault)
2. Azure VM としての[構成サーバーのデプロイ](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server)。
3. Azure VM としての[マスター ターゲット サーバーのデプロイ](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server)。
4. [プロセス サーバーのデプロイ](site-recovery-vmware-to-azure-classic-legacy.md#step-4-deploy-the-on-premises-process-server)。以下の点に注意してください。

	- 同じサブネット / Amazon Virtual Private Cloud 内のプロセス サーバーを EC2 インスタンスとしてデプロイする必要があります。![EC2 インスタンス](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration1.png)

	- プロセス サーバーをデプロイしたら、移行する EC2 インスタンスと通信できることを検証します。
	- 保護する各 VM には、モビリティ サービスがインストールされている必要があります。このサービスは、プロセス サーバーにデータを送信します。モビリティ サービスは、手動でインストールまたはプッシュすることができ、VM の保護が有効になっている場合はプロセス サーバーによって自動的にインストールされます。移行する EC2 インスタンスのファイアウォール規則で、このサービスのプッシュ インストールを許可するように構成する必要があります。EC2 インスタンスのセキュリティ グループには、次の規則を適用する必要があります。

		![firewall rules](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration2.png)

	- プロセス サーバーをデプロイし、Site Recovery 資格情報コンテナーで構成サーバーに登録すると、Site Recovery コンソールの **[構成サーバー]** タブに表示されます。これには最大 15 分かかることがあります。
	
		![プロセス サーバー](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration3.png)

5. [最新の更新プログラムのインストール](site-recovery-vmware-to-azure-classic-legacy.md#step-5-install-latest-updates)。インストールしたすべてのコンポーネント サーバーが最新の状態であることを確認します。
6. [保護グループの作成](site-recovery-vmware-to-azure-classic-legacy.md#step-7-create-a-protection-group)。Site Recovery を使用して移行されたインスタンスの保護を開始するには、保護グループを設定する必要があります。グループにレプリケーション設定を指定すると、そのグループに追加するすべてのインスタンスに適用されます。 
7. [仮想マシンの設定](site-recovery-vmware-to-azure-classic-legacy.md#step-8-set-up-machines-you-want-to-protect)。モバイル サービスを各インスタンスにインストールする必要があります (自動または手動で)。
8. [仮想マシンの保護の有効化](site-recovery-vmware-to-azure-classic-legacy.md#step-9-enable-protection)。インスタンスを保護グループに追加し、インスタンスの保護を有効にします。以下の点に注意してください。

	- EC2 コンソールから取得できるインスタンスのプライベート IP アドレスを使用して、Azure に移行する EC2 インスタンスを検出できます。
	-  作成した保護グループのタブで、[マシンの追加] > [物理マシン] の順にクリックします。![EC2 の探索](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration4.png)
	- インスタンスのプライベート IP アドレスを指定します。
		- ![EC2 の探索](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration5.png)
	- 保護が有効になり、初期レプリケーションが保護グループの初期レプリケーション設定に従って実行されます。
9. [計画されていないフェールオーバーの実行](site-recovery-failover-classic-legacy.md#run-an-unplanned-failover)。初期レプリケーションが完了したら、計画されていないフェールオーバーを AWS から Azure に実行できます。必要に応じて、復旧計画を作成し、計画されていないフェールオーバーを実行して、複数の仮想マシンを AWS から Azure に移行できます。復旧計画については、[こちら](site-recovery-create-recovery-plans.md)をご覧ください。
		
## 次のステップ

ご意見やご質問がありましたら、[Site Recovery フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

<!---HONumber=AcomDC_0121_2016-->