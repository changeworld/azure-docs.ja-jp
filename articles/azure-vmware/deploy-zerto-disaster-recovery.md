---
title: Azure VMware Solution 上に Zerto Disaster Recovery をデプロイする (初期可用性)
description: オンプレミス VMware または Azure VMware Solution 仮想マシン用に Zerto Disaster Recovery を実装する方法について説明します。
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 9e919f8fa22965bdd7a170db2c19bd921de030cd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475297"
---
# <a name="deploy-zerto-disaster-recovery-on-azure-vmware-solution-initial-availability"></a>Azure VMware Solution 上に Zerto Disaster Recovery をデプロイする (初期可用性)

この記事では、オンプレミス VMware または Azure VMware Solution ベースの仮想マシン (VM) 用にディザスター リカバリー (DR) を実装する方法について説明します。 この記事のソリューションでは、[Zerto Disaster Recovery](https://www.zerto.com/solutions/use-cases/disaster-recovery/) を使用します。 Zerto のインスタンスは、保護されたサイトと復旧サイトの両方にデプロイされます。 

Zerto は、障害が発生した場合の VM のダウンタイムを最小限に抑えるように設計されています。 Zerto のプラットフォームは、ほぼ最小限のデータ損失を可能にする継続的データ保護 (CDP) の基礎の上に構築されています。 これにより、多くのビジネス クリティカルおよびミッション クリティカルなエンタープライズ アプリケーションに必要なレベルの保護が提供されます。 Zerto はまた、フェールオーバーとフェールバックを自動化および調整することにより、障害時の最小限のダウンタイムを保証します。 全体として、Zerto では自動化を通して管理を簡素化し、高速で、かつ高度に予測可能な復旧時間を確保します。


## <a name="core-components-of-the-zerto-platform"></a>Zerto プラットフォームのコア コンポーネント

| コンポーネント | 説明 |
| --- | --- |
| **Zerto Virtual Manager (ZVM)**   | Windows VM にインストールされた Windows サービスとして実装される Zerto の管理アプリケーション。 プライベート クラウド管理者が Windows VM をインストールして管理します。 ZVM によって、Day 0 および Day 2 DR 構成が可能になります。 たとえば、プライマリおよびディザスター リカバリー サイトの構成、VM の保護、VM の復旧などを行います。 ただし、保護された顧客 VM のレプリケーション データは処理しません。     |
| **仮想レプリケーション アプライアンス (vRA)**   | ソースからレプリケーション ターゲットへのデータ レプリケーションを処理するための Linux VM。 ESXi ホストあたり vRA の 1 つのインスタンスがインストールされるため、プライベート クラウドのホストと共に拡張したり縮小したりする、真のスケール アーキテクチャが提供されます。 VRA は、保護された VM とそのローカルまたはリモート ターゲットとの間のデータ レプリケーションを管理し、データをジャーナルに格納します。    |
| **Zerto ESXi ホスト ドライバー**   | Zerto DR 用に構成された各 VMware ESXi ホストにインストールされます。 このホスト ドライバーは vSphere VM の I/O をインターセプトし、そのホストの選択された vRA にレプリケーション データを送信します。 その後、VM のデータを 1 つ以上の DR ターゲットにレプリケートする役割は vRA が果たします。    |
| **Zerto Cloud Appliance (ZCA)**   | Zerto を使用して vSphere VM を Azure ネイティブ IaaS VM として復旧する場合にのみ使用される Windows VM。 ZCA は、次のものから構成されます。<ul><li>**ZVM:** UI をホストし、管理やオーケストレーションのために Azure のネイティブ API と統合する Windows サービス。</li><li>**VRA:** Azure との間でデータをレプリケートする Windows サービス。</li></ul>ZCA は、それがデプロイされているプラットフォームとネイティブに統合するため、Microsoft Azure 上のストレージ アカウント内で Azure BLOB ストレージを使用できます。 その結果、これらの各プラットフォーム上での最も費用対効果の高いデプロイが保証されます。   |
| **Virtual Protection Group (VPG)**   | ZVM 上に作成された VM の論理グループ。 Zerto では、VPG に対して DR、バックアップ、モビリティの各ポリシーを構成できます。 このメカニズムにより、VM のグループに整合性のある一連のポリシーを適用できるようになります。  |


Zerto プラットフォームのアーキテクチャの詳細については、[Zerto プラットフォーム アーキテクチャ ガイド](https://www.zerto.com/wp-content/uploads/2021/07/Zerto-Platform-Architecture-Guide.pdf)を参照してください。 


## <a name="supported-zerto-scenarios"></a>サポートされている Zerto シナリオ

Zerto は Azure VMware Solution と共に次の 3 シナリオで使用できます。 

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>シナリオ 1: オンプレミス VMware から Azure VMware Solution への DR

このシナリオでは、プライマリ サイトは、オンプレミスの vSphere ベースの環境です。 また、ディザスター リカバリー サイトは、Azure VMware Solution プライベート クラウドです。 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-1.png" alt-text="Azure VMware Solution 上の Zerto ディザスター リカバリー ソリューションのシナリオ 1 を示す図。" border="false":::



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-cloud-dr"></a>シナリオ 2: Azure VMware Solution から Azure VMware Solution へのクラウド DR

このシナリオでは、プライマリ サイトは、ある Azure リージョン内の Azure VMware Solution プライベート クラウドです。 一方、ディザスター リカバリー サイトは、別の Azure リージョン内の Azure VMware Solution プライベート クラウドです。

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2.png" alt-text="Azure VMware Solution 上の Zerto DR ソリューションのシナリオ 2 を示す図。" border="false":::


### <a name="scenario-3-azure-vmware-solution-to-iaas-vms-cloud-dr"></a>シナリオ 3: Azure VMware Solution から IaaS VM へのクラウド DR

このシナリオでは、プライマリ サイトは、ある Azure リージョン内の Azure VMware Solution プライベート クラウドです。 また、障害時には Azure BLOB と Azure IaaS (Hyper-V ベース) VM が使用されます。

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-3.png" alt-text="Azure VMware Solution 上の Zerto DR ソリューションのシナリオ 3 を示す図。" border="false":::



## <a name="prerequisites"></a>前提条件

### <a name="on-premises-vmware-to-azure-vmware-solution-dr"></a>オンプレミス VMware から Azure VMware Solution への DR

- セカンダリ リージョンとしてデプロイされた Azure VMware Solution プライベート クラウド。

- オンプレミスと Azure VMware Solution の間の VPN または ExpressRoute 接続。



### <a name="azure-vmware-solution-to-azure-vmware-solution-cloud-dr"></a>Azure VMware Solution から Azure VMware Solution へのクラウド DR

- Azure VMware Solution のプライベート クラウドが、プライマリ リージョンとセカンダリ リージョンにデプロイされている必要があります。

   :::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2a-prerequisite.png" alt-text="Azure VMware Solution 上の Zerto DR ソリューションのシナリオ 2 の最初の前提条件を示す図。":::
 
- ソースとターゲットの Azure VMware Solution プライベート クラウドの間の、ExpressRoute Global Reach などによる接続。



### <a name="azure-vmware-solution-iaas-vms-cloud-dr"></a>Azure VMware Solution から IaaS VM へのクラウド DR

- Azure VMware Solution から、ディザスター リカバリーに使用される vNET へのネットワーク接続 (ExpressRoute ベース)。   

- 残りの前提条件については、[Zerto Virtual Replication Azure エンタープライズ ガイドライン](http://s3.amazonaws.com/zertodownload_docs/Latest/Zerto%20Virtual%20Replication%20Azure%20Enterprise%20Guidelines.pdf)に従ってください。



## <a name="install-zerto-on-azure-vmware-solution"></a>Azure VMware Solution 上に Zerto をインストールする

現在、Azure VMware Solution 上の Zerto DR は初期可用性 (IA) フェーズにあります。 IA フェーズでは、Microsoft に連絡して IA サポートを要求し、認定を受ける必要があります。

Azure VMware Solution 上の Zerto に対する IA サポートを要求するには、zertoonavs@microsoft.com に電子メール要求を送信してください。 IA フェーズでは、Azure VMware Solution は、Zerto の手動インストールとオンボードのみをサポートしています。 ただし、Microsoft は、お客様が Zerto をプライベート クラウドに確実に手動でインストールできるよう協力いたします。

>[!NOTE]
>Azure VMware Solution の一般提供によって、Azure VMware Solution 上の Zerto のセルフサービス インストールと Day 2 操作が可能になります。


## <a name="configure-zerto-for-disaster-recovery"></a>ディザスター リカバリー用に Zerto を構成する

オンプレミス VMware から Azure VMware Solution への DR と、Azure VMware Solution から Azure VMware Solution へのクラウド DR のシナリオ用に Zerto を構成するには、[Zerto Virtual Manager 管理ガイド vSphere 環境](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/Zerto%20Virtual%20Manager%20vSphere%20Administration%20Guide.pdf?cb=1629311409)を参照してください。


詳細については、[Zerto テクニカル ドキュメント](https://www.zerto.com/myzerto/technical-documentation/)を参照してください。 あるいは、[v8.5 Search Tool for Zerto ソフトウェア PDF ドキュメント バンドル](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/SEARCH_TOOL.zip?cb=1629311409)の Zerto ガイドの部分をすべてダウンロードすることもできます。



## <a name="ongoing-management-of-zerto"></a>Zerto の継続的な管理

- Azure VMware Solution プライベート クラウド操作をスケーリングするときは、Zerto 保護のために新しい Azure VMware Solution ホストを追加するか、または新しい Azure VMware Solution vSphere クラスターに対して Zerto DR を構成することが必要になる場合があります。 このどちらのシナリオでも、新しいホスト用に Zerto vRA を追加するには、初期可用性フェーズで Azure VMware Solution チームへのサポート リクエストを開く必要があります。 次の Day 2 構成に対する[サポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)は、Azure Portal から開くことができます。   

   :::image type="content" source="media/zerto-disaster-recovery/support-request-zerto-disaster-recovery.png" alt-text="Day 2 Zerto DR 構成に対するサポート リクエストを示すスクリーンショット。":::

- Zerto-vSphere 統合 (vSphere DRS ハード アフィニティを使用) の性質上、クラスターのスケールダウン操作は失敗することが予測されます。 Azure VMware Solution SRE チームは、これらのエラーを IA フェーズでオフラインで修復する予定です。

- GA フェーズでは、上記のすべての操作が、自動化されたセルフサービスの方法で有効になります。


## <a name="faqs"></a>FAQ

### <a name="can-i-use-a-pre-existing-zerto-product-license-on-azure-vmware-solution"></a>Azure VMware Solution で既存の Zerto 製品ライセンスを使用できますか?

Azure VMware Solution 環境用に既存の Zerto 製品ライセンスを再利用できます。 新しい Zerto ライセンスが必要な場合は、新しいライセンスを取得するために Zerto ( **sales@zerto.com** ) に電子メールを送信してください。 

### <a name="how-is-zerto-supported"></a>Zerto はどのようにサポートされますか?

Zerto Disaster Recovery は、Zerto によって販売およびサポートされるソリューションです。 Zerto のサポートに関する問題がある場合は、いつでも [Zerto のサポート](https://www.zerto.com/company/support-and-service/support/)に問い合わせてください。

Zerto と Microsoft のサポート チームは、Azure VMware Solution での Zerto の問題をトラブルシューティングするために、必要に応じて相互に連携します。

