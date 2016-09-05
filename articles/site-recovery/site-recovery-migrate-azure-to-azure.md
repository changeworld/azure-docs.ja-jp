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
	ms.date="08/21/2016"
	ms.author="raynew"/>

#  Azure Site Recovery を使用した Azure リージョン間での Azure IaaS 仮想マシンの移行

## Overview

Azure Site Recovery へようこそ。 この記事は、Azure VM を Azure リージョン間で移行する場合に役立ちます。開始する前に、次のことに注意してください。

- Azure には、リソースの作成と操作に関して、Azure Resource Manager とクラシックの 2 種類のデプロイメント モデルがあります。また、Azure にも 2 つのポータルがあります。クラシック デプロイメント モデルをサポートする Azure クラシック ポータルと、両方のデプロイメント モデルをサポートする Azure ポータルです。Site Recovery を Resource Manager とクラシックのどちらで構成するかに関係なく、移行の基本的な手順は同じです。ただし、この記事の UI の説明とスクリーンショットは、Azure ポータルに適用されます。
- **現時点では、リージョン間の一方向の移行だけが可能です。VM を Azure リージョン間でフェールオーバーできますが、フェールバックすることはできません。**
- この記事で説明する移行の手順は、物理マシンを Azure にレプリケートする手順に基づいています。各手順には、Azure ポータルで物理サーバーをレプリケートする方法を説明する、[VMware VM または物理サーバーの Azure へのレプリケート](site-recovery-vmware-to-azure.md)に関する記事の手順へのリンクが含まれています。
- Site Recovery をクラシック ポータルでセットアップする場合は、[こちらの記事](site-recovery-vmware-to-azure-classic.md)に記載された詳細な手順に従ってください。この[古い記事](site-recovery-vmware-to-azure-classic-legacy.md)の**手順を参照する必要はありません**。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。


## 前提条件

このデプロイで必要なものを次に示します。

- **構成サーバー**: 構成サーバーとして機能する、Windows Server 2012 R2 を実行するオンプレミス VM。この VM にも他の Site Recovery コンポーネント (プロセス サーバーとマスター ターゲット サーバーを含む) をインストールします。詳細については、「[シナリオのアーキテクチャ](site-recovery-vmware-to-azure.md#scenario-architecture)」および「[構成サーバーの前提条件](site-recovery-vmware-to-azure.md#configuration-server-prerequisites)」をご覧ください。
- **IaaS 仮想マシン**: 移行する VM。これらの VM を物理コンピューターとして扱って移行します。

## デプロイメントの手順

このセクションでは、新しい Azure ポータルでのデプロイの手順について説明します。クラシック ポータルの Site Recovery について、これらのデプロイの手順が必要な場合は、[こちらの記事](site-recovery-vmware-to-azure-classic.md)をご覧ください。

1. [コンテナーを作成します](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault)。
2. [構成サーバーをデプロイします](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment)。
3. 構成サーバーをデプロイしたら、移行する VM と通信できることを確認します。
4. [レプリケーション設定をセットアップします](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings)。レプリケーション ポリシーを作成し、構成サーバーに割り当てます。
5. [モビリティ サービスをインストールします](site-recovery-vmware-to-azure.md#step-6-replication-application)。保護する各 VM には、モビリティ サービスがインストールされている必要があります。このサービスは、プロセス サーバーにデータを送信します。モビリティ サービスは、手動でインストールまたはプッシュすることができ、VM の保護が有効になっている場合はプロセス サーバーによって自動的にインストールされます。移行する VM のファイアウォール規則を、このサービスのプッシュ インストールを許可するように構成する必要があります。
6. [レプリケーションを有効にします](site-recovery-vmware-to-azure.md#enable-replication)。移行する VM のレプリケーションを有効にします。仮想マシンのプライベート IP アドレスを使用して、Azure に移行する IaaS 仮想マシンを検出できます。このアドレスは、Azure の仮想マシン ダッシュボードにあります。レプリケーションを有効にするときに、VM のマシンの種類を物理コンピューターとして設定します。
7. [計画されていないフェールオーバーを実行します](site-recovery-failover.md#run-an-unplanned-failover)。初期レプリケーションが完了したら、Azure リージョン間で計画されていないフェールオーバーを実行できます。必要に応じて、復旧計画を作成し、計画されていないフェールオーバーを実行して、複数の仮想マシンをリージョン間で移行できます。復旧計画については、[こちら](site-recovery-create-recovery-plans.md)をご覧ください。

## 次のステップ

その他のレプリケーション シナリオの詳細については、[Azure Site Recovery の概要](site-recovery-overview.md)に関する記事をご覧ください。

<!---HONumber=AcomDC_0824_2016-->