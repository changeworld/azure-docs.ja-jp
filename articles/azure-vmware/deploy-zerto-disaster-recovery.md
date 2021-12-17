---
title: Zerto ディザスターリカバリーを Azure VMware ソリューションにデプロイする (初期可用性)
description: オンプレミスの VMware または Azure VMware ソリューションの仮想マシンに Zerto ディザスターリカバリーを実装する方法について説明します。
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 8f7fcd27cdde9915f2e0c9c2467576aa59a0a478
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016911"
---
# <a name="deploy-zerto-disaster-recovery-on-azure-vmware-solution-initial-availability"></a>Zerto ディザスターリカバリーを Azure VMware ソリューションにデプロイする (初期可用性)

この記事では、オンプレミスの VMware または Azure VMware ソリューションベースの仮想マシン (Vm) のディザスターリカバリーを実装する方法について説明します。 この記事のソリューションでは、 [Zerto ディザスターリカバリー](https://www.zerto.com/solutions/use-cases/disaster-recovery/)を使用します。 Zerto のインスタンスは、保護されたサイトと復旧サイトの両方にデプロイされます。 

Zerto は、障害が発生した場合に Vm のダウンタイムを最小限に抑えるように設計されたディザスターリカバリーソリューションです。 Zerto のプラットフォームは、ほぼ最小限のデータ損失を可能にする継続的データ保護 (CDP) の基礎の上に構築されています。 これにより、多くのビジネス クリティカルおよびミッション クリティカルなエンタープライズ アプリケーションに必要なレベルの保護が提供されます。 Zerto はまた、フェールオーバーとフェールバックを自動化および調整することにより、障害時の最小限のダウンタイムを保証します。 全体として、Zerto では自動化を通して管理を簡素化し、高速で、かつ高度に予測可能な復旧時間を確保します。


## <a name="core-components-of-the-zerto-platform"></a>Zerto プラットフォームのコア コンポーネント

| コンポーネント | 説明 |
| --- | --- |
| **Zerto Virtual Manager (ZVM)**   | Windows VM にインストールされた Windows サービスとして実装される Zerto の管理アプリケーション。 プライベート クラウド管理者が Windows VM をインストールして管理します。 ZVM では、Day 0 と Day 2 のディザスターリカバリー構成が有効になります。 たとえば、プライマリおよびディザスター リカバリー サイトの構成、VM の保護、VM の復旧などを行います。 ただし、保護された顧客 VM のレプリケーション データは処理しません。     |
| **仮想レプリケーション アプライアンス (vRA)**   | ソースからレプリケーション ターゲットへのデータ レプリケーションを処理するための Linux VM。 ESXi ホストあたり vRA の 1 つのインスタンスがインストールされるため、プライベート クラウドのホストと共に拡張したり縮小したりする、真のスケール アーキテクチャが提供されます。 VRA は、保護された VM とそのローカルまたはリモート ターゲットとの間のデータ レプリケーションを管理し、データをジャーナルに格納します。    |
| **Zerto ESXi ホスト ドライバー**   | Zerto ディザスターリカバリー用に構成された各 VMware ESXi ホストにインストールされます。 このホスト ドライバーは vSphere VM の I/O をインターセプトし、そのホストの選択された vRA にレプリケーション データを送信します。 その後、vRA は、1つまたは複数のディザスターリカバリーターゲットに VM のデータをレプリケートします。    |
| **Zerto Cloud Appliance (ZCA)**   | Zerto を使用して vSphere VM を Azure ネイティブ IaaS VM として復旧する場合にのみ使用される Windows VM。 ZCA は、次のものから構成されます。<ul><li>**ZVM:** UI をホストし、管理やオーケストレーションのために Azure のネイティブ API と統合する Windows サービス。</li><li>**VRA:** Azure との間でデータをレプリケートする Windows サービス。</li></ul>ZCA は、それがデプロイされているプラットフォームとネイティブに統合するため、Microsoft Azure 上のストレージ アカウント内で Azure BLOB ストレージを使用できます。 その結果、これらの各プラットフォーム上での最も費用対効果の高いデプロイが保証されます。   |
| **Virtual Protection Group (VPG)**   | ZVM 上に作成された VM の論理グループ。 Zerto を使用すると、VPG でディザスターリカバリー、バックアップ、およびモビリティポリシーを構成できます。 このメカニズムにより、VM のグループに整合性のある一連のポリシーを適用できるようになります。  |


Zerto プラットフォームのアーキテクチャの詳細については、[Zerto プラットフォーム アーキテクチャ ガイド](https://www.zerto.com/wp-content/uploads/2021/07/Zerto-Platform-Architecture-Guide.pdf)を参照してください。 


## <a name="supported-zerto-scenarios"></a>サポートされている Zerto シナリオ

Zerto は Azure VMware Solution と共に次の 3 シナリオで使用できます。 

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-disaster-recovery"></a>シナリオ 1: オンプレミスの VMware から Azure への VMware ソリューションのディザスターリカバリー

このシナリオでは、プライマリ サイトは、オンプレミスの vSphere ベースの環境です。 ディザスターリカバリーサイトは、Azure VMware ソリューションのプライベートクラウドです。 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-1.png" alt-text="Azure VMware Solution 上の Zerto ディザスター リカバリー ソリューションのシナリオ 1 を示す図。":::


### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-cloud-disaster-recovery"></a>シナリオ 2: azure vmware ソリューションから Azure VMware ソリューションへのクラウドディザスターリカバリー

このシナリオでは、プライマリ サイトは、ある Azure リージョン内の Azure VMware Solution プライベート クラウドです。 ディザスターリカバリーサイトは、別の Azure リージョンの Azure VMware ソリューションプライベートクラウドです。

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2.png" alt-text="Azure VMware ソリューションの Zerto ディザスターリカバリーソリューションのシナリオ2を示す図" border="false":::


### <a name="scenario-3-azure-vmware-solution-to-iaas-vms-cloud-disaster-recovery"></a>シナリオ 3: IaaS Vm への Azure VMware ソリューションのクラウドディザスターリカバリー

このシナリオでは、プライマリ サイトは、ある Azure リージョン内の Azure VMware Solution プライベート クラウドです。 Azure Blob と Azure IaaS (Hyper-v ベース) Vm は、障害発生時に使用されます。

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-3.png" alt-text="Azure VMware ソリューションの Zerto ディザスターリカバリーソリューションのシナリオ3を示す図" border="false":::



## <a name="prerequisites"></a>前提条件

### <a name="on-premises-vmware-to-azure-vmware-solution-disaster-recovery"></a>オンプレミスの VMware から Azure への VMware ソリューションのディザスターリカバリー

- セカンダリ リージョンとしてデプロイされた Azure VMware Solution プライベート クラウド。

- オンプレミスと Azure VMware Solution の間の VPN または ExpressRoute 接続。



### <a name="azure-vmware-solution-to-azure-vmware-solution-cloud-disaster-recovery"></a>Azure vmware ソリューションから Azure VMware ソリューションへのクラウドディザスターリカバリー

- Azure VMware Solution のプライベート クラウドが、プライマリ リージョンとセカンダリ リージョンにデプロイされている必要があります。

   :::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2a-prerequisite.png" alt-text="図は、Azure VMware ソリューションでの Zerto ディザスターリカバリーソリューションのシナリオ2の最初の前提条件を示しています。":::
 
- ソースとターゲットの Azure VMware Solution プライベート クラウドの間の、ExpressRoute Global Reach などによる接続。

### <a name="azure-vmware-solution-iaas-vms-cloud-disaster-recovery"></a>Azure VMware ソリューション IaaS Vm クラウドのディザスターリカバリー

- Azure VMware Solution から、ディザスター リカバリーに使用される vNET へのネットワーク接続 (ExpressRoute ベース)。   

- 残りの前提条件については、[Zerto Virtual Replication Azure エンタープライズ ガイドライン](http://s3.amazonaws.com/zertodownload_docs/Latest/Zerto%20Virtual%20Replication%20Azure%20Enterprise%20Guidelines.pdf)に従ってください。



## <a name="install-zerto-on-azure-vmware-solution"></a>Azure VMware Solution 上に Zerto をインストールする

現時点では、Azure VMware ソリューションの Zerto ディザスターリカバリーは、初期の可用性 (IA) フェーズです。 IA フェーズでは、Microsoft に連絡して IA サポートを要求し、認定を受ける必要があります。

Azure VMware Solution 上の Zerto に対する IA サポートを要求するには、zertoonavs@microsoft.com に電子メール要求を送信してください。 IA フェーズでは、Azure VMware Solution は、Zerto の手動インストールとオンボードのみをサポートしています。 ただし、Microsoft は、お客様が Zerto をプライベート クラウドに確実に手動でインストールできるよう協力いたします。

> [!NOTE]
> Microsoft では、手動インストールの一環として、Zerto 用の新しい vCenter ユーザーアカウントを作成します。 このユーザーアカウントは、Azure VMware Solution vCenter で操作を実行するための Zerto Virtual Manager (ZVM) に対してのみ使用されます。 ZVMをAzure VMware Solutionにインストールする際、「Zerto vCenterの権限を使用してロールとパーミッションを実施するように選択する」オプションを選択しないでください。 


ZVM のインストール後、Zerto Virtual Manager **サイト設定** から以下のオプションを選択します。 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-install-5.png" alt-text="青色のチェックボックスの一覧に表示されるすべてのオプションを選択するためのワークロードの自動化に関するセクションのスクリーンショット。":::

>[!NOTE]
>Azure VMware Solution の一般提供によって、Azure VMware Solution 上の Zerto のセルフサービス インストールと Day 2 操作が可能になります。


## <a name="configure-zerto-for-disaster-recovery"></a>ディザスター リカバリー用に Zerto を構成する

オンプレミスの VMware から Azure への VMware ソリューションのディザスターリカバリーおよび Azure vmware ソリューションから azure vmware solution Zerto へのディザスターリカバリーシナリオに対応するように構成するには、 [Zerto Virtual Manager 管理ガイド vSphere 環境](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/Zerto%20Virtual%20Manager%20vSphere%20Administration%20Guide.pdf?cb=1629311409)を参照してください。


詳細については、[Zerto テクニカル ドキュメント](https://www.zerto.com/myzerto/technical-documentation/)を参照してください。 あるいは、[v8.5 Search Tool for Zerto ソフトウェア PDF ドキュメント バンドル](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/SEARCH_TOOL.zip?cb=1629311409)の Zerto ガイドの部分をすべてダウンロードすることもできます。



## <a name="ongoing-management-of-zerto"></a>Zerto の継続的な管理

- Azure VMware ソリューションのプライベートクラウド操作をスケーリングする場合は、Zerto 保護のために新しい Azure VMware ソリューションホストを追加するか、新しい Azure VMware Solution vSphere クラスターに Zerto ディザスターリカバリーを構成することが必要になる場合があります。 どちらのシナリオでも、最初の可用性フェーズで Azure VMware ソリューションチームとのサポート要求を開く必要があります。 2日構成の Azure portal から [サポートチケット](https://rc.portal.azure.com/#create/Microsoft.Support) を開くことができます。 

   :::image type="content" source="media/zerto-disaster-recovery/support-request-zerto-disaster-recovery.png" alt-text="2日目の Zerto ディザスターリカバリー構成のサポートリクエストを示すスクリーンショット。":::

- GA フェーズでは、上記のすべての操作が、自動化されたセルフサービスの方法で有効になります。


## <a name="faqs"></a>FAQ

### <a name="can-i-use-a-pre-existing-zerto-product-license-on-azure-vmware-solution"></a>Azure VMware Solution で既存の Zerto 製品ライセンスを使用できますか?

Azure VMware Solution 環境用に既存の Zerto 製品ライセンスを再利用できます。 新しい Zerto ライセンスが必要な場合は、新しいライセンスを取得するために Zerto ( **info@zerto.com** ) に電子メールを送信してください。 

### <a name="how-is-zerto-supported"></a>Zerto はどのようにサポートされますか?

Zerto ディザスターリカバリーは、Zerto によって販売およびサポートされるソリューションです。 Zerto ディザスターリカバリーに関するサポートの問題については、常に [Zerto サポート](https://www.zerto.com/company/support-and-service/support/)にお問い合わせください。

Zerto と Microsoft のサポートチームは、Azure VMware ソリューションでのディザスターリカバリーに関する問題のトラブルシューティングを行うために、必要に応じて相互に連携します。

