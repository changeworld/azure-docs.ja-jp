---
title: Azure VMware Solutions (AVS) - オンプレミス ワークロード用のディザスター サイトとして、AVS プライベート クラウドを使用する
description: オンプレミスの VMware ワークロード用のディザスター リカバリー サイトとして、AVS プライベート クラウドを設定する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5ee43af97e79f1e835787d61bd79cfb256ef445
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083136"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>オンプレミスの VMware ワークロード用のディザスター リカバリー サイトとして AVS プライベート クラウドを設定する

AVS プライベート クラウドは、災害時にもビジネス継続性を提供するための、オンプレミス アプリケーションの復旧サイトとして設定できます。 この復旧ソリューションは、レプリケーションおよびオーケストレーションのプラットフォームとしての Zerto Virtual Replication に基づいています。 重要なインフラストラクチャとアプリケーション仮想マシンを、オンプレミスの vCenter から AVS プライベート クラウドに継続的にレプリケートできます。 AVS プライベート クラウドは、フェールオーバー テストの実施と、災害時のアプリケーションの可用性の確保のために使用することができます。 同様のアプローチに従って、AVS プライベート クラウドを、別の場所にある復旧サイトによって保護されるプライマリ サイトとして設定することができます。

> [!NOTE]
> お使いのディザスター リカバリー環境のサイズ設定に関するガイドラインについては、Zerto のドキュメント「[Zerto Virtual Replication のサイズ設定に関する考慮事項](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf)」を参照してください。

AVS ソリューション:

* ディザスター リカバリー (DR) 専用のデータセンターを設定する必要がなくなります。
* AVS がデプロイされている Azure の場所を活用して、世界規模での地理的回復性を実現できます。
* DR のためのデプロイ コストと総保有コストを削減できるようになります。

このソリューションでは、次のことを行う必要があります。

* AVS プライベート クラウド内で Zerto をインストール、構成、管理します。
* AVS プライベート クラウドが保護されたサイトの場合は、Zerto に独自のライセンスを提供します。 AVS サイトで実行されている Zerto とお使いのオンプレミス サイトをペアリングすることによって、ライセンスを付与できます。

次の図は、Zerto ソリューション用のアーキテクチャを示しています。

![Architecture](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>ソリューションをデプロイする方法

以下のセクションでは、AVS プライベート クラウド内で Zerto Virtual Replication を使用して、DR ソリューションをデプロイする方法について説明します。

1. [前提条件](#prerequisites)
2. [お使いの AVS プライベート クラウド上でのオプションの構成](#optional-configuration-on-your-avs-private-cloud)
3. [AVS プライベート クラウド上での ZVM と VRA の設定](#set-up-zvm-and-vra-on-your-avs-private-cloud)
4. [Zerto Virtual Protection Group の設定](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>前提条件

オンプレミス環境から AVS プライベート クラウドへの Zerto Virtual Replication を有効にするには、次の前提条件を満たす必要があります。

1. [お使いのオンプレミス ネットワークと AVS プライベート クラウドとの間に、サイト間 VPN 接続を設定します](set-up-vpn.md)。
2. [お使いの AVS プライベート クラウドの管理コンポーネントが、AVS プライベート クラウドの DNS サーバーに転送されるように DNS 参照を設定します](on-premises-dns-setup.md)。 DNS 参照の転送を有効にするには、`*.cloudsimple.io` 用のオンプレミス DNS サーバーに、AVS DNS サーバーへの転送ゾーン エントリを作成します。
3. オンプレミスの vCenter コンポーネントがオンプレミス DNS サーバーに転送されるように、DNS 参照を設定します。 DNS サーバーは、AVS プライベート クラウドからサイト間 VPN を経由して到達可能になっている必要があります。 サポートが必要な場合は、次の情報を記載した[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を送信してください。 

    * オンプレミスの DNS ドメイン名
    * オンプレミス DNS サーバーの IP アドレス

4. AVS プライベート クラウドに Windows サーバーをインストールします。 このサーバーは Zerto Virtual Manager のインストール先として使用します。
5. [AVS 特権をエスカレートします](escalate-private-cloud-privileges.md)。
6. Zerto Virtual Manager のサービス アカウントとして使用するために、管理者ロールがある新しいユーザーを AVS プライベート クラウドの vCenter に作成します。

### <a name="optional-configuration-on-your-avs-private-cloud"></a>お使いの AVS プライベート クラウド上でのオプションの構成

1. AVS プライベート クラウドの vCenter に 1 つまたは複数のリソース プールを作成します。これは、VM 用のターゲット リソース プールとして、オンプレミス環境から使用するためのものです。
2. AVS プライベート クラウドの vCenter に 1 つまたは複数のフォルダーを作成します。これは、VM 用のターゲット フォルダーとしてオンプレミス環境から使用するためのものです。
3. フェールオーバー ネットワーク用の VLAN を作成し、ファイアウォール規則を設定します。 サポートが必要な場合は、[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いてください。
4. フェールオーバー ネットワーク用の分散ポート グループを作成し、VM のフェールオーバーをテストするためのネットワークをテストします。
5. [DHCP および DNS サーバー](dns-dhcp-setup.md)をインストールするか、お使いの AVS プライベート クラウド環境で Active Directory ドメイン コントローラーを使用します。

### <a name="set-up-zvm-and-vra-on-your-avs-private-cloud"></a>AVS プライベート クラウド上での ZVM と VRA の設定

1. AVS プライベート クラウド内の Windows サーバーに Zerto Virtual Manager (ZVM) をインストールします。
2. 前の手順で作成したサービス アカウントを使用して ZVM にサインインします。
3. Zerto Virtual Manager のライセンスを設定します。
4. Zerto Virtual Replication Appliance (VRA) を、お使いの AVS プライベート クラウドの ESXi ホスト上にインストールします。
5. AVS プライベート クラウドの ZVM をお使いのオンプレミスの ZVM とペアリングします。

### <a name="set-up-zerto-virtual-protection-group"></a>Zerto Virtual Protection Group の設定

1. 新しい Virtual Protection Group (VPG) を作成し、その VPG の優先順位を指定します。
2. ビジネス継続性のための保護を必要とする仮想マシンを選択し、必要に応じて起動順序をカスタマイズします。
3. お使いの AVS プライベート クラウドを復旧サイトとして選択し、AVS プライベート クラウド クラスターまたは作成したリソース グループを既定の復旧サーバーとして選択します。 お使いの AVS プライベート クラウド上の復旧データストアとして、**vsanDatastore** を選択します。

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > [VM Settings]\(VM 設定\) オプションでは、個々の VM のホスト オプションをカスタマイズできます。

4. 必要に応じてストレージ オプションをカスタマイズします。
5. 前の手順で作成した分散ポート グループを、フェールオーバー ネットワークとフェールオーバー テスト ネットワーク用に使用する復旧ネットワークとして指定します。また、必要に応じて復旧スクリプトをカスタマイズします。
6. 必要に応じて個々の VM のネットワーク設定をカスタマイズし、VPG を作成します。
7. レプリケーションが完了したら、フェールオーバーをテストします。

## <a name="reference"></a>リファレンス

[Zerto のドキュメント](https://www.zerto.com/myzerto/technical-documentation/)
