---
title: Azure Site Recovery を使用した Azure への VMware レプリケーションのソース環境を設定する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して VMware VM を Azure にレプリケートするためのオンプレミスの環境を設定する方法について説明します。
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 01b0717660265b28d4ea7d804a761e7e425c997c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319602"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Azure への VMware レプリケーションのソース環境を設定する

この記事では、VMware VM を Azure にレプリケートするためのオンプレミスのソース環境を設定する方法について説明します。 これには、レプリケーション シナリオを選択するための手順、オンプレミス コンピューターを Site Recovery の構成サーバーとして設定するための手順、およびオンプレミスのVM を自動的に検出するための手順が含まれます。 

## <a name="prerequisites"></a>前提条件

この記事は、既に以下の操作を行っていることを前提としています。
- [Azure Portal](http://portal.azure.com) で[リソースを設定する](tutorial-prepare-azure.md)。
- [オンプレミスの VMware 設定する](vmware-azure-tutorial-prepare-on-premises.md) (自動検出のための専用アカウントを含む)。



## <a name="choose-your-protection-goals"></a>保護の目標を選択する

1. Azure Portal で、**Recovery Services** コンテナー ブレードに移動し、コンテナーを選択します。
2. コンテナーのリソース メニューで、**[作業の開始]** > **[Site Recovery]** > **[手順 1: インフラストラクチャを準備する]** > **[保護の目標]** の順にクリックします。

    ![Choose goals](./media/vmware-azure-set-up-source/choose-goals.png)
3. **[保護の目標]** で、**[To Azure (Azure へ)]** を選択し、**[Yes, with VMware vSphere Hypervisor (はい、VMware vSphere ハイパーバイザーを使用する)]** を選択します。 次に、 **[OK]** をクリックします

    ![Choose goals](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>構成サーバーを設定する

Open Virtualization Application (OVA) テンプレートを使用し、構成サーバーをオンプレミスの VMware VM として設定できます。 VMware VM にインストールされるコンポーネントについては、[こちら](concepts-vmware-to-azure-architecture.md)をご覧ください。

1. 構成サーバー デプロイの[前提条件](vmware-azure-deploy-configuration-server.md#prerequisites)を確認します。
2. デプロイに必要な[容量を確認](vmware-azure-deploy-configuration-server.md#capacity-planning)します。
3. OVA テンプレートを[ダウンロード](vmware-azure-deploy-configuration-server.md#download-the-template)して[インポート](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware)し、構成サーバーを実行するオンプレミス VMware VM を設定します。 テンプレートに付属するライセンスは評価版ライセンスとなり、180 日間有効です。 この期間が経過した後は、入手済みのライセンスを使用して Windows のライセンス認証をユーザーが行う必要があります。
4. VMware VM を有効にし、Recovery Services コンテナーに[登録](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services)します。


## <a name="add-the-vmware-account-for-automatic-discovery"></a>自動検出用の VMware アカウントを追加する

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>VMware サーバーに接続する

オンプレミス環境で実行されている仮想マシンを Azure Site Recovery で検出できるようにするには、VMware vCenter サーバーまたは vSphere ESXi ホストを Site Recovery に接続する必要があります。

**[+vCenter]** を選択して、VMware vCenter サーバーまたは VMware vSphere ESXi ホストの接続を開始します。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>一般的な問題
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>次の手順
Azure で[ターゲット環境を設定](./vmware-azure-set-up-target.md)します。
