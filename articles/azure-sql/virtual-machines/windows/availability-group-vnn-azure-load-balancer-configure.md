---
title: AG VNN リスナーのロード バランサーの構成
description: 高可用性とディザスター リカバリー (HADR) を実現するために、Azure Load Balancer を構成して、Azure VM 上の SQL Server を使用する可用性グループの仮想ネットワーク名 (VNN) リスナーにトラフィックをルーティングする方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 7c749744ef6dcad4ca8f233b1a85e07843ba0406
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158476"
---
# <a name="configure-load-balancer-for-ag-vnn-listener"></a>AG VNN リスナーのロード バランサーの構成
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 同じ Azure 仮想ネットワーク内の[複数のサブネット](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)に SQL Server VM を作成することで、Always On 可用性グループ (AG) に対して Azure Load Balancer が不要になります。

Azure 仮想マシンでは、一度に 1 つのクラスター ノードに設定する必要がある IP アドレスを保持するために、クラスターでロード バランサーが使用されます。 このソリューションでは、SQL Server VM が 1 つのサブネット内にある場合に、Always On 可用性グループ (AG) の仮想ネットワーク名 (VNN) リスナーの IP アドレスが、ロード バランサーによって保持されます。 

この記事では、Azure Load Balancer サービスを使用してロード バランサーを構成する方法について説明します。 高可用性とディザスター リカバリー (HADR) を実現するために、ロード バランサーによって、Azure VM 上の SQL Server を使用する[可用性グループ (AG) リスナー](availability-group-overview.md)にトラフィックがルーティングされます。 

SQL Server 2019 CU8 以降を使用している顧客向けの代替の接続オプションとしては、構成を簡素化し、フェールオーバーを改善するために、代わりに [DNN リスナー](availability-group-vnn-azure-load-balancer-configure.md)を検討してください。  



## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

- Azure Load Balancer が[お客様の可用性グループに適切な接続オプション](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn)であると判断済みであること。
- [可用性グループ リスナー](availability-group-overview.md)が構成済みであること。
- 最新バージョンの [PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows) をインストール済みであること。 


## <a name="create-load-balancer"></a>ロード バランサーの作成

内部ロード バランサーまたは外部ロード バランサーを作成することができます。 内部ロード バランサーは、ネットワークの内部にあるプライベート リソースからのみアクセスできます。  外部ロード バランサーは、パブリック リソースから内部リソースへとトラフィックをルーティングすることができます。 内部ロード バランサーを構成するときは、負荷分散規則を構成する際のフロントエンド IP に、可用性グループ リスナー リソースと同じ IP アドレスを使用します。 外部ロード バランサーを構成するときは、可用性グループ リスナーと同じ IP アドレスは使用できません。リスナーの IP アドレスをパブリック IP アドレスにすることはできないためです。 したがって外部ロード バランサーを使用するには、可用性グループと同じサブネット内の IP アドレスのうち、他の IP アドレスと競合しないアドレスを論理的に割り当て、そのアドレスを負荷分散規則のフロントエンド IP アドレスとして使用します。 

[Azure portal](https://portal.azure.com) を使用してロード バランサーを作成します。

1. Azure Portal で、仮想マシンが含まれているリソース グループに移動します。

1. **[追加]** を選択します。 Azure Marketplace で「**ロード バランサー**」を検索します。 **[ロード バランサー]** を選択します。

1. **［作成］** を選択します

1. 次の値を使用してロード バランサーを設定します。

   - **サブスクリプション**:Azure サブスクリプション。
   - **[リソース グループ]** :仮想マシンが含まれているリソース グループ。
   - **Name**:ロード バランサーを識別する名前。
   - **[リージョン]** :仮想マシンが含まれている Azure の場所。
   - **[種類]** :パブリックまたはプライベート。 プライベート ロード バランサーには、同じ仮想ネットワーク内からアクセスできます。 プライベート ロード バランサーは、ほとんどの Azure アプリケーションで使用できます。 アプリケーションがインターネット経由で直接 SQL Server にアクセスする必要がある場合は、パブリック ロード バランサーを使用します。
   - **SKU**:Standard。
   - **仮想ネットワーク**:仮想マシンと同じネットワーク。
   - **[IP アドレスの割り当て]** :静的。 
   - **[プライベート IP アドレス]** :クラスター化されたネットワーク リソースに割り当てた IP アドレス。

   次の画像は **[ロード バランサーの作成]** の UI を示しています。

   ![ロードバランサーを設定する](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>バックエンド プールを構成する

1. 仮想マシンが含まれている Azure リソース グループに戻り、新しいロード バランサーを探します。 リソース グループの表示を更新することが必要な場合もあります。 ロード バランサーを選択します。

1. **[バックエンド プール]** を選択し、 **[追加]** を選択します。

1. VM を含む可用性セットにバックエンド プールを関連付けます。

1. **[ターゲット ネットワーク IP 構成]** で、 **[仮想マシン]** を選択し、クラスター ノードとして参加する仮想マシンを選びます。 必ず、可用性グループをホストするすべての仮想マシンを含めます。

1. **[OK]** を選択して、バックエンド プールを作成します。

## <a name="configure-health-probe"></a>正常性プローブを構成する

1. [ロード バランサー] ペインで **[正常性プローブ]** を選択します。

1. **[追加]** を選択します。

1. **[正常性プローブの追加]** ペインで、<span id="probe"></span> 次の正常性プローブのパラメーターを設定します。

   - **Name**:正常性プローブの名前。
   - **プロトコル**:TCP
   - **ポート**:[VM を準備したときに](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1)正常性プローブ用にファイアウォールで作成したポート。 この記事の例では、TCP ポート `59999` を使用します。
   - **間隔**: 5 秒
   - **[異常のしきい値]** : 連続エラー数 2。

1. **[OK]** を選択します。

## <a name="set-load-balancing-rules"></a>負荷分散規則を設定する

ロード バランサーの負荷分散規則を設定します。 

# <a name="private-load-balancer"></a>[プライベート ロード バランサー](#tab/ilb)

1. [ロード バランサー] ペインで、 **[負荷分散規則]** を選択します。

1. **[追加]** を選択します。

1. 次のように負荷分散規則のパラメーターを設定します。

   - **Name**:負荷分散規則の名前。
   - **フロントエンド IP アドレス**: AG リスナーのクラスター化されたネットワーク リソースの IP アドレス。
   - **ポート**:SQL Server の TCP ポート。 既定のインスタンス ポートは 1433 です。
   - **[バックエンド ポート]** : **[フローティング IP (ダイレクト サーバー リターン)]** を有効にしたときの **[ポート]** の値と同じポート。
   - **[バックエンド プール]** : 先ほど構成したバックエンド プール名。
   - **[正常性プローブ]** : 先ほど構成した正常性プローブ。
   - **[セッション永続化]** : [なし] :
   - **[アイドル タイムアウト (分)]** : 4.
   - **[フローティング IP]\(ダイレクト サーバー リターン\)** : 有効。

1. **[OK]** を選択します。

# <a name="public-load-balancer"></a>[パブリック ロード バランサー](#tab/elb)

1. [ロード バランサー] ペインで、 **[負荷分散規則]** を選択します。

1. **[追加]** を選択します。

1. 次のように負荷分散規則のパラメーターを設定します。

   - **Name**:負荷分散規則の名前。
   - **フロントエンド IP アドレス**: クライアントがパブリック エンドポイントへの接続に使用するパブリック IP アドレス。 
   - **ポート**:SQL Server の TCP ポート。 既定のインスタンス ポートは 1433 です。
   - **バックエンド ポート**: AG のリスナーによって使用される同じポート。 既定のポートは 1433 です。 
   - **[バックエンド プール]** : 先ほど構成したバックエンド プール名。
   - **[正常性プローブ]** : 先ほど構成した正常性プローブ。
   - **[セッション永続化]** : [なし] :
   - **[アイドル タイムアウト (分)]** : 4.
   - **[フローティング IP (ダイレクト サーバー リターン)]** : 無効にします。

1. **[OK]** を選択します。

---

## <a name="configure-cluster-probe"></a>クラスターのプローブを構成する

PowerShell でクラスターのプローブ ポート パラメーターを設定します。

# <a name="private-load-balancer"></a>[プライベート ロード バランサー](#tab/ilb)

クラスターのプローブ ポート パラメーターを設定するには、使用環境の値を使用して、次のスクリプトで変数を更新します。 スクリプトから山かっこ (`<` および `>`) を削除します。

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<Availability group Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

次の表では、更新する必要がある値について説明します。


|**Value**|**説明**|
|---------|---------|
|`Cluster Network Name`| ネットワークの Windows Server フェールオーバー クラスターの名前。 **[フェールオーバー クラスター マネージャー]**  >  **[ネットワーク]** で、ネットワークを右クリックして **[プロパティ]** を選択します。 正しい値は **[全般]** タブの **[名前]** にあります。|
|`AG listener IP Address Resource Name`|AG リスナーの IP アドレスのリソース名。 **[フェールオーバー クラスター マネージャー]**  >  **[Roles]\(ロール\)** で、可用性グループ ロールの **[サーバー名]** の下にある IP アドレス リソースを右クリックして **[プロパティ]** を選択します。 正しい値は **[全般]** タブの **[名前]** にあります。|
|`ILBIP`|内部ロード バランサー (ILB) の IP アドレス。 このアドレスは、Azure portal で ILB のフロントエンド アドレスとして構成されます。  これは可用性グループ リスナーと同じ IP アドレスです。 **[フェールオーバー クラスター マネージャー]** の `<AG listener IP Address Resource Name>` がある同じプロパティ ページで見つけることができます。|
|`nnnnn`|ロード バランサーの正常性プローブで構成したプローブ ポート。 未使用の TCP ポートが有効です。|
|"SubnetMask"| クラスター パラメーターのサブネット マスク。 TCP IP のブロードキャスト アドレス `255.255.255.255` である必要があります。| 


クラスターのプローブを設定したら、PowerShell ですべてのクラスター パラメーターを確認できます。 このスクリプトを実行します。

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```

# <a name="public-load-balancer"></a>[パブリック ロード バランサー](#tab/elb)

クラスターのプローブ ポート パラメーターを設定するには、使用環境の値を使用して、次のスクリプトで変数を更新します。 スクリプトから山かっこ (`<` および `>`) を削除します。

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<Availability group Listener IP Address Resource Name>" 
$ELBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ELBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

次の表では、更新する必要がある値について説明します。


|**Value**|**説明**|
|---------|---------|
|`Cluster Network Name`| ネットワークの Windows Server フェールオーバー クラスターの名前。 **[フェールオーバー クラスター マネージャー]**  >  **[ネットワーク]** で、ネットワークを右クリックして **[プロパティ]** を選択します。 正しい値は **[全般]** タブの **[名前]** にあります。|
|`AG listener IP Address Resource Name`|AG リスナーの IP アドレスのリソース名。 **[フェールオーバー クラスター マネージャー]**  >  **[ロール]** で、可用性グループ ロールの **[サーバー名]** の下にある IP アドレス リソースを右クリックして、 **[プロパティ]** を選択します。 正しい値は **[全般]** タブの **[名前]** にあります。|
|`ELBIP`|外部ロード バランサー (ELB) の IP アドレス。 このアドレスは、Azure portal で ELB のフロントエンド アドレスとして構成され、外部リソースからパブリック ロード バランサーへの接続に使用されます。|
|`nnnnn`|ロード バランサーの正常性プローブで構成したプローブ ポート。 未使用の TCP ポートが有効です。|
|"SubnetMask"| クラスター パラメーターのサブネット マスク。 TCP IP のブロードキャスト アドレス `255.255.255.255` である必要があります。| 


クラスターのプローブを設定したら、PowerShell ですべてのクラスター パラメーターを確認できます。 このスクリプトを実行します。

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```

> [!NOTE]
> 外部ロード バランサーにはプライベート IP アドレスがないため、サブネット内の IP アドレスを解決する VNN DNS 名をユーザーが直接使用することはできません。 パブリック LB のパブリック IP アドレスを使用するか、または DNS サーバーに対する別の DNS マッピングを構成してください。 


---

## <a name="modify-connection-string"></a>接続文字列を変更する 

それをサポートするクライアントの場合は、`MultiSubnetFailover=True` を接続文字列に追加します。 MultiSubnetFailover 接続オプションは必須ではありませんが、サブネットのフェールオーバーが速くなるという利点があります。 これは、クライアント ドライバーが、各 IP アドレスの TCP ソケットを同時に開こうとするためです。 クライアント ドライバーは、最初の IP が正常に応答するのを待ち、応答した場合は、その IP を接続に使用します。

ご自身のクライアントで MultiSubnetFailover パラメーターがサポートされていない場合は、RegisterAllProvidersIP と HostRecordTTL の設定を変更して、フェールオーバー後の接続の遅延を防ぐことができます。 

PowerShell を使用して、RegisterAllProvidersIp と HostRecordTTL の設定を変更します。 

```powershell
Get-ClusterResource yourListenerName | Set-ClusterParameter RegisterAllProvidersIP 0  
Get-ClusterResource yourListenerName|Set-ClusterParameter HostRecordTTL 300 
```

詳細については、SQL Server の[リスナーの接続タイムアウト](/troubleshoot/sql/availability-groups/listener-connection-times-out)に関するドキュメントを参照してください。 


> [!TIP]
> - 1 つのサブネットの HADR ソリューションでも、接続文字列内で MultiSubnetFailover パラメータ = true を設定することで、今後、接続文字列を変更することなく、複数のサブネットにまたがることができます。  
> - 既定では、クライアントは 20 分間、クラスター DNS レコードをキャッシュします。 HostRecordTTL を小さくすると、キャッシュするレコードの Time to Live (TTL) が短くなり、レガシ クライアントがより迅速に再接続できるようになります。 このため、HostRecordTTL の設定を小さくすると、DNS サーバーへのトラフィックが増加する可能性があります。

## <a name="test-failover"></a>[テスト フェールオーバー]

クラスター化されたリソースのフェールオーバーをテストして、クラスターの機能を検証します。 

次の手順を実行します。

1. [SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) を開き、可用性グループ リスナーに接続します。 
1. **オブジェクト エクスプローラー** で **[Always On 可用性グループ]** を展開します。 
1. 可用性グループを右クリックして、 **[フェールオーバー]** を選択します。 
1. ウィザードの指示に従って、可用性グループをセカンダリ レプリカにフェールオーバーします。 

レプリカのロールが切り替わり、両方が同期された場合、フェールオーバーは成功です。 


## <a name="test-connectivity"></a>接続をテストする

接続をテストするには、同じ仮想ネットワーク内の別の仮想マシンにサインインします。 **SQL Server Management Studio** を開き、可用性グループ リスナーに接続します。

>[!NOTE]
>必要に応じて、[SQL Server Management Studio をダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)できます。

## <a name="next-steps"></a>次のステップ

VNN が作成されたら、[SQL Server VM のクラスター設定](hadr-cluster-best-practices.md)を最適化することを検討してください。 

詳細については、以下をご覧ください。

- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [AlwaysOn 可用性グループと Azure VM 上の SQL Server](availability-group-overview.md)
- [AlwaysOn 可用性グループの概要](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM 上の SQL Server に対する HADR 設定](hadr-cluster-best-practices.md)



