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
	ms.date="08/22/2016"
	ms.author="raynew"/>

#  Azure Site Recovery を使用した Amazon Web Services (AWS) の Windows 仮想マシンの Azure への移行

## Overview

Azure Site Recovery へようこそ。この記事では、Site Recovery を使用して、AWS で実行中のWindows インスタンスを Azure に移行します。開始する前に、次のことに注意してください。

- Azure には、リソースの作成と操作に関して、Azure Resource Manager とクラシックの 2 種類のデプロイメント モデルがあります。また、Azure にも 2 つのポータルがあります。クラシック デプロイメント モデルをサポートする Azure クラシック ポータルと、両方のデプロイメント モデルをサポートする Azure ポータルです。移行の基本的な手順は、Resource Manager またはクラシックで Site Recovery を構成しているかどうかと同じです。ただし、この記事での UI の説明とスクリーンショットは、Azure ポータルに関連します。
- **現時点で、AWS から Azure にのみ移行できます。AWS から Azure に VM をフェールオーバーすることはできますが、もう一度フェールバックすることはできません。実行中のレプリケーションはありません。**
- この記事で説明する移行の手順は、物理マシンを Azure にレプリケートするための手順に基づいています。ここには、[VMware VM または物理サーバーを Azure にレプリケートする](site-recovery-vmware-to-azure.md)に関する記事の手順へのリンクが掲載されています。ここでは、Azure ポータルで物理サーバーをレプリケートする方法について説明します。
- クラシック ポータルで Site Recovery を設定している場合は、[この記事](site-recovery-vmware-to-azure-classic.md)の詳細な手順に従ってください。この**古い記事**の手順を[参照する必要はありません](site-recovery-vmware-to-azure-classic-legacy.md)。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。


## 前提条件

このデプロイで必要なものを次に示します。

- **構成サーバー**: 構成サーバーとして動作する、Windows Server 2012 R2 を実行するオンプレミス VM。この VM にも他の Site Recovery コンポーネント (プロセス サーバーとマスター ターゲット サーバーを含む) をインストールします。詳細については、「[シナリオのアーキテクチャ](site-recovery-vmware-to-azure.md#scenario-architecture)」と「[構成サーバーの前提条件](site-recovery-vmware-to-azure.md#configuration-server-prerequisites)」をご覧ください。
- **EC2 VM インスタンス**: 移行する Windows を実行中のインスタンス。

## デプロイメントの手順

このセクションでは、新しい Azure ポータルでのデプロイの手順について説明します。クラシック ポータルの Site Recovery について、これらのデプロイの手順が必要な場合は、 [この記事](site-recovery-vmware-to-azure-classic.md)をご覧ください。

1. [コンテナーを作成します](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault)。
2. [構成サーバーをデプロイします](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment)。
3. 構成サーバーをデプロイしたら、移行する VM と通信できることを検証します。
4. [レプリケーション設定をセットアップします](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings)。レプリケーション ポリシーを作成し、構成サーバーに割り当てます。
5. [モビリティ サービスをインストールします](site-recovery-vmware-to-azure.md#step-6-replication-application)。保護する各 VM には、モビリティ サービスがインストールされている必要があります。このサービスは、プロセス サーバーにデータを送信します。モビリティ サービスは、手動でインストールまたはプッシュすることができ、VM の保護が有効になっている場合はプロセス サーバーによって自動的にインストールされます。移行する EC2 インスタンスのファイアウォール規則で、このサービスのプッシュ インストールを許可するように構成する必要があります。EC2 インスタンスのセキュリティ グループには、次の規則を適用する必要があります。

	![firewall rules](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [レプリケーションを有効にします](site-recovery-vmware-to-azure.md#enable-replication)。移行する VM のレプリケーションを有効にします。EC2 コンソールから取得できるプライベート IP アドレスを使用して、EC2 インスタンスを検出できます。
7. [計画されていないフェールオーバーを実行します](site-recovery-failover.md#run-an-unplanned-failover)。初期レプリケーションが完了したら、各 VM に対して、AWS から Azure への計画されていないフェールオーバーを実行できます。必要に応じて、復旧計画を作成し、計画されていないフェールオーバーを実行して、複数の仮想マシンを AWS から Azure に移行できます。復旧計画については、[こちら](site-recovery-create-recovery-plans.md)をご覧ください。

## 次のステップ

その他のレプリケーション シナリオの詳細については、「[Azure Site Recovery とは](site-recovery-overview.md)」を参照してください。

<!---HONumber=AcomDC_0824_2016-->