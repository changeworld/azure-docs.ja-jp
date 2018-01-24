---
title: "Hyper-V VM から Azure へのディザスター リカバリーのためにオンプレミス Hyper-V サーバーを準備する | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して、Azure へのディザスター リカバリーのために、System Center VMM で管理されていないオンプレミス Hyper-V VM を準備する方法について説明します。"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6e2837341e16f5077cfff18d4a34c097c165ef09
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2018
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Azure へのディザスター リカバリーのためにオンプレミス Hyper-V サーバーを準備する

このチュートリアルでは、Hyper-V VM を Azure にレプリケートするときに、ディザスター リカバリーの目的でオンプレミス Hyper-V インフラストラクチャを準備する方法を説明します。 Hyper-V ホストは System Center Virtual Machine Manager (VMM) で管理できますが、必須ではありません。  このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 必要に応じて、Hyper-V 要件と VMM 要件を確認する
> * 該当する場合は VMM を準備する
> * Azure の場所へのインターネット アクセスを確認する
> * Azure にフェールオーバーした後に VM にアクセスできるように VM を準備する

これは、このシリーズの 2 番目のチュートリアルです。 前のチュートリアルで説明されているように、[Azure コンポーネントを設定](tutorial-prepare-azure.md)しておいてください。



## <a name="review-server-requirements"></a>サーバーの要件を確認する

Hyper-V ホストが次の要件を満たしていることを確認します。 System Center Virtual Machine Manager (VMM) クラウドでホストを管理する場合は、VMM の要件を確認します。


**コンポーネント** | **VMM によって管理される Hyper-V** | **Hyper-V (VMM なし)**
--- | --- | ---
**Hyper-V ホスト オペレーティング システム** | Windows Server 2016、2012 R2 | 該当なし
**VMM** | VMM 2012、VMM 2012 R2 | 該当なし


## <a name="review-hyper-v-vm-requirements"></a>Hyper-V VM の要件を確認する

VM が次の表にまとめた要件を満たしていることを確認します。

**VM 要件** | **詳細**
--- | ---
**ゲスト オペレーティング システム** | [Azure がサポートする](https://technet.microsoft.com/library/cc794868.aspx)任意のゲスト OS
**Azure の要件** | オンプレミスの Hyper-V VM は、Azure VM の要件 (site-recovery-support-matrix-to-azure.md) を満たしている必要があります。

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

1. このチュートリアルの目的として、最も簡単な構成は、Hyper-V ホストと VMM サーバー (該当する場合) がプロキシを使用せずにインターネットに直接アクセスする方法です。 
2. Hyper-V ホストと VMM サーバー (関連する場合) が次の URL にアクセスできることを確認します。 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. 次のことを確認します。
    - IP アドレスベースのファイアウォール規則がある場合、その規則で Azure への通信を許可します。
    - [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。
    - ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

フェールオーバーのシナリオでは、レプリケートされたオンプレミス ネットワークに接続することがあります。

フェールオーバー後に RDP を使用して Windows VM に接続するには、次の操作を行います。

1. インターネット経由でアクセスするには、フェールオーバーの前に、オンプレミスの VM 上の RDP を有効にします。 TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、**[Windows ファイアウォール]** > **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。
2. サイト間 VPN 経由でアクセスするには、オンプレミスのコンピューターで RDP を有効にします。 RDP は、**[Windows ファイアウォール]** -> **[許可されたアプリおよび機能]** から、**ドメインとプライベート** ネットワークでの使用を許可する必要があります。
   オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細情報](https://support.microsoft.com/kb/3031135)。 フェールオーバーをトリガーするときに、VM に保留中の Windows 更新プログラムがないようにします。 ある場合は、更新が完了するまで、仮想マシンにログインすることはできません。
3. フェールオーバー後の Microsoft Azure VM で **[ブート診断]** をオンにして、VM のスクリーンショットを確認します。 接続できない場合は、VM が実行中であることを確認したうえで、[トラブルシューティングのヒント](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を確認してください。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する](tutorial-hyper-v-to-azure.md)
> [VMM クラウドに Hyper-V VM の Azure へのディザスター リカバリーを設定する](tutorial-hyper-v-vmm-to-azure.md)
