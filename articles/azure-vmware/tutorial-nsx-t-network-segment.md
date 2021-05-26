---
title: チュートリアル - Azure VMware Solution で NSX-T ネットワーク セグメントを追加する
description: 仮想マシン (VM) に使用する NSX-T ネットワーク セグメントを vCenter で作成する方法について説明します。
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 70a0602b037aaebd4b9776c3a8dafafcb173fa23
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110064462"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>チュートリアル:Azure VMware Solution にネットワーク セグメントを追加する 

vCenter に作成された仮想マシン (VM) は、NSX-T に作成されたネットワーク セグメントに配置されて、vCenter に表示されるようになります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * NSX-T Manager を操作してネットワーク セグメントを追加する
> * 新しいネットワーク セグメントを追加する
> * vCenter で新しいネットワーク セグメントを監視する

## <a name="prerequisites"></a>前提条件

vCenter と NSX-T Manager のインターフェイスへのアクセスが可能な Azure VMware Solution プライベート クラウド。 詳細については、[ネットワークの構成](tutorial-configure-networking.md)に関するチュートリアルを参照してください。

## <a name="add-a-network-segment"></a>ネットワーク セグメントの追加

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、VM に使用する NSX-T ネットワーク セグメントを vCenter で作成しました。 

次のことが可能になりました。 

- [Azure VMware Solution 用の DHCP を作成して管理する](configure-dhcp-azure-vmware-solution.md)
- [Azure VMware Solution で VM をデプロイするためのコンテンツ ライブラリを作成する](deploy-vm-content-library.md) 
- [オンプレミス環境をプライベート クラウドにピアリングする](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
