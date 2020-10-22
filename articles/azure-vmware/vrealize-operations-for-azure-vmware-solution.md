---
title: Azure VMware Solution 向け vRealize Operations の設定
description: Azure VMware Solution のプライベート クラウド向けに vRealize Operations を設定する方法について説明します。
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9e512d107ddc4d9bca28323658d09f4b4b378dc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579457"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Azure VMware Solution 向け vRealize Operations の設定


vRealize Operations Manager は、VMware インフラストラクチャ管理者がシステム リソースの監視に使用できる運用管理プラットフォームです。 これらのシステム リソースとしては、アプリケーションレベルのオブジェクトまたはインフラストラクチャ レベルのオブジェクト (物理および仮想の両方) があります。 従来、ほとんどの VMware 管理者は、vRealize Operations を使用して VMware プライベート クラウドのコンポーネント (vCenter、ESXi、NSX-T、vSAN、Hybrid Cloud Extension (HCX)) を監視および管理していました。 各 Azure VMware Solution プライベート クラウドは、専用の vCenter、NSX-T、vSAN、HCX デプロイを使用してプロビジョニングされます。 

最初に、「[開始する前に](#before-you-begin)」と「[前提条件](#prerequisites)」をよくお読みください。 その後、Azure VMware Solution を使用する vRealize Operations Manager の 2 つの一般的なデプロイ トポロジについて説明します。

> [!div class="checklist"]
> * [Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Azure VMware Solution で動作する vRealize Operations のデプロイ](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>開始する前に
* vRealize Operations のデプロイの詳細については、[vRealize Operations Manager の製品ドキュメント](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)を確認します。 
* 基本的な Azure VMware Solution Software Defined Datacenter (SDDC) の[チュートリアル シリーズ](tutorial-network-checklist.md)を確認します。
* Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開オプションでは、必要に応じて、[vRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) の製品ドキュメントを確認します。 



## <a name="prerequisites"></a>前提条件
* オンプレミスと Azure VMware Solution SDDC の間で VPN または Azure ExpressRoute を構成する必要があります。
* Azure VMware Solution のプライベート クラウドが Azure にデプロイされていること。



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開
ほとんどの顧客は、1 つ以上のオンプレミスの vCenters ドメインを管理するために使用する既存のオンプレミスの vRealize Operations の展開があります。 次の図で示すように、顧客が Azure で新しい Azure VMware Solution プライベート クラウドをプロビジョニングする場合、通常、Azure ExpressRoute または Layer 3 VPN ソリューションのいずれかを使用して、オンプレミスの環境を Azure VMware Solution に接続します。   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開"  border="false":::

vRealize Operations 機能を Azure VMware Solution のプライベート クラウドに拡張するには、[Azure VMware Solution プライベート クラウド リソース用のアダプター インスタンス](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html)を作成して、Azure VMware Solution のプライベート クラウドからデータを収集し、それをオンプレミスの vRealize Operations に取り込みます。 オンプレミスの vRealize Operations Manager インスタンスは、Azure VMware Solution 上の vCenter および NSX-T マネージャーに直接接続できます。または、必要に応じて、vRealize Operations Remote Collector を Azure VMware Solution のプライベート クラウドにデプロイすることもできます。 vRealize Operations Remote Collector では、Azure VMware Solution のプライベート クラウドから収集したデータを圧縮して暗号化した後、ExpressRoute または VPN ネットワークを経由して、オンプレミスで実行されている vRealize Operations Manager に送信します。 

> [!TIP]
> vRealize Operations Manager をインストールするためのステップ バイ ステップ ガイドについては、[VMware のドキュメント](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)を参照してください。 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Azure VMware Solution で動作する vRealize Operations のデプロイ

もう 1 つのデプロイ オプションは、次の図で示すように、vRealize Operations Manager のインスタンスを、Azure VMware Solution プライベート クラウド内の vSphere クラスターの 1 つにデプロイする方法です。 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開" border="false":::

vRealize Operations の Azure VMware Solution インスタンスをデプロイすると、vCenter、ESXi、NSX-T、vSAN、HCX からデータを収集するように vRealize Operations を構成できます。 

> [!TIP]
> vRealize Operations Manager をインストールするためのステップ バイ ステップ ガイドについては、[VMware のドキュメント](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)を参照してください。


## <a name="known-limitations"></a>既知の制限事項

- Azure VMware Solution での **cloudadmin@vsphere.local** ユーザーは、[特権が制限されています](concepts-role-based-access-control.md)。 VMware ツールを使用したゲスト内のメモリ コレクションは、Azure VMware Solution 上の仮想マシン (VM) ではサポートされません。 この場合、アクティブで消費されたメモリの利用は引き続き機能します。
- Azure VMware Solution では、DRS 設定などのクラスター構成を管理するため、ホスト ベースのビジネス用インテントに向けたワークロードの最適化は機能しません。
- SDDC 内のクラスターをまたぐ配置に向けた、クラスター ベースのビジネス用インテントを使用するワークロードの最適化は、vRealize Operations Manager 8.0 以降で完全にサポートされています。 ただし、ワークロードの最適化では、リソース プールは認識されず、仮想マシンはクラスター レベルに配置されます。 ユーザーはこれを、Azure VMware Solution の vCenter Server インターフェイスで手動で修正できます。
- Azure VMware Solution の vCenter Server 資格情報を使用して、vRealize Operations Manager にサインインすることはできません。 
- Azure VMware Solution では、vRealize Operations Manager プラグインはサポートされていません。

vCenter Server クラウド アカウントを使用して Azure VMware Solution vCenter を vRealize Operations Manager に接続すると、次の警告が表示されます。

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開":::

この警告が発生するのは、Azure VMware Solution の **cloudadmin@vsphere.local** ユーザーに、登録のために必要なすべての vCenter Server 操作を実行するための十分な特権がないためです。 ただし、次に示すように、アダプター インスタンスでデータ収集を行うには十分な特権です。

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開":::

詳細については、「[vCenter アダプター インスタンスの構成に必要な特権](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html)」を参照してください。

<!-- LINKS - external -->


<!-- LINKS - internal -->




