---
title: "Site Recovery を使用して Amazon Web Services から Azure に仮想マシンを移行する | Microsoft Azure"
description: "この記事では、Azure Site Recovery を使用して、Amazon Web Services (AWS) で実行中の仮想マシンを Azure に移行する方法を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/01/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: c7238d677b315390bc5f53e54ab7dbded2871c5a


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Azure Site Recovery を使用して Amazon Web Services (AWS) から Azure に仮想マシンを移行する
## <a name="overview"></a>Overview
Azure Site Recovery へようこそ。 この記事では、Site Recovery を使用して、AWS で実行中の EC2 インスタンスを Azure に移行します。 開始する前に、次のことに注意してください。

* Azure には、リソースの作成と操作に関して、Azure Resource Manager とクラシックの 2 種類のデプロイメント モデルがあります。 また、Azure にも 2 つのポータルがあります。クラシック デプロイメント モデルをサポートする Azure クラシック ポータルと、両方のデプロイメント モデルをサポートする Azure ポータルです。 移行の基本的な手順は、Resource Manager またはクラシックで Site Recovery を構成しているかどうかと同じです。ただし、この記事での UI の説明とスクリーンショットは、Azure ポータルに関連します。
* **現時点で、AWS から Azure にのみ移行できます。AWS から Azure に VM をフェールオーバーすることはできますが、もう一度フェールバックすることはできません。実行中のレプリケーションはありません。**
* この記事で説明する移行の手順は、物理マシンを Azure にレプリケートする手順に基づいています。 各手順には、Azure ポータルで物理サーバーをレプリケートする方法を説明する、 [VMware VM または物理サーバーの Azure へのレプリケート](site-recovery-vmware-to-azure.md)に関する記事の手順へのリンクが含まれています。
* Site Recovery をクラシック ポータルでセットアップする場合は、 [こちらの記事](site-recovery-vmware-to-azure-classic.md)に記載された詳細な手順に従ってください。 **古い記事** の [手順を参照する必要はありません](site-recovery-vmware-to-azure-classic-legacy.md)に記載された詳細な手順に従ってください。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム
Site Recovery を使用すると、次のどのオペレーティング システムで実行している EC2 インスタンスでも移行できます。

### <a name="windows64-bit-only"></a>Windows (64 ビットのみ)
* Windows Server 2008 R2 SP1+ (Citrix PV ドライバーまたは AWS PV ドライバーのみ、**RedHat PV ドライバーを実行しているインスタンスはサポートされません**)
* Windows Server 2012
* Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (64 ビットのみ)
* Red Hat Enterprise Linux 6.7 (HVM 仮想化インスタンスのみ)

## <a name="prerequisites"></a>前提条件
このデプロイで必要なものを次に示します。

* **構成サーバー**: 構成サーバーとして動作する、Windows Server 2012 R2 を実行するオンプレミス VM。 この VM にも他の Site Recovery コンポーネント (プロセス サーバーとマスター ターゲット サーバーを含む) をインストールします。 詳細については、「[シナリオのアーキテクチャ](site-recovery-vmware-to-azure.md#scenario-architecture)」および「[構成サーバーの前提条件](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites)」をご覧ください。
* **EC2 VM インスタンス**: 移行する EC2 インスタンス。

## <a name="deployment-steps"></a>デプロイメントの手順
このセクションでは、新しい Azure ポータルでのデプロイの手順について説明します。 クラシック ポータルの Site Recovery について、これらのデプロイの手順が必要な場合は、 [こちらの記事](site-recovery-vmware-to-azure-classic.md)をご覧ください。

1. [コンテナーを作成します](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault)。
2. [構成サーバーをデプロイします](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment)。
3. 構成サーバーをデプロイしたら、移行する VM と通信できることを検証します。
4. [レプリケーション設定をセットアップします](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings)。 レプリケーション ポリシーを作成し、構成サーバーに割り当てます。
5. [モビリティ サービスをインストールします](site-recovery-vmware-to-azure.md#step-6-replicate-applications)。 保護する各 VM には、モビリティ サービスがインストールされている必要があります。 このサービスは、プロセス サーバーにデータを送信します。 モビリティ サービスは、手動でインストールまたはプッシュすることができ、VM の保護が有効になっている場合はプロセス サーバーによって自動的にインストールされます。 移行する EC2 インスタンスのファイアウォール規則で、このサービスのプッシュ インストールを許可するように構成する必要があります。 EC2 インスタンスのセキュリティ グループには、次の規則を適用する必要があります。

    ![firewall rules](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. [レプリケーションを有効にします](site-recovery-vmware-to-azure.md#enable-replication)。 移行する VM のレプリケーションを有効にします。 EC2 コンソールから取得できるプライベート IP アドレスを使用して、EC2 インスタンスを検出できます。
7. [ 計画されていないフェールオーバーを実行します](site-recovery-failover.md#run-an-unplanned-failover)。 初期レプリケーションが完了したら、各 VM に対して、AWS から Azure への計画されていないフェールオーバーを実行できます。 必要に応じて、復旧計画を作成し、計画されていないフェールオーバーを実行して、複数の仮想マシンを AWS から Azure に移行できます。 [こちら](site-recovery-create-recovery-plans.md) をご覧ください。

## <a name="next-steps"></a>次のステップ
その他のレプリケーション シナリオの詳細については、 [Azure Site Recovery の概要](site-recovery-overview.md)



<!--HONumber=Nov16_HO3-->


