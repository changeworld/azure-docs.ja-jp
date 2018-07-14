---
title: Azure Site Recovery の 2 つの Azure リージョン間で仮想ネットワークをマッピングする | Microsoft Docs
description: Azure Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、回復を調整します。 Azure またはセカンダリ データセンターへのフェールオーバーについて説明します。
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 7b7f9c079a1fc9d74fed4cc4d94d37f336ca5dc7
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916742"
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>異なる Azure リージョン間で仮想ネットワークをマッピングする


この記事では、異なる Azure リージョンに配置されている Azure 仮想ネットワークのインスタンスを相互にマッピングする方法について説明します。 ネットワーク マッピングによって、レプリケートされた仮想マシンがターゲットの Azure リージョンに作成されると、その仮想マシンは、ソース仮想マシンの仮想ネットワークにマッピングされている仮想ネットワークにも作成されます。  

## <a name="prerequisites"></a>前提条件
ネットワークをマッピングする前に、ソース リージョンとターゲットの Azure リージョンの両方で [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)が作成されていることを確認します。

## <a name="map-virtual-networks"></a>仮想ネットワークのマッピング

Azure リージョンに配置されている Azure 仮想ネットワーク (ソース ネットワーク) を、別のリージョンに配置されている仮想ネットワーク (ターゲット ネットワーク) にマッピングするには、Azure Virtual Machines の場合、**[Site Recovery インフラストラクチャ]**  >  **[ネットワーク マッピング]** に移動します。 ネットワーク マッピングを作成します。

![[ネットワーク マッピング] ウィンドウ - ネットワーク マッピングの作成](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


次の例では、仮想マシンは東アジア リージョンで実行されています。 仮想マシンは、東南アジア リージョンにレプリケートされています。

東アジア リージョンから東南アジア リージョンへのネットワーク マッピングを作成するには、ソース ネットワークの場所とターゲット ネットワークの場所を選択します。 **[OK]** をクリックします。

![[ネットワーク マッピングの追加] ウィンドウ - ソース ネットワークのソースとターゲットの場所を選択する](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


前の手順を繰り返して、東南アジア リージョンから東アジア リージョンへのネットワーク マッピングを作成します。

![[ネットワーク マッピングの追加] ウィンドウ - ターゲット ネットワークのソースとターゲットの場所を選択する](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>レプリケーションを有効にするときにネットワークをマッピングする

初めて Azure リージョンから別のリージョンに仮想マシンをレプリケートするときに、ネットワーク マッピングが存在しない場合は、レプリケーションの設定時にターゲット ネットワークを設定できます。 この設定に基づいて、Azure Site Recovery では、ソース リージョンからターゲット リージョンへのネットワーク マッピングと、ターゲット リージョンからソース リージョンへのネットワーク マッピングが作成されます。   

![[設定の構成] ウィンドウ - ターゲットの場所を選択する](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

既定では、Site Recovery では、ソース ネットワークと同じターゲット リージョンにネットワークが作成されます。 Site Recovery では、ソース ネットワークの名前にサフィックスとして **-asr** を追加して、ネットワークが作成されます。 作成されているネットワークを選ぶには、**[カスタマイズ]** を選択します。

![[ターゲットの設定のカスタマイズ] ウィンドウ - ターゲット リソース グループの名前とターゲット仮想ネットワークの名前を設定する](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

ネットワーク マッピングが既に行われている場合、レプリケーションを有効にするときに、ターゲット仮想ネットワークを変更することはできません。 この場合は、ターゲット仮想ネットワークを変更するには、既存のネットワーク マッピングを変更します。  

![[ターゲットの設定のカスタマイズ] ウィンドウ - ターゲット リソース グループの名前を設定する](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![[ネットワーク マッピングの変更] ウィンドウ - 既存のターゲット仮想ネットワークを変更する](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> リージョン A からリージョン B へのネットワーク マッピングを変更する場合は、リージョン B からリージョン A へのネットワーク マッピングも必ず変更してください。
>
>


## <a name="subnet-selection"></a>サブネットの選択
ターゲット仮想マシンのサブネットは、ソース仮想マシンのサブネットの名前に基づいて選択されます。 ソース仮想マシンと同じ名前のサブネットをターゲット ネットワークで利用できる場合は、そのサブネットがターゲット仮想マシンに対して選択されます。 ターゲット ネットワークに同じ名前のサブネットが存在しない場合は、アルファベット順で先にくるサブネットがターゲット サブネットとして設定されます。

サブネットを変更するには、仮想マシンの **[コンピューティングとネットワーク]** 設定に移動します。

![[コンピューティングとネットワーク] の [コンピューティングのプロパティ] ウィンドウ](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP アドレス

ターゲット仮想マシンの各ネットワーク インターフェイスの IP アドレスは、次のセクションで説明するように設定されます。

### <a name="dhcp"></a>DHCP
ソース仮想マシンのネットワーク インターフェイスが DHCP を使用する場合は、ターゲット仮想マシンのネットワーク インターフェイスも DHCP を使用するように設定されます。

### <a name="static-ip-address"></a>静的 IP アドレス
ソース仮想マシンのネットワーク インターフェイスが静的 IP アドレスを使用する場合は、ターゲット仮想マシンのネットワーク インターフェイスも静的 IP アドレスを使用するように設定されます。 次のセクションでは、静的 IP アドレスがどのように設定されるかについて説明します。

#### <a name="same-address-space"></a>同じアドレス空間

ソース サブネットとターゲット サブネットのアドレス空間が同じ場合、ソース仮想マシンのネットワーク インターフェイスの IP アドレスがターゲット IP アドレスとして設定されます。 同じ IP アドレスが使用できない場合は、次に使用可能な IP アドレスがターゲット IP アドレスとして設定されます。

#### <a name="different-address-spaces"></a>異なるアドレス空間

ソース サブネットとターゲット サブネットのアドレス空間が異なる場合、ターゲット サブネット内の次に使用可能な IP アドレスがターゲット IP アドレスとして設定されます。

各ネットワーク インターフェイスのターゲット IP を変更するには、仮想マシンの **[コンピューティングとネットワーク]** 設定に移動します。

## <a name="next-steps"></a>次の手順

* [Azure 仮想マシンのレプリケートに関するネットワーク ガイダンス](site-recovery-azure-to-azure-networking-guidance.md)を確認します。
