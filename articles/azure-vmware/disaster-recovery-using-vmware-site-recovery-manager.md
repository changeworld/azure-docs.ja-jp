---
title: VMware Site Recovery Manager を使用してディザスター リカバリーをデプロイする
description: Azure VMware Solution のプライベート クラウドで VMware Site Recovery Manager (SRM) を使用してディザスター リカバリーをデプロイします。
ms.topic: how-to
ms.date: 10/04/2021
ms.openlocfilehash: 116c7b672874c1e012b4d9abadacb10bc4ca895f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547247"
---
# <a name="deploy-disaster-recovery-with-vmware-site-recovery-manager"></a>VMware Site Recovery Manager を使用してディザスター リカバリーをデプロイする

この記事では、オンプレミスの VMware 仮想マシン (VM) または Azure VMware Solution ベースの VM にディザスター リカバリーを実装する方法について説明します。  この記事のソリューションでは、Azure VMware Solution で [VMware Site Recovery Manager (SRM)](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) と vSphere Replication を使用します。 SRM サーバーとレプリケーション サーバーのインスタンスを、保護対象サイトと回復サイトの両方にデプロイします。       

SRM は、障害が発生した場合に、Azure VMware Solution 環境内の仮想マシンのダウンタイムを最小限に抑えるために設計されたディザスター リカバリー ソリューションです。 SRM によってフェールオーバーとフェールバックが自動化および調整され、障害時のダウンタイムが最小限に抑えられます。 また、組み込みの非破壊的テストにより、目標復旧時間が確実に達成されます。 全体として、SRM を使用すると、自動化によって管理が簡素化され、高速で十分予測可能な回復時間が保証されます。  

vSphere Replication は、VMware のハイパーバイザー ベースの vSphere VM 用レプリケーション テクノロジです。 それによって、サイトの部分的または完全な障害から VM が保護されます。 さらに、ストレージに依存しない VM 中心のレプリケーションにより、DR の保護が簡素化されます。 vSphere Replication は VM ごとに構成され、レプリケートされる VM をきめ細かく制御できます。

この記事では、オンプレミスの VMware 仮想マシン (VM) または Azure VMware Solution ベースの VM にディザスター リカバリーを実装します。


## <a name="supported-scenarios"></a>サポートされるシナリオ

SRM を使用すると、保護対象の vCenter Server サイトと回復 vCenter Server サイトの間での VM の回復を、計画、テスト、実行できます。 Azure VMware Solution での SRM は、次の 2 つの DR シナリオで使用できます。 

- オンプレミスの VMware から Azure VMware Solution へのプライベート クラウドのディザスター リカバリー 
- プライマリ Azure VMware Solution からセカンダリ Azure VMware Solution へのプライベート クラウドのディザスター リカバリー 

次の図は、プライマリ Azure VMware Solution からセカンダリ Azure VMware Solution へのシナリオのデプロイを示したものです。

:::image type="content" source="media/vmware-srm-vsphere-replication/vmware-site-recovery-manager-diagram.png" alt-text="Azure VMware Solution での VMware Site Recovery Manager (SRM) のディザスター リカバリー ソリューションを示した図。" border="false":::

SRM を使用すると、次のようなさまざまな種類の回復を実装できます。

- **計画的な移行** は、プライマリとセカンダリ両方の Azure VMware Solution サイトが実行されていて、完全に機能している場合に開始されます。 これは、保護対象サイトから回復サイトへの仮想マシンの整然とした移行であり、ワークロードが順番に移行されるときにデータの損失は予想されません。 

- SRM を使用した **ディザスター リカバリー** は、保護対象の Azure VMware Solution サイトが予期せずオフラインになったときに呼び出すことができます。 Site Recovery Manager によって、データ損失とシステム ダウンタイムを最小限に抑えるレプリケーション メカニズムを使用して回復プロセスが調整されます。

   Azure VMware Solution では、SRM と vSphere Replication の組み合わせを使用することで保護できるのは、ホスト上の個々の VM のみです。

- **双方向保護** では、ペアにされた SRM サイトの 1 つのセットを使用して、両方向で VM が保護されます。 異なる VM のセットがなければ、各サイトは同時に保護対象サイトと回復サイトになることができます。

>[!IMPORTANT]
>Azure VMware Solution では次のものはサポートされていません。 
>
>- 配列ベースのレプリケーションとストレージ ポリシーの保護グループ 
>- VVOL の保護グループ 
>- SRM コマンド ライン ツールを使用した SRM IP のカスタマイズ
>- 一対多および多対一のトポロジ 
>- カスタムの SRM プラグイン識別子または拡張 ID


## <a name="deployment-workflow"></a>デプロイのワークフロー

次に示すのは、プライマリ Azure VMware Solution からセカンダリへのワークフローの図です。 さらに、VM のエンドツーエンドの保護を実現するために、Azure VMware Solution の Azure portal および VMware 環境内で実行する手順も示されています。 

:::image type="content" source="media/vmware-srm-vsphere-replication/site-recovery-manager-workflow.png" alt-text="Azure VMware Solution での VMware Site Recovery Manager のデプロイ ワークフローを示す図。" border="false":::

## <a name="prerequisites"></a>前提条件

リモート vCenter の VRM 管理者と SRM 管理者ロールをリモート ユーザーに明示的に指定してください。

### <a name="scenario-on-premises-to-azure-vmware-solution"></a>シナリオ: オンプレミスから Azure VMware Solution へ 

- セカンダリ リージョンとしてデプロイされた Azure VMware Solution プライベート クラウド。

- オンプレミスの SRM と仮想クラウド アプライアンスへの [DNS 解決](configure-dns-azure-vmware-solution.md)。

   >[!NOTE]
   >2021 年 7 月 1 日以降に作成されたプライベート クラウドの場合は、プライベート DNS 解決を構成できます。 2021 年 7 月 1 日より前に作成されたプライベート クラウドで、プライベート DNS 解決が必要な場合は、[サポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)を開いて、**プライベート DNS の構成** を要求してください。

- オンプレミスと Azure VMware Solution の間の ExpressRoute 接続 - 2 Gbps。

### <a name="scenario-primary-azure-vmware-solution-to-secondary"></a>シナリオ: プライマリ Azure VMware Solution からセカンダリへ

- Azure VMware Solution のプライベート クラウドが、プライマリ リージョンとセカンダリ リージョンにデプロイされている必要があります。

   :::image type="content" source="media/vmware-srm-vsphere-replication/two-private-clouds-different-regions.png" alt-text="異なるリージョンの 2 つの Azure VMware Solution プライベート クラウドを示すスクリーンショット。":::
 
- ソースとターゲットの Azure VMware Solution プライベート クラウドの間の、ExpressRoute Global Reach などによる接続。

   :::image type="content" source="media/vmware-srm-vsphere-replication/global-reach-connectity-to-on-premises.png" alt-text="ソースとターゲットのプライベート クラウド間の接続を示すスクリーンショット。":::

 
## <a name="install-srm-in-azure-vmware-solution"></a>Azure VMware Solution に SRM をインストールする

1. オンプレミスのデータセンターに、VMware SRM と vSphere をインストールします。

   >[!NOTE]
   >[PSC ごとに 1 つの vCenter Server を使用する 2 サイト トポロジ](https://docs.vmware.com/en/Site-Recovery-Manager/8.4/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)のデプロイ モデルを使用します。 また、[必要な vSphere Replication ネットワーク ポート](https://kb.VMware.com/s/article/2087769)が開かれていることを確認します。

1. Azure VMware Solution プライベート クラウドで、 **[管理]** の下の **[アドオン]**  >  **[ディザスター リカバリー]** を選択します。

   Azure VMware Solution プライベート クラウドの既定の CloudAdmin ユーザーには、VMware SRM または vSphere Replication をインストールするための十分な特権がありません。 インストール プロセスには、「[前提条件](#prerequisites)」セクションで説明されている複数のステップが含まれます。 代わりに、Azure VMware Solution プライベート クラウドからアドオン サービスとして VMware SRM と vSphere Replication をインストールできます。

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png" alt-text="アドオンとして VMware SRM と vSphere Replication をインストールするための Azure VMware Solution プライベート クラウドのスクリーンショット" border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png":::

1. **[ディザスター リカバリー ソリューション]** ドロップダウンから、 **[VMware Site Recovery Manager (SRM) – vSphere Replication]** を選択します。 

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png" alt-text="[VMware Site Recovery Manager (SRM) - vSphere Replication] が選択されている [アドオン] の [ディザスター リカバリー] タブを示すスクリーンショット。" border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png":::

1. ライセンス キーを入力し、使用条件への同意を選択して、 **[インストール]** を選択します。

   >[!NOTE]
   >ライセンス キーを指定しないと、SRM は評価モードでインストールされます。 ライセンスは、VMware SRM を有効にするためにのみ使用されます。
   
   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png" alt-text="[ライセンス キー] フィールドが選択されている [アドオン] の下の [ディザスター リカバリー] タブを示すスクリーンショット。" border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png":::


## <a name="install-the-vsphere-replication-appliance"></a>vSphere Replication アプライアンスをインストールする

SRM アプライアンスが正常にインストールされた後、vSphere Replication アプライアンスをインストールする必要があります。 各レプリケーション サーバーは、最大 200 台の保護対象 VM に対応します。 ニーズに応じてスケールインまたはスケールアウトします。 

1. **[ディザスター リカバリー]** タブの **[Replication using]\(レプリケーションに使用するもの\)** ドロップダウンから、 **[vSphere Replication]** を選択します。

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-1.png" alt-text="[Replication using]\(レプリケーションに使用するもの\) オプションで選択された [vSphere Replication] を示すスクリーンショット。":::

1. vSphere サーバーのスライダーを移動し、保護対象の VM の数に基づいて必要なレプリケーション サーバーの数を指定します。 その後、 **[インストール]** を選択します。

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-2.png" alt-text="レプリケーション サーバーの数を増減する方法を示すスクリーンショット。":::

1. インストールが済んだら、SRM と vSphere Replication の両方のアプライアンスがインストールされていることを確認します。

   >[!TIP]
   >[アンインストール] ボタンは、SRM と vSphere Replication の両方のアプライアンスが現在インストールされていることを示します。

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-3.png" alt-text="SRM と vSphere Replication の両方のアプライアンスがインストールされていることを示すスクリーンショット。":::
  

## <a name="configure-site-pairing-in-vcenter"></a>vCenter でのサイトのペアリングを構成する

VMware SRM と vSphere Replication をインストールした後は、vCenter で構成とサイトのペアリングを完了する必要があります。

1. cloudadmin@vsphere.local として vCenter にサインインします。

1. **[Site Recovery]** に移動し、vSphere Replication と VMware SRM の状態を確認した後、 **[OPEN Site Recovery]\(Site Recovery を開く\)** を選択してクライアントを起動します。

   :::image type="content" source="media/vmware-srm-vsphere-replication/open-site-recovery.png" alt-text="vSphere Replication と Site Recovery Manager のインストール状態が OK である vSphere Client を示すスクリーンショット。" border="true":::


1. 開かれた新しいタブの Site Recovery (SR) クライアントで、 **[NEW SITE PAIR]\(新しいサイト ペア\)** を選択します。
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/new-site-pair.png" alt-text="Site Recovery の [New Site Pair]\(新しいサイト ペア\) ボタンが選択された vSphere Client を示すスクリーンショット。" border="true":::

1. リモート サイトの詳細を入力し、 **[NEXT]\(次へ\)** を選択します。

   >[!NOTE]
   >Azure VMware Solution のプライベート クラウドは、組み込みの Platform Services Controller (PSC) を使用して動作するため、選択できるローカル vCenter は 1 つのみです。 リモート vCenter で埋め込みの Platform Service Controller (PSC) を使用している場合は、vCenter の FQDN (またはその IP アドレス) とポートを使用して PSC を指定します。 
   >
   >リモート ユーザーがペアリングを実行するには、十分なアクセス許可が必要です。 これを確実に行う簡単な方法は、そのユーザーにリモート vCenter での VRM 管理者と SRM 管理者のロールを与えることです。 リモート Azure VMware Solution プライベート クラウドの場合、cloudadmin はそれらのロールを使用して構成されます。

   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png" alt-text="新しいサイト ペアのサイトの詳細を示すスクリーンショット。" border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png":::

1. **[CONNECT]\(接続\)** を選択して、リモート vCenter の証明書を受け入れます。

   この時点で、クライアントにより、ペアにするサービスとして、両側の VRM アプライアンスと SRM アプライアンスが検出される必要があります。

1. ペアにするアプライアンスを選択して、 **[NEXT]\(次へ\)** を選択します。
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png" alt-text="新しいサイト ペアの vCenter Server とサービスの詳細を示すスクリーンショット。" border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png":::

1. **[CONNECT]\(接続\)** を選択して、リモート VMware SRM とリモート vCenter の証明書をもう一度受け入れます。

1. **[CONNECT]\(接続\)** を選択して、ローカル VMware SRM とローカル vCenter の証明書を受け入れます。

1. 設定を確認し、 **[FINISH]\(完了\)** を選択します。

   成功した場合、クライアントにペアリング用の別のパネルが表示されます。 一方、失敗した場合は、アラームが報告されます。

1. 右下にある二重の上向き矢印を選択してパネルを展開し、 **[Recent Tasks]\(最近のタスク\)** と **[Alarms]\(アラーム\)** を表示します。

   >[!NOTE]
   >SR クライアントの更新には時間がかかることがあります。 操作に時間がかかりすぎるか、"スタック" していると思われる場合は、メニュー バーの更新アイコンを選択します。 

1. **[VIEW DETAILS]\(詳細の表示\)** を選択して、リモート サイトのペアリング用のパネルを開きます。これにより、リモート vCenter にサインインするためのダイアログが開きます。

   :::image type="content" source="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png" alt-text="Site Recovery Manager と vSphere Replication の新しいサイト ペアの詳細を示すスクリーンショット。" border="true" lightbox="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png":::

1. レプリケーションとサイトの回復を行うのに十分なアクセス許可を持つユーザー名を入力し、 **[LOG IN]\(ログイン\)** を選択します。 

   ペアリングの場合、多くの場合は異なるユーザーであるログインは、ペアリングを確立するための 1 回限りのアクションです。 SR クライアントでペアリングを使用するには、クライアントが起動されるたびにこのログインが必要です。

   >[!NOTE] 
   >十分なアクセス許可を持つユーザーには、リモート vCenter で **VRM 管理者** と **SRM 管理者** のロールが与えられている必要があります。 また、そのユーザーは、フォルダーやデータストアなどのリモート vCenter インベントリにもアクセスできる必要があります。 リモート Azure VMware Solution のプライベート クラウドの場合、cloudadmin ユーザーには適切なアクセス許可とアクセス権があります。 
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/sign-into-remote-vcenter.png" alt-text="vCenter Server の資格情報を示すスクリーンショット。" border="true":::

   ローカル VRM の埋め込み VRS が実行されていないことを示す警告メッセージが表示されます。  これは、Azure VMware Solution によって、Azure VMware Solution プライベート クラウドの埋め込み VRS が使用されていないためです。  代わりに、VRS アプライアンスが使用されています。 

   :::image type=" content" source=" media/vmware-srm-vsphere-replication/pair-the-sites-summary.png" alt-text="Site Recovery Manager と vSphere Replication のサイト ペアの概要を示すスクリーンショット。" border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-summary.png":::

## <a name="srm-protection-reprotection-and-failback"></a>SRM の保護、再保護、フェールバック

サイトのペアリングを作成した後は、次に示す VMware のドキュメントに従って、Azure portal から VM をエンドツーエンドで保護します。

- [Using vSphere Replication with Site Recovery Manager (vSphere Replication と Site Recovery Manager の使用) (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2C77C830-892D-45FF-BA4F-80AC10085DBE.html)

- [Inventory Mappings for Array-Based Replication Protection Groups and vSphere Replication Protection Groups (配列ベースのレプリケーション保護グループと vSphere レプリケーション保護グループのインベントリ マッピング) (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2E2B4F84-D388-456B-AA3A-57FA8D47063D.html)

- [About Placeholder Virtual Machines (プレースホルダー仮想マシンについて) (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-EFE73B20-1C68-4D2C-8C86-A6E3C6214F07.html)

- [vSphere Replication Protection Groups (vSphere Replication 保護グループ) (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-CCF2E768-736E-4EAA-B3BE-50182635BC49.html)

- [Creating, Testing, and Running Recovery Plans (復旧計画の作成、テスト、実行) (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-AF6BF11B-4FB7-4543-A873-329FDF1524A4.html)

- [Configuring a Recovery Plan (復旧計画の構成) (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-FAC499CE-2994-46EF-9164-6D97EAF52C68.html)

- [Customizing IP Properties for Virtual Machines (仮想マシンの IP プロパティのカスタマイズ) (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-25B33730-14BE-4268-9D88-1129011AFB39.html)

- [How Site Recovery Manager Reprotects Virtual Machines with vSphere Replication (Site Recovery Manager により vSphere Replication で仮想マシンが再保護される方法) (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-1DE0E76D-1BA7-44D8-AEA2-5B2218E219B1.html)

- [Perform a Failback (フェールバックを実行する) (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-556E84C0-F8B7-4F9F-AAB0-0891C084EDE4.html)

   >[!NOTE]
   >AVS 環境とオンプレミス環境の間のネットワーク マッピングに IP カスタマイズ規則が定義されている場合、SRM 8.3.0 の[既知の問題](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-releasenotes-8-3.html#knownissues)が原因で、AVS 環境からオンプレミス環境へのフェールバック時にこれらの規則は適用されません。 この制限を回避するには、保護グループ内のすべての VM から保護を削除し、フェールバックを開始する前に保護を再構成します。


## <a name="ongoing-management-of-your-srm-solution"></a>SRM ソリューションの継続的な管理

Microsoft は、Azure VMware Solution プライベート クラウドでの VMware SRM と vSphere Replication のインストールを簡単にすることを目指していますが、ライセンスの管理とディザスター リカバリー ソリューションの日常的な操作は、お客様が行う必要があります。 

## <a name="scale-limitations"></a>スケールの制限

スケールの制限はプライベート クラウドごとにあります。

| 構成 | 制限 |
| --- | --- |
| 保護対象の仮想マシンの数  | 1000  |
| 復旧計画あたりの仮想マシンの数  | 1000  |
| 復旧計画あたりの保護グループの数  | 250  |
| RPO の値  | 5 分以上*  |
| 保護グループあたりの仮想マシンの合計数  | 500  |
| 復旧計画の合計数  | 250  |

\* 15 分未満の回復ポイントの目標 (RPO) の詳細については、"[vSphere レプリケーション管理ガイド](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-9E17D567-A947-49CD-8A84-8EA2D676B55A.html)" の _5 分の回復ポイントの目標のしくみ_ に関するページを参照してください。



## <a name="srm-licenses"></a>SRM のライセンス

評価版ライセンスまたは運用ライセンスを使用して、VMware SRM をインストールできます。  評価版ライセンスは 60 日間有効です。 評価期間が終了したら、VMware SRM の運用ライセンスを取得する必要があります。 

Azure VMware Solution のプライベート クラウドには、既存のオンプレミスの VMware SRM ライセンスを使用することはできません。 営業チームおよび VMware と連携して、VMware SRM の新しい契約ベースの運用ライセンスを取得します。 

SRM の運用ライセンスを取得した後は、Azure VMware Solution ポータルを使用して、新しい運用ライセンスで SRM を更新できます。 


## <a name="uninstall-srm"></a>SRM をアンインストールする 

SRM が不要になった場合は、クリーンな方法でアンインストールする必要があります。 SRM をアンインストールする前に、両方のサイトから正しい順序ですべての SRM 構成を削除する必要があります。 SRM をアンインストールする前にすべての構成を削除しないと、プレースホルダー VM などの一部の SRM コンポーネントが、Azure VMware Solution インフラストラクチャに残る可能性があります。

1. vSphere Client または vSphere Web Client で、 **[Site Recovery]**  >  **[Open Site Recovery]\(Site Recovery を開く\)** を選択します。

2. **[Site Recovery]** のホーム タブで、サイト ペアを選択し、 **[View Details]\(詳細の表示\)** を選択します。

3. **[Recovery Plans]\(復旧計画\)** タブを選択し、復旧計画を右クリックして、 **[Delete]\(削除\)** を選択します。

   >[!NOTE]
   >実行中の復旧計画を削除することはできません。

4. **[Protection Groups]\(保護グループ\)** タブを選択し、保護グループを選択して、 **[Virtual Machines]\(仮想マシン\)** タブを選択します。

5. すべての仮想マシンを強調表示にして、右クリックし、 **[Remove Protection]\(保護の削除\)** を選択します。

   VM から保護を削除すると、回復サイトからプレースホルダー VM が削除されます。 すべての保護グループについてこの操作を繰り返します。

6. **[Protection Groups]\(保護グループ\)** タブで、保護グループを右クリックして、 **[Delete]\(削除\)** を選択します。

   >[!NOTE] 
   >復旧計画に含まれている保護グループを削除することはできません。 保護がまだ構成されている仮想マシンが含まれる vSphere Replication 保護グループを削除することはできません。

7. **[Site Pair]\(サイト ペア\)**  >  **[Configure]\(構成\)** を選択して、すべてのインベントリ マッピングを削除します。

   a. **[Network Mappings]\(ネットワーク マッピング\)** 、 **[Folder Mappings]\(フォルダー マッピング\)** 、 **[Resource Mappings]\(リソース マッピング\)** の各タブを選択します。

   b. 各タブで、サイトを選択し、マッピングを右クリックして、 **[Delete]\(削除\)** を選択します。

8. 両方のサイトで、 **[Placeholder Datastores]\(プレースホルダー データストア\)** を選択し、プレースホルダー データストアを右クリックして、 **[Remove]\(削除\)** を選択します。

9. **[Site Pair]\(サイト ペア\)**  >  **[Summary]\(概要\)** を選択し、 **[Break Site Pair]\(サイト ペアの解除\)** を選択します。

   >[!NOTE] 
   >サイトのペアリングを解除すると、リモート サイトの Site Recovery Manager、vCenter Server、Platform Services Controller での Site Recovery Manager の登録に関連するすべての情報が削除されます。

10. プライベート クラウドで、 **[管理]** の下の **[アドオン]**  >  **[ディザスター リカバリー]** を選択し、 **[Uninstall the replication appliances]\(レプリケーション アプライアンスのアンインストール\)** を選択します。

11. レプリケーション アプライアンスがアンインストールされたら、 **[ディザスター リカバリー]** タブで、 **[Uninstall for the Site Recovery Manager]\(Site Recovery Manager のアンインストール\)** を選択します。

12. セカンダリ Azure VMware Solution サイトでこれらの手順を繰り返します。


## <a name="support"></a>サポート 

VMware SRM は、VMware のディザスター リカバリー ソリューションです。  

Microsoft でサポートされているのは、Azure VMware Solution 内での、SRM および vSphere Replication Manager のインストールとアンインストール、および vSphere Replication アプライアンスのスケールアップとスケールダウンだけです。 

構成やレプリケーションなど、他のすべての問題については、VMware にサポートを依頼してください。

VMware と Microsoft のサポート チームは、Azure VMware Solution での SRM の問題をトラブルシューティングするために、必要に応じて相互に連携します。


## <a name="references"></a>References

- [VMware Site Recovery Manager のドキュメント](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
- [VMware Site Recovery Manager 8.3 の互換性マトリックス](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-compat-matrix-8-3.html)
- [VMware SRM 8.3 リリース ノート](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-releasenotes-8-3.html)
- [VMware vSphere Replication のドキュメント](https://docs.vmware.com/en/vSphere-Replication/index.html)
- [vSphere Replication 8.3 の互換性マトリックス](https://docs.vmware.com/en/vSphere-Replication/8.3/rn/vsphere-replication-compat-matrix-8-3.html)
- [Site Recovery Manager 8.3 の運用上の制限](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-3AD7D565-8A27-450C-8493-7B53F995BB14.html)
- [vSphere Replication 8.3 の運用上の制限](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-E114BAB8-F423-45D4-B029-91A5D551AC47.html)
- [vSphere Replication の帯域幅の計算](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
- [SRM のインストールと構成](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-B3A49FFF-E3B9-45E3-AD35-093D896596A0.html)
- [vSphere Replication の管理](https://docs.vmware.com/en/vSphere-Replication/8.2/com.vmware.vsphere.replication-admin.doc/GUID-35C0A355-C57B-430B-876E-9D2E6BE4DDBA.html)
- [SRM のインストールの前提条件とベスト プラクティス](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
- [SRM のネットワーク ポート](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-499D3C83-B8FD-4D4C-AE3D-19F518A13C98.html)
- [vSphere Replication のネットワーク ポート](https://kb.vmware.com/s/article/2087769)
