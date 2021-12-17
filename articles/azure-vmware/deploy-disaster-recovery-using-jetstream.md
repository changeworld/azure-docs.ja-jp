---
title: JetStream DR を使用してディザスター リカバリーをデプロイする
description: Azure VMware Solution プライベート クラウドとオンプレミス VMware のワークロード用に、JetStream DR を実装する方法について説明します。
ms.topic: how-to
ms.date: 11/17/2021
ms.custom: references_regions
ms.openlocfilehash: 394cda9b1cfb45d260a13de910d734755e58cd42
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717844"
---
# <a name="deploy-disaster-recovery-using-jetstream-dr"></a>JetStream DR を使用してディザスター リカバリーをデプロイする


[JetStream DR](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) は、障害が発生した場合に仮想マシン (VM) のダウンタイムを最小限に抑えるように設計されたクラウドネイティブのディザスター リカバリー ソリューションです。 JetStream DR のインスタンスは、保護されたサイトと復旧サイトの両方にデプロイされます。 

JetStream は、最小限のデータ損失またはデータ損失ほぼなしを可能にする [VMware VAIO フレームワーク](https://core.vmware.com/resource/vmware-vsphere-apis-io-filtering-vaio)を使用して、継続的データ保護 (CDP) の基礎の上に構築されています。 JetStream DR により、ビジネス クリティカルおよびミッション クリティカルなアプリケーションに必要なレベルの保護が提供されます。 また、DR サイトで最小限のリソースを使用し、[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) などのコスト効果の高いクラウド ストレージを使用して、コスト効果の高い DR を実現します。

この記事では、Azure VMware Solution プライベート クラウドとオンプレミス VMware のワークロード用に、JetStream DR を実装する方法について説明します。

JetStream DR の詳細については、以下を参照してください。

- [JetStream ソリューションの概要](https://www.jetstreamsoft.com/2020/09/28/solution-brief-disaster-recovery-for-avs/)

- [Azure Marketplace での JetStream DR](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020)

- [JetStream サポート技術情報の記事](https://www.jetstreamsoft.com/resources/knowledge-base/)


## <a name="core-components-of-the-jetstream-dr-solution"></a>JetStream DR ソリューションのコア コンポーネント

| 項目 | 説明 |
| --- | --- |
| **JetStream Management サーバー仮想アプライアンス (MSA)**  | MSA では、プライマリ サイト、保護ドメイン、VM の回復など、Day 0 と Day 2 の構成を有効にします。  MSA は、クラウド管理者によって vSphere ノードにインストールされます。MSA は、vCenter からネイティブで JetStream DR を管理できる vCenter プラグインを実装します。 MSA は、保護された VM のレプリケーション データを処理しません。  | 
| **JetStream DR 仮想アプライアンス (DRVA)**  | Linux ベースの仮想マシン アプライアンスは、保護された VM のレプリケーション データをソース ESXi ホストから受け取ります。 レプリケーション データは、DR サイト (通常は Azure Blob Storage などのオブジェクト ストア) に格納する役割を担います。 プライベート cloudadmin では、保護されている VM の数とレプリケートするストレージの量に応じて、1 つまたは複数の DRVA インスタンスを作成できます。  | 
| **JetStream ESXi ホスト コンポーネント (IO フィルター パッケージ)**  | JetStream ソフトウェアは、JetStream DR 用に構成された各 ESXi ホストにインストールされます。 このホスト ドライバーは vSphere VM の I/O をインターセプトし、そのホストの DRVA にレプリケーション データを送信します。   | 
| **JetStream 保護ドメイン**  | 同じポリシーとラン ブックを使用してまとめて保護される VM の論理グループ。 保護ドメイン内のすべての VM のデータは、同じ Azure Blob コンテナー インスタンスに格納されます。 同じ DRVA インスタンスは、保護ドメイン内のすべての VM のリモート DR ストレージへのレプリケーションを処理します。   | 
| **Azure Blob Storage コンテナー**  | 保護された VM のレプリケートされたデータは、Azure Blob に格納されます。 JetStream ソフトウェアは、JetStream 保護ドメインごとに 1 つの Azure BLOB コンテナー インスタンスを作成します。    | 



## <a name="jetstream-scenarios-on-azure-vmware-solution"></a>Azure VMware Solution での JetStream シナリオ
JetStream DR は Azure VMware Solution と共に次の 2 つのシナリオで使用できます。  

- オンプレミス VMware から Azure VMware Solution への DR

- Azure VMware Solution から Azure VMware Solution への DR

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>シナリオ 1: オンプレミス VMware から Azure VMware Solution への DR

このシナリオでは、プライマリ サイトはオンプレミスの VMware 環境であり、DR サイトは Azure VMware Solution のプライベート クラウドです。

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="オンプレミスから Azure VMware Solution プライベート クラウドへの JetStream のデプロイを示す図。" border="false" lightbox="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png":::

### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>シナリオ 2: Azure VMware Solution から Azure VMware Solution への DR

このシナリオでは、プライマリ サイトは、ある Azure リージョン内の Azure VMware Solution プライベート クラウドです。 ディザスター リカバリー サイトは、別の Azure リージョン内の Azure VMware Solution プライベート クラウドです。 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Azure VMware Solution のプライベート クラウド間の JetStream のデプロイを示す図。" border="false" lightbox="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png":::


## <a name="prerequisites"></a>[前提条件]


### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>シナリオ 1: オンプレミス VMware から Azure VMware Solution への DR 

- ターゲット DR リージョンに 3 つ以上のノードを使用してデプロイされた Azure VMware Solution プライベート クラウド。  


   :::image type="content" source="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png" alt-text="Azure VMware Solution 上のディザスター リカバリー ソリューションの最初の前提条件を示す図。" lightbox="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png":::


- プライマリ サイト JetStream アプライアンスと Microsoft Azure Storage BLOB インスタンスの間に構成されたネットワーク接続。 

- Azure Marketplace から [JetStream DR をセットアップしてサブスクライブ](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020)し、JetStream DR ソフトウェアをダウンロードします。

- [Azure Blob Storage アカウント](../storage/common/storage-account-create.md)は、Standard または Premium パフォーマンス レベルのいずれかを使用して作成されます。 [[アクセス層]**で**[ホット]](../storage/blobs/access-tiers-overview.md) を選択します。 

   >[!NOTE]
   >Blob の **[階層型名前空間を有効にする]** オプションはサポートされていません。   

- Azure VMware Solution プライベート クラウドに構成された NSX-T ネットワーク セグメントであり、必要に応じて、JetStream 仮想アプライアンスのセグメントで DHCP を有効にします。  

- Azure VMware Solution vCenter、Azure VMware Solution ESXi ホスト、Azure Storage アカウント、JetStream 仮想アプライアンス用の JetStream Marketplace サービスの IP アドレスを解決するように構成された DNS サーバー。 



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>シナリオ 2: Azure VMware Solution から Azure VMware Solution への DR

- プライマリ リージョンとセカンダリ リージョンの両方に少なくとも 3 つのノードがデプロイされた Azure VMware Solution プライベート クラウド。  
 
- プライマリ サイト JetStream アプライアンスと Microsoft Azure Storage BLOB インスタンスの間に構成されたネットワーク接続。 

- Azure Marketplace から [JetStream DR をセットアップしてサブスクライブ](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020)し、JetStream DR ソフトウェアをダウンロードします。

- [Azure Blob Storage アカウント](../storage/common/storage-account-create.md)は、Standard または Premium パフォーマンス レベルのいずれかを使用して作成されます。 [[アクセス層]**で**[ホット]](../storage/blobs/access-tiers-overview.md) を選択します。 

   >[!NOTE]
   >Blob の **[階層型名前空間を有効にする]** オプションはサポートされていません。   

- Azure VMware Solution プライベート クラウドに構成された NSX-T ネットワーク セグメントであり、必要に応じて、JetStream 仮想アプライアンスのセグメントで DHCP を有効にします。  

- Azure VMware Solution vCenter、Azure VMware Solution ESXi ホスト、Azure Storage アカウント、JetStream 仮想アプライアンス用の JetStream Marketplace サービスの IP アドレスを解決するようにプライマリ サイトと DR サイトの両方で構成された DNS サーバー。 


オンプレミスの JetStream DR の前提条件の詳細については、[JetStream プレインストール ガイド](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/pre-installation-guidelines/)を参照してください。





## <a name="install-jetstream-dr-on-azure-vmware-solution"></a>Azure VMware Solution に JetStream DR をインストールする  
 
サポートされている両方のシナリオに対して、次の手順を実行できます。 
 
1. オンプレミスのデータセンターで、[JetStream のドキュメント](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/installing-jetstream-dr-software/)に従って JetStream DR をインストールします。  

1. Azure VMware Solution のプライベート クラウドで、Run コマンドを使用して JetStream DR をインストールします。 [Azure portal](https://portal.azure.com) から、 **[実行コマンド]**  >  **[パッケージ]**  >  **[JSDR.Configuration]** の順に選択します。  

   :::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="使用可能な JetStream 実行コマンドにアクセスする方法を示すスクリーンショット。" lightbox="media/run-command/run-command-overview-jetstream.png":::
 
   >[!NOTE]
   >Azure VMware Solution の既定の CloudAdmin ユーザーには、JetStream DR をインストールするための十分な特権がありません。  Azure VMware Solution では、JetStream DR 用の Azure VMware Solution Run コマンドを呼び出すことによって、JetStream DR の簡素化された自動インストールを実現します。  
 

1. JetStream DR をインストールするための前提条件が満たされているかどうかを確認する **Invoke-PreflightJetDRInstall** コマンドレットを実行します。 たとえば、必要な数のホスト、クラスター名、一意の VM 名が検証されます。 

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **Network**  | JetStream MSA をデプロイする必要がある、NSX-T ネットワーク セグメントの名前。  |
   | **データストア**  | MSA をデプロイするデータストアの名前。  |
   | **ProtectedCluster** | 保護する Azure VMware Solution プライベート クラウド クラスターの名前 (例: **Cluster-1**)。  指定できるクラスター名は 1 つだけです。 |
   | **クラスター** | JetStream MSA がデプロイされている Azure VMware Solution プライベート クラスターの名前 (例: **Cluster-1**)。 |
   | **VMName** | JetStream MSA VM の名前 (例: **jetstreamServer**)。 |
   | **実行の名前を指定**  | 実行の英数字の名前 (例: **Invoke-PreflightJetDRInstall-Exec1**)。 コマンドレットが正常に実行されたかどうかを確認するために使用されます。 |
   | **タイムアウト**  | 完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. [実行の状態を表示します](concepts-run-command.md#view-the-status-of-an-execution)。
   

## <a name="install-the-jetstream-dr-msa"></a>JetStream DR MSA をインストールする

Azure VMware Solution では、静的 IP アドレスまたは DHCP ベースの IP アドレスのいずれかを使用した JetStream のインストールがサポートされています。  

### <a name="static-ip-address"></a>静的 IP アドレス

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Install-JetDRWithStaticIP]** の順に選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。


   | **フィールド** | **Value** |
   | --- | --- |
   | **ProtectedCluster** | 保護する Azure VMware Solution プライベート クラウド クラスターの名前 (例: **Cluster-1**)。  インストール時に指定できるクラスター名は 1 つのみです。 |
   | **データストア**  | JetStream MSA をデプロイするデータストアの名前。  |
   | **VMName** | JetStream MSA VM の名前 (例: **jetstreamServer**)。 |
   | **クラスター** | JetStream MSA がデプロイされている Azure VMware Solution プライベート クラスターの名前 (例: **Cluster-1**)。 |
   | **ネットマスク** | デプロイする MSA のネットマスク (たとえば **22** や **24**)。 |
   | **MSIp** | JetStream MSA VM の IP アドレス。   |
   | **Dns** | JetStream MSA VM で使用する DNS IP。   |
   | **ゲートウェイ** | JetStream MSA VM のネットワーク ゲートウェイの IP アドレス。  |
   | **資格情報**  |  JetStream MSA VM のルート ユーザーの資格情報。   |
   | **HostName** | JetStream MSA VM のホスト名 (FQDN)。  |
   | **Network**  | JetStream MSA をデプロイする必要がある、NSX-T ネットワーク セグメントの名前。 |
   | **実行の名前を指定**  | 実行の英数字名 (例: **Install-JetDRWithStaticIP-Exec1**)。   コマンドレットが正常に実行されたかどうかを確認するために使用されます。  |


1. [実行の状態を表示します](concepts-run-command.md#view-the-status-of-an-execution)。


### <a name="dhcp-based-ip-address"></a>DHCP ベースの IP アドレス

この手順では、DR 保護が必要なクラスターに JetStream vSphere インストール バンドル (VIB) もインストールします。 

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Install-JetDRWithDHCP]** の順に選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **ProtectedCluster** | 保護する Azure VMware Solution プライベート クラウド クラスターの名前 (例: **Cluster-1**)。  インストール時に指定できるクラスター名は 1 つのみです。 |
   | **データストア**  | JetStream MSA をデプロイするデータストアの名前。  |
   | **VMName** | JetStream MSA VM の名前 (例: **jetstreamServer**)。 |
   | **クラスター** | JetStream MSA がデプロイされている Azure VMware Solution プライベート クラスターの名前 (例: **Cluster-1**)。 |
   | **資格情報**  |  JetStream MSA VM のルート ユーザーの資格情報。   |
   | **HostName** | JetStream MSA VM のホスト名 (FQDN)。  |
   | **Network**  | JetStream MSA をデプロイする必要がある、NSX-T ネットワーク セグメントの名前。 |
   | **実行の名前を指定**  | 実行の英数字名 (例: **Install-JetDRWithDHCP-Exec1**)。   コマンドレットが正常に実行されたかどうかを確認するために使用されます。  |
 
 
1. [実行の状態を表示します](concepts-run-command.md#view-the-status-of-an-execution)。
 
 
## <a name="add-jetstream-dr-to-new-azure-vmware-solution-clusters"></a>新しい Azure VMware Solution クラスターに JetStream DR を追加する  


1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Enable-JetDRForCluster]** の順に選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **ProtectedCluster** | 保護する Azure VMware Solution プライベート クラウド クラスターの名前 (例: **Cluster-1**)。  インストール時に指定できるクラスター名は 1 つのみです。 |
   | **資格情報**  |  JetStream MSA VM のルート ユーザーの資格情報。   |
   | **MSIp** | JetStream MSA VM の IP アドレス。   |
   | **実行の名前を指定**  | 実行の英数字名 (例: **Enable-JetDRForCluster-Exec1**)。   コマンドレットが正常に実行されたかどうかを確認するために使用されます。  |
  
1. [実行の状態を表示します](concepts-run-command.md#view-the-status-of-an-execution)。
 
 
 

## <a name="configure-jetstream-dr"></a>JetStream DR を構成する 
 
このセクションでは、JetStream DR を構成するために必要な手順の概要のみを説明します。  詳細な説明と手順については、[JetStream DR の構成](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/configuring-jetstream-dr/)に関するドキュメントを参照してください。 
 
JetStream DR MSA と JetStream VIB が Azure VMware Solution クラスターにインストールされた後、JetStream ポータルを使用して残りの構成手順を完了します。 



1. vCenter アプライアンスから JetStream ポータルにアクセスします。

1. [外部ストレージ サイトを追加](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/add-a-storage-site/)します。  

1. [JetStream DRVA アプライアンスをデプロイ](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/deploy-a-dr-virtual-appliance/)します。 

1. Azure VMware Solution クラスターで使用できるデータストアのいずれかを使用して、[JetStream レプリケーション ログ ストア ボリュームを作成](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-replication-log-store-volume/)します。 

   >[!TIP]
   >レプリケーション ログには、高速ローカル ストレージ (vSAN データストアなど) が推奨されます。 
 
1. [JetStream で保護されたドメインを作成](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-protected-domain/)します。 前の手順で作成した Azure Blob Storage サイト、JetStream DRVA インスタンス、レプリケーション ログを指定します。 

1. 保護する [VM を選択](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/select-vms-for-protection/)し、[VM 保護を開始](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/start-vm-protection/)します。

 
フェールオーバー Runbook の作成、DR サイトへのフェールオーバーの呼び出し、プライマリ サイトへのフェールバックの呼び出しなど、JetStream DR の残りの構成手順については、[JetStream 管理ガイドのドキュメント](https://www.jetstreamsoft.com/portal/jetstream-article-categories/product-manual/)を参照してください。  
 
## <a name="disable-jetstream-dr-on-an-azure-vmware-solution-cluster"></a>Azure VMware Solution クラスターで JetStream DR を無効にする  
 
このコマンドレットは、1 つのクラスターのみで JetStream DR を無効にします。JetStream DR を完全にアンインストールすることはありません。 

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Disable-JetDRForCluster]** の順に選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **ProtectedCluster** | 保護する Azure VMware Solution プライベート クラウド クラスターの名前 (例: **Cluster-1**)。  インストール時に指定できるクラスター名は 1 つのみです。 |
   | **資格情報**  |  JetStream MSA VM のルート ユーザーの資格情報。   |
   | **MSIp** | JetStream MSA VM の IP アドレス。   |
   | **実行の名前を指定**  | 実行の英数字名 (例: **Disable-JetDRForCluster-Exec1**)。   コマンドレットが正常に実行されたかどうかを確認するために使用されます。  |

1. [実行の状態を表示します](concepts-run-command.md#view-the-status-of-an-execution)。


 
## <a name="uninstall-jetstream-dr"></a>JetStream DR のアンインストール  

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Invoke-PreflightJetDRUninstall]** の順に選択します。 このコマンドレットは、クラスターに少なくとも 4 つのホスト (最小要件) が含まれるか確認します。 

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **ProtectedCluster** | 保護する Azure VMware Solution プライベート クラウド クラスターの名前 (例: **Cluster-1**)。  インストール時に指定できるクラスター名は 1 つのみです。 |
   | **資格情報**  |  JetStream MSA VM のルート ユーザーの資格情報。   |
   | **MSIp** | JetStream MSA VM の IP アドレス。   |
   | **実行の名前を指定**  | 実行の英数字の名前 (例: **Invoke-PreflightJetDRUninstall-Exec1**)。   コマンドレットが正常に実行されたかどうかを確認するために使用されます。  |

1. [実行の状態を表示します](concepts-run-command.md#view-the-status-of-an-execution)。

1. プレフライト コマンドレットが正常に完了したら、 **[Uninstall-JetDR]** を選択し、必要な値を指定するか既定値を変更して、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **ProtectedCluster** | 保護する Azure VMware Solution プライベート クラウド クラスターの名前 (例: **Cluster-1**)。  インストール時に指定できるクラスター名は 1 つのみです。 |
   | **資格情報**  |  JetStream MSA VM のルート ユーザーの資格情報。   |
   | **MSIp** | JetStream MSA VM の IP アドレス。   |
   | **実行の名前を指定**  | 実行の英数字名 (例: **Uninstall-JetDR-Exec1**)。   コマンドレットが正常に実行されたかどうかを確認するために使用されます。  |

 1. [実行の状態を表示します](concepts-run-command.md#view-the-status-of-an-execution)。


 
## <a name="support"></a>サポート  
 
JetStream DR は、[JetStream Software](https://www.jetstreamsoft.com/) がサポートするソリューションです。 JetStream に関する製品またはサポートの問題については、support-avs@jetstreamsoft.com にお問い合わせください。  
 
Azure VMware Solution は、実行コマンド (プレビュー) を使用して、JetStream DR のインストールとアンインストールの両方を自動化します。 実行コマンドに関する問題については、Microsoft サポートにお問い合わせください。 JetStream のインストールとアンインストールのコマンドレットに関する問題については、サポートについて JetStream にお問い合わせください。 



## <a name="next-steps"></a>次の手順

- [インフラストラクチャのセットアップ: JetStream DR for Azure VMware Solution](https://vimeo.com/480574312/b5386a871c) 

- [JetStream DR for Azure VMware Solution (完全なデモ)](https://vimeo.com/475620858/2ce9413248)
   
   - [JetStream DR for Azure VMware Solution の概要](https://vimeo.com/491880696/ec509ff8e3)

   - [VM の構成と保護](https://vimeo.com/491881616/d887590fb2)

   - [Azure VMware Solution へのフェールオーバー](https://vimeo.com/491883564/ca9fc57092)

   - [オンプレミスにフェールバックする](https://vimeo.com/491884402/65ee817b60)

