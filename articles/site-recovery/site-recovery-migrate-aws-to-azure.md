---
title: "AWS から Azure への VM の移行 | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して、Amazon Web Services (AWS) で実行中の仮想マシンを Azure に移行する方法を説明します。"
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/12/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: 5031f64ffcd34b6287a3ecd87dd027c2bc7c716f
ms.openlocfilehash: 9d0d0ba4ca5966b39ce62ea8296d48e5930c9782


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Azure Site Recovery を使用して Amazon Web Services (AWS) から Azure に仮想マシンを移行する

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、AWS Windows インスタンスを Azure 仮想マシンに移行する方法を説明します。

移行は事実上 AWS から Azure へのフェールオーバーです。 マシンをフェールバックすることはできませんし、レプリケーションは一切継続しません。 この記事は Azure Portal での移行手順を説明しており、[物理マシンを Azure にレプリケートする](site-recovery-vmware-to-azure.md)手順に基づいています。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

Site Recovery を使用すると、次のどのオペレーティング システムで実行している EC2 インスタンスでも移行できます。

- Windows (64 ビットのみ)
    - Windows Server 2008 R2 SP1+ (Citrix PV ドライバーまたは AWS PV ドライバーのみ。 **RedHat PV ドライバーを実行しているインスタンスはサポートされません**) Windows Server 2012 Windows Server 2012 R2
- Linux (64 ビットのみ)
    - Red Hat Enterprise Linux 6.7 (HVM 仮想化インスタンスのみ)

## <a name="prerequisites"></a>前提条件

このデプロイで必要なものを次に示します。

* **構成サーバー**: Windows Server 2012 R2 を実行するオンプレミス VM が、構成サーバーとしてデプロイされている。 既定では、構成サーバーを展開するときに、他の Site Recovery コンポーネント (プロセス サーバーとマスター ターゲット サーバー) がインストールされます。 [詳細情報](site-recovery-components.md#replicate-vmware-vmsphysical-servers-to-azure)
* **EC2 インスタンス**: 移行対象の、EC2 仮想マシンのインスタンス。

## <a name="deployment-steps"></a>デプロイメントの手順

1. コンテナーの作成
2. 構成サーバーのデプロイ
3. 構成サーバーをデプロイしたら、移行する VM と通信できることを検証します。
4. レプリケーション設定をセットアップします。
5. モビリティ サービスをインストールします。 保護する各 VM には、モビリティ サービスがインストールされている必要があります。 このサービスは、プロセス サーバーにデータを送信します。 モビリティ サービスは、手動でインストールまたはプッシュすることができ、VM の保護が有効になっている場合はプロセス サーバーによって自動的にインストールされます。 移行する EC2 インスタンスのファイアウォール規則で、このサービスのプッシュ インストールを許可するように構成する必要があります。 EC2 インスタンスのセキュリティ グループには、次の規則を適用する必要があります。

    ![firewall rules](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. レプリケーションを有効にします。 移行する VM のレプリケーションを有効にします。 EC2 コンソールから取得できるプライベート IP アドレスを使用して、EC2 インスタンスを検出できます。
7. 計画されていないフェールオーバーを実行します。 初期レプリケーションが完了したら、各 VM に対して、AWS から Azure への計画されていないフェールオーバーを実行できます。 必要に応じて、復旧計画を作成し、計画されていないフェールオーバーを実行して、複数の仮想マシンを AWS から Azure に移行できます。 [こちら](site-recovery-create-recovery-plans.md) をご覧ください。

詳細については、[デプロイの手順](site-recovery-vmware-to-azure.md)および[計画されていないフェールオーバー](site-recovery-failover.md#run-an-unplanned-failover)の実行に関するページを参照してください。



<!--HONumber=Feb17_HO2-->


