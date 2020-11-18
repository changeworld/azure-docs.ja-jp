---
title: チュートリアル - Azure VMware Solution で NSX-T ネットワーク セグメントを追加する
description: 仮想マシン (VM) に使用する NSX-T ネットワーク セグメントを vCenter で作成する方法について説明します。
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 8ecb37a42e2986bd1c6261b8fe6c23382323b31d
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335050"
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

- [Azure VMware Solution 用の DHCP を作成して管理する](manage-dhcp.md)
- [Azure VMware Solution で VM をデプロイするためのコンテンツ ライブラリを作成する](deploy-vm-content-library.md) 
- [オンプレミス環境をプライベート クラウドにピアリングする](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
