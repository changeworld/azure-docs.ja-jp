---
title: Hyper-V VM から Azure へのディザスター リカバリーのためにオンプレミス Hyper-V サーバーを準備する | Microsoft Docs
description: Azure Site Recovery サービスを使用して、Azure へのディザスター リカバリーのために、System Center VMM で管理されていないオンプレミス Hyper-V VM を準備する方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fb820d124fd9b5e882cad538ad436532d7865fbc
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37923490"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Azure へのディザスター リカバリーのためにオンプレミス Hyper-V サーバーを準備する

このチュートリアルでは、Hyper-V VM を Azure にレプリケートするときに、ディザスター リカバリーの目的でオンプレミス Hyper-V インフラストラクチャを準備する方法を説明します。 Hyper-V ホストは System Center Virtual Machine Manager (VMM) で管理できますが、必須ではありません。  このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 必要に応じて、Hyper-V 要件と VMM 要件を確認する
> * 該当する場合は VMM を準備する
> * Azure の場所へのインターネット アクセスを確認する
> * Azure にフェールオーバーした後に VM にアクセスできるように VM を準備する

これは、このシリーズの 2 番目のチュートリアルです。 前のチュートリアルで説明されているように、[Azure コンポーネントを設定](tutorial-prepare-azure.md)しておいてください。



## <a name="review-requirements-and-prerequisites"></a>要件と前提条件を確認する

Hyper-V ホストと VM が要件に準拠していることを確認します。

1. オンプレミスのサーバーの要件を[確認](hyper-v-azure-support-matrix.md#on-premises-servers)します。
2. Azure にレプリケートする Hyper-V VM の[要件を確認](hyper-v-azure-support-matrix.md#replicated-vms)します。
3. Hyper-V ホストの[ネットワーク](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)と、ホストおよびゲスト [ストレージ](hyper-v-azure-support-matrix.md#hyper-v-host-storage)がオンプレミスの Hyper-V ホストをサポートしていることを確認します。
4. [Azure のネットワーク](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover)、[ストレージ](hyper-v-azure-support-matrix.md#azure-storage)、[コンピューティング](hyper-v-azure-support-matrix.md#azure-compute-features)に関して、フェールオーバー後のサポートをチェックします。
5. Azure にレプリケートするオンプレミスの VM は、「[Azure VM の要件](hyper-v-azure-support-matrix.md#azure-vm-requirements)」に準拠している必要があります。


## <a name="prepare-vmm-optional"></a>VMM を準備する (省略可能)

Hyper-V ホストが VMM で管理される場合、オンプレミス VMM サーバーを用意する必要があります。 

- VMM サーバーに少なくとも 1 つのクラウドと、1 つまたは複数のホスト グループがあることを確認します。 VM を実行する Hyper-V ホストはクラウドに置いてください。
- ネットワーク マッピング用に VMM サーバーを準備します。

### <a name="prepare-vmm-for-network-mapping"></a>ネットワーク マッピング用に VMM を準備する

VMM を使用している場合、[ネットワーク マッピング](site-recovery-network-mapping.md)は、オンプレミス VMM VM ネットワークと Azure 仮想ネットワークの間でマッピングを行います。 マッピングによって、フェールバック後に作成されるとき、Azure VM は正しいネットワークに接続されます。

次の手順に従って、ネットワーク マッピング用に VMM を準備します。

1. Hyper-V ホストが置かれているクラウドに関連付けられている [VMM 論理ネットワーク](https://docs.microsoft.com/system-center/vmm/network-logical)があることを確認します。
2. 論理ネットワークにリンクされている [VM ネットワーク](https://docs.microsoft.com/system-center/vmm/network-virtual)があることを確認します。
3. VMM で、VM を VM ネットワークに接続します。

## <a name="verify-internet-access"></a>インターネット アクセスを確認する

1. このチュートリアルの目的として、最も簡単な構成は、Hyper-V ホストと VMM サーバーがプロキシを使用せずにインターネットに直接アクセスする方法です。 
2. Hyper-V ホストと VMM サーバー (関連する場合) が次の URL にアクセスできることを確認します。 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. IP アドレスでアクセスをコントロールしている場合は、次のことを確認してください。
    - IP アドレスベースのファイアウォール ルールが [Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)および HTTPS (443) ポートに接続できること。
    - お使いのサブスクリプションの Azure リージョンの IP アドレス範囲を許可します。


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

フェールオーバーのシナリオでは、レプリケートされたオンプレミス ネットワークに接続することがあります。

フェールオーバー後に RDP を使用して Windows VM に接続するには、次のアクセスを許可します。

1. インターネット経由でアクセスするには、フェールオーバーの前に、オンプレミスの VM 上の RDP を有効にします。 TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、**[Windows ファイアウォール]** > **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。
2. サイト間 VPN 経由でアクセスするには、オンプレミスのコンピューターで RDP を有効にします。 RDP は、**[Windows ファイアウォール]** -> **[許可されたアプリおよび機能]** から、**ドメインとプライベート** ネットワークでの使用を許可する必要があります。
   オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細情報](https://support.microsoft.com/kb/3031135)。 フェールオーバーをトリガーするときに、VM に保留中の Windows 更新プログラムがないようにします。 ある場合は、更新が完了するまで、仮想マシンにログインすることはできません。
3. フェールオーバー後の Microsoft Azure VM で **[ブート診断]** をオンにして、VM のスクリーンショットを確認します。 接続できない場合は、VM が実行中であることを確認したうえで、[トラブルシューティングのヒント](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を確認してください。

フェールオーバー後、Azure VM には、レプリケートされたオンプレミス VM と同じ IP アドレスか、別の IP アドレスを使用してアクセスできます。 フェールオーバー用の IP アドレスの設定については、[こちら](concepts-on-premises-to-azure-networking.md)を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する](tutorial-hyper-v-to-azure.md)
> [VMM クラウドに Hyper-V VM の Azure へのディザスター リカバリーを設定する](tutorial-hyper-v-vmm-to-azure.md)
