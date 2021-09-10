---
title: JetStream を使用してディザスター リカバリーをデプロイする
description: Azure VMware Solution プライベート クラウドとオンプレミス VMware のワークロード用に、JetStream ディザスター リカバリー (DR) をデプロイする方法について説明します。
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: a26cbaa4f8c37f3148a86005a637b006572685dc
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315126"
---
# <a name="deploy-disaster-recovery-using-jetstream"></a>JetStream を使用してディザスター リカバリーをデプロイする

[JetStream ディザスター リカバリー (DR)](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) は、VMware vSphere 環境にインストールされ、vCenter プラグイン アプライアンスを介して管理されます。 クラウドネイティブの継続的データ保護 (CDP) が提供され、仮想マシン (VM) の I/O 操作が絶えずレプリケートされます。 スナップショットを一定の間隔でキャプチャする代わりに、実行中のアプリケーションへの影響を最小限に抑えながら、プライマリ ストレージに書き込まれるときに、データが継続的にキャプチャされ、レプリケートされます。  その結果、VM とそのデータをすばやく復旧し、回復ポイントの目標 (RPO) を低くすることができます。  

Azure VMware Solution では、vSAN の回復クラスターまたは Azure NetApp Files などの接続されているファイル システムにデータを直接格納できます。 データは、vSphere 内で実行される I/O フィルターを介してキャプチャされます。 基になるデータ ストアには、VMFS、VSAN、vVol、または任意の HCI プラットフォームを指定できます。 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-disaster-recovery-diagram.png" alt-text="JetStream DR と Azure VMware Solution 統合を示す図。" border="false":::

JetStream DR ソフトウェアは、次の 3 つの主要なコンポーネントで構成されます。 

- 管理サーバー仮想アプライアンス (MSA) は、DR 保護の前にインストールおよび構成されます。 

- DR 仮想アプライアンス (DRVA) は、JetStream DR MSA によって自動的にデプロイされる .ISO イメージです。

- ホスト コンポーネント (IO フィルター パッケージ) 

MSA は、コンピューティング クラスターにホスト コンポーネントをインストールして構成し、さらに JetStream DR ソフトウェアを管理するために使用されます。 DRVA では、データ パス DR コンポーネントが実行されます。 スケーラビリティを向上するために、複数の DRV を同時に実行できます。 各 DRVA には、iSCSI LUN として、または低遅延 VDISK として接続された 1 つ以上の専用パーティションがあります。 パーティションは、永続的なメタデータのレプリケーション ログとリポジトリを保持するために使用されます。

この記事では、Azure VMware Solution プライベート クラウドとオンプレミスの VMware ワークロードで、JetStream DR をデプロイして使用する方法について説明します。


## <a name="supported-scenarios"></a>サポートされるシナリオ
必要な保護サービスと保護されるプライベート クラウドの種類に応じて、2 つの方法で JetStream DR をデプロイできます。

- オンプレミスからクラウド

- クラウド間

### <a name="on-premises-to-cloud-deployment"></a>オンプレミスからクラウドへのデプロイ
組織の VMware ベースのデータ センターで実行されている VM は、継続的に Microsoft Azure にレプリケートされます。 その結果、オンプレミスのデータ センターでインシデントが発生した場合、VM は Azure VMware Solution で運用を再開できます。 VM は回復環境で実行されている間、継続的な保護のために、データをレプリケートし続けます。 オンプレミスのデータ センターが復元されたら、VM とそのデータ (回復環境で VM によって生成された新しいデータを含む) は、中断することなく元のデータ センターに戻すことができます。

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="オンプレミスから Azure VMware Solution プライベート クラウドへの JetStream のデプロイを示す図。" border="false":::

### <a name="cloud-to-cloud-deployment"></a>クラウド間のデプロイ

この構成では、Azure VMware Solution が、1 つのデータ センターでプライマリ環境をホストします。 それは、VM とデータを、別のデータ センター内の別のプライベート クラウドに継続的にレプリケートすることで、保護します。 インシデントが発生した場合、VM とデータは 2 番目のデータ センターで復旧されます。 この保護は双方向にすることができ、データ センター "A" が "B" を保護し、その逆も同様です。

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Azure VMware Solution のプライベート クラウド間の JetStream のデプロイを示す図。" border="false":::


## <a name="prerequisites"></a>[前提条件]

- セカンダリ リージョンとしてデプロイされた Azure VMware Solution プライベート クラウド。 

- Azure VMware Solution プライベート クラウドへの ExpressRoute 接続がある Ubuntu Linux ジャンプ ボックス。 

- Linux ジャンプ ボックスにインストールされた最新の PowerShell。

- PowerShell ギャラリーの最新のサードパーティ モジュールがインストールされている。

### <a name="protected-site"></a>保護されたサイト

保護されたサイトでは、vCenter、DNS、Active Directory などの管理サービスの **サービス クラスター**、および保護された基幹業務アプリケーションが実行される **コンピューター クラスター** がホストされます。 保護されたサイトは、オンプレミスに配置することも、または Azure VMware Solution でホストすることもできます。

次のいずれかの種類を使用できます。

- Azure Blob Storage

- Azure VMware Solution vSAN

- Azure VMware Solution に接続されたファイル システム (Azure NetApp Files など)


| **Item**  | **説明**  |  
| --- | --- |  
| **vCenter Server**  | <ul><li>サポートされているバージョン: 6.7</li><li>HTTPS ポート: ファイアウォールを使用している場合は、HTTPS ポート 443 が開かれている必要があります。</li><li>接続: JetStream DR 管理サーバー アプライアンスの FQDN に vCenter から到達できる必要があります。 そうでない場合、プラグインのインストールは失敗します。</li><li>時間: vCenter と JetStream DR MSA のクロックを同期させる必要があります。</li></ul>  |
| **Distributed Resource Scheduler (DRS)**  | コンピューティング クラスターでは、リソースの分散が推奨されます。  |
| **クラスター**  | vSphere ホスト: JetStream DR によって保護される VM は、クラスターの一部である必要があります。  |
| **vSphere ホスト**  | <ul><li>サポートされているバージョン: 6.7U1 (ビルド #10302608) 以降</li><li>接続: vCenter Server FQDN にホストから到達できる必要があります。 そうでない場合、ホストの構成は失敗します。</li><li>時間: vSphere ホストと JetStream DR MSA のクロックを同期させる必要があります。</li><li>CIM サービス: CIM サーバーを有効にする必要があります。これは既定の設定です。</li></ul>  |
| **JetStream DR MSA**  | <ul><li>CPU: 64 ビット、4 vCPU</li><li>メモリ:4 GB</li><li>ディスク領域: 60 GB</li><li>ネットワーク: 静的または動的に割り当てられた (DHCP) IP アドレスを使用できます。 FQDN が DNS に登録されている必要があります。</li><li>DNS: vSphere ホストと vCenter Server の DNS 名前解決</li></ul>  |
| **JetStream DRVA**  | <ul><li>CPU: 4 コア</li><li>メモリ:8 GB</li><li>ネットワーク: 静的または動的に割り当てられた (DHCP) IP アドレスを使用できます。</li></ul>  |
| **レプリケーション ログ ストア**  | 最適なパフォーマンスを得るために、保護されたサイトでは、クラスターで共有をホストする低遅延のフラッシュ ストレージ デバイスを公開する必要があります。 このデバイスは、JetStream DR ソフトウェアによって制御するか、サード パーティによって提供されます。 これは、レプリケーション ログのリポジトリとして使用されます。 DRVA ホストと ESXi ホストでは、iSCSI を介してこのストレージに直接アクセスできる必要があります。  |
| **ポート**  | JetStream DR ソフトウェアがインストールされている場合、ソース ESXi ホストで特定の範囲のポートが自動的に開かれます。 そのため、ほとんどのユーザーにとって、追加のアクションは必要ありません。 ただし、オンプレミス/ソースの設定でこれらのポートをブロックする特別なファイアウォール規則がある場合は、これらのポートを手動で開く必要があります。<br /><br />ポート範囲: 32873 から 32878  |



### <a name="recovery-site"></a>回復サイト

フェールオーバー回復用に、Azure VMware Solution _パイロット ライト_ クラスターが確立されています。 回復サイトはインストール プロセスの一部として作成されますが、回復サイト クラスターは、通常の運用時に完全に設定または使用されません。 障害イベントに応じて、フェール オーバー コンピューティング クラスターがオンデマンドで回復サイトに追加されます。

### <a name="network"></a>ネットワーク

保護されたサイトと回復サイトの間に、次の特性を持つネットワークが確立されている必要があります。

| **項目** | **説明** |
| --- | --- |
| **JetStream DR MSA**  | MSA には管理ネットワークが必要です。 このネットワークは、JetStream DR RESTful API にアクセスし、他のデータ パス呼び出しを行うために使用されます。 オブジェクト ストアへの接続にプライベート ネットワークを使用できる場合は、このプライベート ネットワークを別のネットワークとして MSA VM に追加する必要があります。 プライベート ネットワークを使用できない場合は、管理ネットワークを使用してオブジェクトストアに接続できることを確認してください。 <br /><br />オブジェクト ストアへのアクセスには専用の外部ネットワークを使用できます。それ以外の場合、データ トラフィックは管理ネットワークを介して送信されます。  | 
| **JetStream DRVA**  | 使用される唯一のネットワークが管理ネットワークの場合は、それによって、IO フィルターとオブジェクト ストアの両方にアクセスできることを確認します。 クラスター内に複数のネットワークが存在する場合は、すべてが DRVA VM に接続されている必要があります。  | 
| **Object Cloud Virtual Appliance (RocVA) からの回復**  | 使用される唯一のネットワークが管理ネットワークの場合は、それによって、ESXi ホストとオブジェクト ストアの両方にアクセスできることを確認します。 クラスター内に複数のネットワークが存在する場合は、すべてが RocVA VM に接続されている必要があります。 RocVA は、VM の回復に必要なときに自動的に作成され、不要になったときに削除される一時的な VM です。  | 
| **オブジェクトストア/BLOB ストレージ**  | オブジェクト ストア/Blob Storage には、保護されたサイトと回復サイトの両方からアクセスできる必要があります。  | 
| **レプリケーション ログ ストア**  | DRVA および ESXi ホストは、iSCSI 経由でこのストレージに直接アクセスできる必要があります。  | 


## <a name="install-jetstream-dr"></a>JetStream DR のインストール

JetStream DR のインストールは、Azure VMware Solution ポータルの実行コマンド機能を使用して実行できます。 次の 3 つの手順でインストールを実行します。

:::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="使用可能な JetStream 実行コマンドにアクセスする方法を示すスクリーンショット。" lightbox="media/run-command/run-command-overview-jetstream.png":::


>[!NOTE]
>実行コマンドは、送信された順序で一度に 1 つずつ実行されます。

### <a name="check-the-current-state-of-the-system"></a>システムの現在の状態を確認する

`Invoke-PreflightJetDRSystemCheck` コマンドレットを実行して、システムの状態と、スクリプトの最小要件を満たしているかどうかを確認します。 さらに、その他のコマンドレットを実行するために必要な vCenter 構成も確認します。  

コマンドレットによって、次が確認されます。

- PowerShell
- vCenter FQDN
- CloudAdmin ロール
- VMware モジュール

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Invoke-PreflightJetDRSystemCheck]** の順に選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 です。  |
   | **実行の名前を指定**  | **checkDRsystem** などの英数字の名前。  |
   | **タイムアウト**  | 完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** をチェックします。 



### <a name="check-cluster-configuration"></a>クラスター構成を確認する

`Invoke-PreflightJetDRInstall` コマンドレットを実行して、次のクラスター構成を確認します。

- クラスターの詳細が正しいかどうか 
- 少なくとも 4 つのホスト (最小) がある
- MSA をインストールするために指定された同じ名前の VM があるかどうか
- VCenter に **jetdr** プラグインが存在するかどうか


1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Invoke-PreflightJetDRInstall]** の順に選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **VMName** | MSA VM の名前。 たとえば、**jetstreamServer** などです。 |
   | **クラスター** | MSA がデプロイされるクラスターの名前。 たとえば、**Cluster-1** などです。 |
   | **ProtectedCluster** | 保護するクラスター。 たとえば、**Cluster-1** などです。 |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 です。  |
   | **実行の名前を指定**  | 英数字名。たとえば、**check_jetserverdetails** などです。  |
   | **タイムアウト**  | 完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** をチェックします。  

1. エラーが報告された場合は、次の手順に進んで、JetDR MSA をデプロイできます。


### <a name="deploy-jetdr-msa"></a>JetDR MSA をデプロイする

`Install-JetDR` コマンドレットを実行して、JetDR MSA をデプロイし、vCenter を JetDR MSA に登録して、クラスターを構成します。 デプロイによって、Microsoft Server Media (MMS) から JetDR バンドルをダウンロードし、管理者特権が割り当てられた新しいユーザーを作成します。 

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Install-JetDR]** の順に選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **Network** | デプロイする MSA のネットワーク マッピング。 たとえば、**VM Network** などです。  |
   | **HostName** | デプロイする MSA のホスト名 (FQDN)。  |
   | **資格情報** | デプロイする MSA のルート ユーザーの資格情報。   |
   | **ゲートウェイ** | デプロイする MSA のゲートウェイ。 DHCP の場合は空白のままにします。  |
   | **Dns** | MSA で使用する必要がある DNS IP。 DHCP の場合は空白のままにします。  |
   | **MSIp** | デプロイする MSA の IP アドレス。 DHCP の場合は空白のままにします。  |
   | **ネットマスク** | デプロイする MSA のネットマスク。 DHCP の場合は空白のままにします。  |
   | **クラスター** | MSA がデプロイされるクラスターの名前。 たとえば、**Cluster-1** などです。 |
   | **VMName** | MSA VM の名前。 たとえば、**jetstreamServer** などです。 |
   | **データストア** | MSA がデプロイされるデータストア。  |
   | **ProtectedCluster** | 保護するクラスター。 たとえば、**Cluster-1** などです。 |
   | **RegisterWithIp** | ホスト名ではなく Ip で MSA を登録します。 <ul><li>MSA のホスト名が DNS に登録されていない場合は True。</li><li>MSA のホスト名が DNS に登録されている場合は False。 </li></ul> |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 です。  |
   | **実行の名前を指定**  | 英数字名。たとえば、**check_jetserverdetails** などです。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** をチェックします。  


## <a name="uninstall-jetstream-dr"></a>JetStream DR のアンインストール

JetStream DR は 2 つの手順でアンインストールします。 


### <a name="check-current-state-of-the-jetstream-appliance"></a>JetStream アプライアンスの現在の状態を確認する

`Invoke-PreflightJetDRUninstall` コマンドレットを実行して、既存の MSA VM とクラスター構成を診断します。 それによって、JetStream DR アプライアンスの現在の状態と、スクリプトの最小要件が満たされているかどうかが確認されます。

- クラスターの詳細が正しいかどうか 
- 少なくとも 4 つのホスト (最小) がある
- VCenter が MSA に登録されているかどうか

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Invoke-PreflightJetDRUninstall]** の順に選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **MSIp** | MSA VM の IP アドレス。  |
   | **資格情報** | MSA VM のルート ユーザーの資格情報。 インストール時に指定したものと同じである必要があります。  |
   | **ProtectedCluster** | 保護されるクラスターの名前。たとえば、**Cluster-1** などです。  インストール時に指定したクラスターである必要があります。 |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 です。  |
   | **実行の名前を指定**  | 英数字名。たとえば、**uninstallcheck_jetserverdetails** などです。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** をチェックします。  




### <a name="uninstall-jetdr"></a>JetDR のアンインストール

JetStream DR とそのコンポーネントをアンインストールするには、`Uninstall-JetDR` コマンドレットを実行します。  それによって、クラスターの構成が解除され、MSA から vCenter の登録が解除され、さらにユーザーが削除されます。

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Uninstall-JetDR]** の順に選択します。

1. 必要な値を入力するか、既定値を変更してから、 **[実行]** を選択します。

   | **フィールド** | **Value** |
   | --- | --- |
   | **MSIp** | MSA VM の IP アドレス。  |
   | **資格情報** | MSA VM のルート ユーザーの資格情報。 インストール時に指定したものと同じである必要があります。   |
   | **ProtectedCluster** | 保護されるクラスターの名前。たとえば、**Cluster-1** などです。  インストール時に指定したクラスターである必要があります。 |
   | **最大保有期間**  | コマンドレット出力の保有期間。 既定値は 60 です。  |
   | **実行の名前を指定**  | 英数字名。たとえば、**uninstallcheck_jetserverdetails** などです。  |
   | **タイムアウト**  |  完了までの時間がかかりすぎる場合に、コマンドレットを終了するまでの期間。  |

1. 進行状況を確認するには、 **[通知]** をチェックします。  


## <a name="next-steps"></a>次のステップ

- [JetStream DR for Azure VMware Solution - 完全なデモ](https://vimeo.com/475620858/2ce9413248)
   
   - [JetStream DR for Azure VMware Solution の概要](https://vimeo.com/491880696/ec509ff8e3)

   - [VM の構成と保護](https://vimeo.com/491881616/d887590fb2)

   - [Azure VMware Solution へのフェールオーバー](https://vimeo.com/491883564/ca9fc57092)

   - [オンプレミスにフェールバックする](https://vimeo.com/491884402/65ee817b60)

- [JetStream DR for Azure VMware Solution インフラストラクチャ セットアップ](https://vimeo.com/480574312/b5386a871c) (*技術的な詳細、音声トラックなし*)
