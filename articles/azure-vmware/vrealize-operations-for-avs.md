---
title: Azure VMware Solution (AVS) 向け vRealize Operations の設定
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475861"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>Azure VMware Solution (AVS) 向け vRealize Operations の設定


vRealize Operations Manager は、VMware インフラストラクチャ管理者がシステム リソースの監視に使用できる運用管理プラットフォームです。 これらのシステム リソースとしては、アプリケーションレベルのオブジェクトまたはインフラストラクチャ レベルのオブジェクト (物理および仮想の両方) があります。 従来、ほとんどの VMware 管理者は、vRealize Operations を使用して VMware プライベート クラウドのコンポーネント (vCenter、ESXi、NSX-T、vSAN、Hybrid Cloud Extension (HCX)) を監視および管理していました。 各 AVS プライベート クラウドは、専用の vCenter、NSX-T、vSAN、HCX デプロイを使用してプロビジョニングされます。 

最初に、「[開始する前に](#before-you-begin)」と「[前提条件](#prerequisites)」をよくお読みください。 その後、AVS を使用する vRealize Operations Manager の 2 つの一般的なデプロイ トポロジについて説明します。

> [!div class="checklist"]
> * [AVS を管理するオンプレミスの vRealize Operations の展開](#on-premises-vrealize-operations-managing-avs-deployment)
> * [AVS で実行する vRealize Operations のデプロイ](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>開始する前に
* vRealize Operations のデプロイの詳細については、[vRealize Operations Manager の製品ドキュメント](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)を確認します。 
* 基本的な AVS Software Defined Datacenter (SDDC) の[チュートリアル シリーズ](tutorial-network-checklist.md)を確認します。
* AVS を管理するオンプレミスの vRealize Operations の展開オプションでは、必要に応じて、[vRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) の製品ドキュメントを確認します。 



## <a name="prerequisites"></a>前提条件
* オンプレミスと AVS SDDC の間で VPN または Azure ExpressRoute を構成する必要があります。
* AVS プライベート クラウドが Azure にデプロイされていること。



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>AVS を管理するオンプレミスの vRealize Operations の展開
ほとんどの顧客は、1 つ以上のオンプレミスの vCenters ドメインを管理するために使用する既存のオンプレミスの vRealize Operations の展開があります。 次の図で示すように、顧客が Azure で新しい AVS プライベート クラウドをプロビジョニングする場合、通常、Azure ExpressRoute または Layer 3 VPN ソリューションのいずれかを使用して、オンプレミスの環境を AVS に接続します。   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="AVS を管理するオンプレミスの vRealize Operations の展開"  border="false":::

vRealize Operations 機能を AVS プライベート クラウドに拡張するには、[AVS プライベート クラウド リソース用のアダプター インスタンス](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html)を作成して、AVS プライベート クラウドからデータを収集し、それをオンプレミスの vRealize Operations に取り込みます。 オンプレミスの vRealize Operations Manager インスタンスは、AVS 上の vCenter および NSX-T マネージャーに直接接続できます。または、必要に応じて、vRealize Operations Remote Collector を AVS プライベート クラウドにデプロイすることもできます。 vRealize Operations Remote Collector では、AVS プライベート クラウドから収集したデータを圧縮して暗号化した後、ExpressRoute または VPN ネットワークを経由して、オンプレミスで実行されている vRealize Operations Manager に送信します。 

> [!TIP]
> vRealize Operations Manager をインストールするためのステップ バイ ステップ ガイドについては、[VMware のドキュメント](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)を参照してください。 



## <a name="vrealize-operations-running-on-avs-deployment"></a>AVS で実行する vRealize Operations のデプロイ

もう 1 つのデプロイ オプションは、次の図で示すように、vRealize Operations Manager のインスタンスを、AVS プライベート クラウド内の vSphere クラスターの 1 つにデプロイする方法です。 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="AVS で実行する vRealize Operations" border="false":::

vRealize Operations の AVS インスタンスをデプロイすると、vCenter、ESXi、NSX-T、vSAN、HCX からデータを収集するように vRealize Operations を構成できます。 

> [!TIP]
> vRealize Operations Manager をインストールするためのステップ バイ ステップ ガイドについては、[VMware のドキュメント](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)を参照してください。




<!-- LINKS - external -->


<!-- LINKS - internal -->




