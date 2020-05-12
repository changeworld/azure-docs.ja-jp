---
title: Azure Lab Services でピア ネットワークに接続する | Microsoft Docs
description: ラボのネットワークと別のネットワークをピアとして接続する方法について説明します。 たとえば、オンプレミスの学校/大学のネットワークと Azure のラボの仮想ネットワークを接続します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 9e53b6bdb041bfac5a82ed607b75b25ab0513f57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188006"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Azure Lab Services でラボのネットワークとピア仮想ネットワークを接続する

この記事では、ラボのネットワークと別のネットワークのピアリングに関する情報を提供します。

## <a name="overview"></a>概要

仮想ネットワーク ピアリングを使用すると、Azure 仮想ネットワーク間をシームレスに接続できます。 ピアリングされた仮想ネットワークは、接続において、見かけ上 1 つのネットワークとして機能します。 ピアリングされた仮想ネットワークに存在する仮想マシン間のトラフィックは Microsoft のバックボーン インフラストラクチャを介して、"プライベート" IP アドレスのみを使った同一仮想ネットワーク内の仮想マシン間のトラフィックと同じようにルーティングされます。 詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」をご覧ください。

次のようなシナリオでは、ラボのネットワークとピア仮想ネットワークの接続が必要なことがあります。

- ラボ内の仮想マシンに、ライセンスを取得するためにオンプレミスのライセンス サーバーに接続するソフトウェアが含まれている
- ラボ内の仮想マシンが、大学のネットワーク共有上のデータ セット (またはその他のすべてのファイル) にアクセスする必要がある

特定のオンプレミスのネットワークは [ExpressRoute](../../expressroute/expressroute-introduction.md) または [Virtual Network ゲートウェイ](../../vpn-gateway/vpn-gateway-about-vpngateways.md)のどちらかを使用して Azure Virtual Network に接続しています。 これらのサービスは、Azure Lab Services の外部で設定する必要があります。 ExpressRoute を使用してオンプレミスネットワークを Azure に接続する方法の詳細については、[ExpressRoute の概要](../../expressroute/expressroute-introduction.md)を参照してください。 Virtual Network ゲートウェイを使用してオンプレミスと接続するには、ゲートウェイ、指定された仮想ネットワーク、ラボのアカウントがすべて同じリージョンにある必要があります。

> [!NOTE]
> ラボ アカウントとピアリングされる予定の Azure Virtual Network の作成時には、クラスルーム ラボが作成される場所に、仮想ネットワークのリージョンがどのように影響するかを理解しておくことが重要です。  詳細については、管理者ガイドの「[リージョン (場所)](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)」セクションを参照してください。

## <a name="configure-at-the-time-of-lab-account-creation"></a>ラボ アカウントの作成時に構成する

新しい[ラボ アカウントの作成](tutorial-setup-lab-account.md)中に、 **[詳細]** タブの **[ピア仮想ネットワーク]** ドロップダウン リストに表示される既存の仮想ネットワークを選択することができます。この一覧には、ラボ アカウントと同じリージョンにある仮想ネットワークのみが表示されます。 選択した仮想ネットワークが、そのラボ アカウントで作成されたラボに接続 (ピアリング) されます。  この変更を行った後に作成されるラボのすべての仮想マシンが、ピアリングされた仮想ネットワーク上のリソースにアクセスできるようになります。

![ピアリングする VNet を選択する](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>アドレス範囲

ラボの仮想マシンの**アドレス範囲**を指定するオプションもあります。  **アドレス範囲**プロパティは、ラボで**ピア仮想ネットワーク**が有効になっている場合にのみ適用されます。  アドレス範囲が指定されている場合は、ラボ アカウントにあるラボ内のすべての仮想マシンがそのアドレス範囲内に作成されます。 アドレス範囲は CIDR 表記 (例: 10.20.0.0/20) で、既存のアドレス範囲と重複することはできません。  アドレス範囲を指定する場合は、作成される "*ラボ*" の数を考慮し、それに対応するアドレス範囲を指定することが重要です。 Lab Services では、1 つのラボにつき最大 512 台の仮想マシンを想定しています。  たとえば、"/23" を含む IP 範囲は、1 つのラボしか作成できません。  "/21" を含む範囲では、4 つのラボを作成できます。

**アドレス範囲**が指定されていない場合、Lab Services は、仮想ネットワークとピアリングされる仮想ネットワークを作成しているときに Azure によって提供される既定のアドレス範囲を使用します。  この範囲は、多くの場合、10.x.0.0/16 のようになります。  これにより、IP 範囲が重複する可能性があるため、ラボの設定でアドレス範囲を指定するか、ピアリングされている仮想ネットワークのアドレス範囲を確認してください。

## <a name="configure-after-the-lab-is-created"></a>ラボの作成後に構成する

ラボ アカウントの作成時にピア ネットワークを設定しなかった場合、**ラボ アカウント**のページの **[Labs configuration]\(ラボ構成\)** タブから同じプロパティを有効にすることができます。 この設定に加えられた変更は、変更後に作成されたラボにのみ適用されます。 画像のように、ラボ アカウントでラボに対して **[仮想ネットワークのピアリング]** を有効または無効にすることができます。

![ラボの作成後に VNet のピアリングを有効または無効にする](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

**[仮想ネットワークのピアリング]** フィールドで仮想ネットワークを選択すると、 **[ラボ作成者にラボの場所の選択を許可する]** オプションが無効になります。 この理由は、ラボ アカウント内のラボがピア仮想ネットワーク内のリソースと接続するには、それらがラボ アカウントと同じリージョンに存在する必要があるためです。

> [!IMPORTANT]
> ピアリングされた仮想ネットワークの設定は、変更が行われた後に作成されたラボにのみ適用され、既存のラボには適用されません。

## <a name="next-steps"></a>次のステップ

次の記事をご覧ください。

- [ラボ作成者にラボの場所の選択を許可する](allow-lab-creator-pick-lab-location.md)
- [ラボに共有イメージ ギャラリーをアタッチする](how-to-attach-detach-shared-image-gallery.md)
- [ユーザーをラボ所有者として追加する](how-to-add-user-lab-owner.md)
- [ラボのファイアウォール設定の表示](how-to-configure-firewall-settings.md)
- [ラボのその他の設定を構成する](how-to-configure-lab-accounts.md)
