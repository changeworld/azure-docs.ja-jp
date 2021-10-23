---
title: クラスター クォーラムを構成する
description: 'Azure VM 上の SQL Server で Windows Server フェールオーバー クラスター用のクォーラムとして、ディスク監視、クラウド監視、またはファイル共有監視を構成する方法について学習します。 '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 575e8a5ab43e38b723bd0b2084709a1216b477fe
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163426"
---
# <a name="configure-cluster-quorum-for-sql-server-on-azure-vms"></a>Azure VM 上の SQL Server 用にクラスター クォーラムを構成する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure Virtual Machines (VM) 上の SQL Server で実行されている Windows Server フェールオーバー クラスター用の 3 つのクォーラム オプション (ディスク監視、クラウド監視、ファイル共有監視) のいずれかを構成する方法について説明します。


## <a name="overview"></a>概要

クラスターのクォーラムは、クラスターが正常に開始または動作し続けるためにアクティブなクラスター メンバーシップの一部である必要がある投票要素の数によって決まります。 クォーラム リソースを構成することで、1 つのノードのみをオンラインにして 2 つのノード クラスターを続行できます。 Windows Server フェールオーバー クラスターは、Azure VM 上の SQL Server の高可用性オプション ([フェールオーバー クラスター インスタンス (FCI)](failover-cluster-instance-overview.md) と[可用性グループ (AG)](availability-group-overview.md)) の基礎となるテクノロジです。 

ディスク監視は最も回復性の高いクォーラム オプションですが、Azure VM 上の SQL Server でディスク監視を使用するには、高可用性ソリューションにいくつかの制限を課す Azure 共有ディスクを使用する必要があります。 そのため、Azure 共有ディスクを使用してフェールオーバー クラスター インスタンスを構成する場合は、ディスク監視を使用します。それ以外の場合は、可能な限りクラウド監視を使用します。 クラウド監視をサポートしていない Windows Server 2012 R2 以前を使用している場合は、ファイル共有監視を使用できます。 

次のクォーラム オプションは、Azure VM 上の SQL Server に使用できます。 

|  |[クラウド監視](/windows-server/failover-clustering/deploy-cloud-witness) |[ディスク監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[ファイル共有監視](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**サポートされる OS**| Windows Server 2016 以降 |All | All|

クォーラムの詳細については、[Windows Server フェールオーバー クラスターの概要](hadr-windows-server-failover-cluster-overview.md)に関するページを参照してください。 

## <a name="cloud-witness"></a>クラウド監視

クラウド監視はフェールオーバー クラスターのクォーラム監視の一種であり、Microsoft Azure Storage を使用してクラスター クォーラムでの投票が提供されます。 


次の表に、クラウド監視に関する追加情報と考慮事項を示します。 

| 監視の種類  | 説明  | 要件と推奨事項  |
| ---------    |---------        |---------                        |
| クラウド監視     |  <ul><li> クラウド監視として Azure Storage が使用され、タイム スタンプのみが含まれます。 </li><li> 複数のサイト、複数のゾーン、複数のリージョンでのデプロイに最適です。</li> <li> Microsoft ストレージ アカウントの下に既知の `msft-cloud-witness` コンテナーが作成されます。 </li> <li> コンテナーの下に 1 つの BLOB ファイルが書き込まれます。この BLOB ファイルの名前には、対応するクラスターの一意の ID が使用されます </li>      |  <ul><li>既定サイズは 1 MB です。</li><li> アカウントの種類には **[General Purpose]** を使用します。 BLOB ストレージはサポートされていません。 </li><li> Standard Storage を使用します。 Azure Premium Storage はサポートされていません。 </li><li> フェールオーバー クラスタリングでは、アービトレーション ポイントとして BLOB ファイルを使用します。ここでは、データの読み取り時に一定の整合性が保証される必要があります。 したがって、 **[レプリケーション]** の種類には **[ローカル冗長ストレージ]** を選択する必要があります。</li><li> バックアップおよびウイルス対策スキャンから除外する必要があります。</li><li> 記憶域スペース ダイレクトでは、ディスク監視はサポートされていません</li> <li> クラウド監視では、HTTPS (既定のポートは 443) を使用して Azure BLOB サービスとの通信を確立します。 ネットワーク プロキシ経由で HTTPS ポートにアクセスできることを確認してください。 </li>|

フェールオーバー クラスター用にクラウド監視クォーラム リソースを構成する場合は、次の点を考慮してください。
- フェールオーバー クラスターでは、アクセス キーを格納する代わりに、Shared Access Security (SAS) トークンが生成され、安全に格納されます。
- 生成された SAS トークンは、アクセス キーが有効なままである限り有効です。 プライマリ アクセス キーをローテーションする場合は、プライマリ アクセス キーを再生成する前に、まず (そのストレージ アカウントを使用しているすべてのクラスターでの) クラウド監視をセカンダリ アクセス キーで更新することが重要です。
- クラウド監視では、Azure ストレージ アカウント サービスの HTTPS REST インターフェイス使用します。 つまり、すべてのクラスター ノードで HTTPS ポートを開く必要があります。


クラウド監視には Azure ストレージ アカウントが必要です。 ストレージ アカウントを構成するには、これらの手順に従います。 

1. [Azure portal](https://portal.azure.com) にサインインします。
2. [ハブ] メニューで、[新規]、[データ + ストレージ]、[ストレージ アカウント] の順に選択します。
3. [ストレージ アカウントを作成する] ページで、次の手順を実行します。
    1. ストレージ アカウントの名前を入力します。 ストレージ アカウント名の長さは 3 から 24 文字である必要があり、数字と小文字のみを使用できます。 ストレージ アカウント名は、Azure 内で一意である必要もあります。
    2. **[アカウントの種類]** で **[汎用]** を選択します。
    3. **[パフォーマンス]** には **[Standard]** を選択します
    2. **[レプリケーション]** には、 **[ローカル冗長ストレージ (LRS)]** を選択します。


ストレージ アカウントが作成されたら、これらの手順に従って、フェールオーバー クラスター用にクラウド監視クォーラム リソースを構成します。 


# <a name="powershell"></a>[PowerShell](#tab/powershell)

既存の Set-ClusterQuorum PowerShell コマンドには、クラウド監視に対応する新しいパラメーターがあります。

PowerShell コマンドを使用して、[`Set-ClusterQuorum`](/powershell/module/failoverclusters/set-clusterquorum) コマンドレットでクラウド監視を構成できます。

```PowerShell
Set-ClusterQuorum -CloudWitness -AccountName <StorageAccountName> -AccessKey <StorageAccountAccessKey>
```

まれに、別のエンドポイントを使用する必要がある場合は、この PowerShell コマンドを使用します。 

```PowerShell
Set-ClusterQuorum -CloudWitness -AccountName <StorageAccountName> -AccessKey <StorageAccountAccessKey> -Endpoint <servername>
```

ストレージ アカウント AccessKey の検索に関するヘルプについては、[クラウド監視のドキュメント](/windows-server/failover-clustering/deploy-cloud-witness)を参照してください。 


# <a name="failover-cluster-manager"></a>[フェールオーバー クラスター マネージャー](#tab/fcm-gui)

クラウド監視を構成するには、フェールオーバー クラスター マネージャーに組み込まれているクォーラム構成ウィザードを使用します。 これを行うには、次のステップに従います。 

1. フェールオーバー クラスター マネージャーを開きます。

2. クラスターを右クリックし、 **[その他のアクション]**  ->  **[クラスター クォーラム設定の構成]** の順にクリックします。 これにより、クラスター クォーラムの構成ウィザードが起動します。

    ![フェールオーバー クラスター マネージャー UI の [クラスター クォーラム設定の構成] へのメニュー パスのスナップショット](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-7.png)
    
3. **[クォーラム構成の選択]** ページで、 **[クォーラム監視を選択する]** を選択します。

    ![クラスター クォーラム ウィザードの [クォーラム監視を選択する] ラジオ ボタンのスナップショット](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-8.png)
   
4. **[クォーラム監視の選択]** ページで、 **[クラウド監視を構成する]** を選択します。

    ![クラウド監視を選択するための適切なオプション ボタンのスナップショット](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-9.png)
    
5. **[クラウド監視を構成する]** ページで、Azure ストレージ アカウント情報を入力します。 この情報の検索に関するヘルプについては、[クラウド監視のドキュメント](/windows-server/failover-clustering/deploy-cloud-witness)を参照してください。 
   1. (必須パラメーター) Azureストレージ アカウント名。
   2. (必須パラメーター) ストレージ アカウントに対応するアクセス キー。
       1. 初めて作成する場合は、プライマリ アクセス キーを使用します 
       2. プライマリ アクセス キーをローテーションする場合は、セカンダリ アクセス キーを使用します
   3. (省略可能なパラメーター) 別の Azure サービス エンドポイント (たとえば、中国の Microsoft Azure サービス) を使用する場合は、エンドポイント サーバー名を更新します。

    ![クラスター クォーラム ウィザードのクラウド監視構成ウィンドウのスナップショット](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-10.png)
      

6. クラウド監視が正常に構成されると、フェールオーバー クラスター マネージャー スナップインで新しく作成された監視リソースを確認できます。

    ![クラウド監視の正常な構成](./media/hadr-create-quorum-windows-failover-cluster-how-to/cloud-witness-11.png)
    


---


## <a name="disk-witness"></a>ディスク監視

ディスク監視は、クラスターの使用可能記憶域グループ内にある小規模なクラスター化されたディスクです。 このディスクは可用性が高く、ノード間でフェールオーバーできます。 

ディスク監視は、Azure 共有ディスクを使用するフェールオーバー クラスター インスタンスなど、共有ストレージの高可用性ソリューションと共に使用する場合に推奨されるクォーラム オプションです。 

次の表に、クォーラム ディスク監視に関する追加情報と考慮事項を示します。 

| 監視の種類  | 説明  | 要件と推奨事項  |
| ---------    |---------        |---------                        |
| ディスク監視     |  <ul><li> クラスター データベースのコピーを格納する専用の LUN です。</li><li> 共有 (レプリケートされない) 記憶域を持つクラスターに最適です。</li>       |  <ul><li>LUN の最小サイズは 512 MB です。</li><li> クラスター専用とし、クラスター化された役割には割り当てないようにする必要があります。</li><li> クラスター化された記憶域に含まれ、記憶域検証テストに合格する必要があります。</li><li> クラスターの共有ボリューム (CSV) ディスクにすることはできません</li><li> 単一のボリュームを持つベーシック ディスクです。</li><li> ドライブ文字を持つ必要はありません</li><li> NTFS または ReFS でフォーマットできます。</li><li> 必要に応じてハードウェア RAID 構成にしてフォールト トレランスを実現できます。</li><li> バックアップおよびウイルス対策スキャンから除外する必要があります。</li><li> 記憶域スペース ダイレクトでは、ディスク監視はサポートされていません</li>|

ディスク監視に Azure 共有ディスクを使用するには、まずそのディスクを作成してマウントする必要があります。 これを行うには、Azure 共有ディスク フェールオーバー クラスター インスタンス ガイドの[ディスクのマウント](failover-cluster-instance-azure-shared-disks-manually-configure.md#add-azure-shared-disk)に関するセクションの手順に従います。 ディスクは Premium である必要はありません。 

ディスクがマウントされた後、次の手順でクラスター記憶域に追加します。 

1. フェールオーバー クラスター マネージャーを開きます。 
1. 左側のナビゲーション ウィンドウで、 **[記憶域]** の下にある **[ディスク]** を選択します。 
1. 右側のナビゲーション ウィンドウで、 **[アクション]** の下にある **[ディスクの追加]** を選択します。 
1. 先ほどマウントした Azure 共有ドライブを選択し、`Cluster Disk 3` などの名前をメモします。 

クラスター化された記憶域としてディスクが追加された後、PowerShell を使用して、それをディスク監視として構成します。  


既存の Set-ClusterQuorum PowerShell コマンドには、クラウド監視に対応する新しいパラメーターがあります。

PowerShell の [`Set-ClusterQuorum`](/powershell/module/failoverclusters/set-clusterquorum) コマンドレットを使用する場合は、ファイル共有のパスをディスク監視のパラメーターとして使用します。

```PowerShell
Set-ClusterQuorum -NodeAndDiskMajority "Cluster Disk 3"
```

フェールオーバー クラスター マネージャーを使用することもできます。クラウド監視の場合と同じ手順に従いますが、代わりにクォーラム オプションとしてディスク監視を選択します。 


## <a name="file-share-witness"></a>ファイル共有監視

ファイル共有監視は、通常 Windows Server を実行しているファイル サーバー上で構成される SMB ファイル共有です。 クラスタリング情報は witness.log ファイルに保持されますが、クラスター データベースのコピーは格納されません。 Azure では、別の仮想マシンでファイル共有を構成することができます。 

ご利用の環境内でディスク監視またはクラウド監視が使用できないか、サポートされていない場合は、ファイル共有監視を構成します。 

次の表に、クォーラム ファイル共有監視に関する追加情報と考慮事項を示します。 

| 監視の種類  | 説明  | 要件と推奨事項  |
| ---------    |---------        |---------                        |
| ファイル共有監視     | <ul><li>Windows Server を実行しているファイル サーバー上で構成される SMB ファイル共有です。</li><li> クラスター データベースのコピーを格納しません。</li><li> クラスター情報を witness.log ファイルにのみ保持します。</li><li> レプリケートされた記憶域を使用するマルチサイト クラスターに最適です。 </li>       |  <ul><li>最低 5 MB の空き容量が必要です。</li><li> 単一のクラスター専用とし、ユーザーまたはアプリケーション データを格納するために使用しないようにする必要があります。</li><li> クラスター名のコンピューター オブジェクトに対する書き込み権限を有効にする必要があります。</li></ul><br>ファイル共有監視をホストするファイル サーバーに関する追加の考慮事項は次のとおりです。<ul><li>単一のファイル サーバーに複数のクラスター用のファイル共有監視を構成できます。</li><li> ファイル サーバーは、クラスター ワークロードとは別個のサイトに配置する必要があります。 このようにすると、サイト間ネットワーク通信が失われたときにどのクラスター サイトにも継続して動作する機会が平等に与えられます。 ファイル サーバーが同じサイトに存在する場合、そのサイトがプリマリ サイトになり、ファイル共有にアクセスできる唯一のサイトになります。</li><li> ファイル サーバーは、ファイル共有監視を使用するクラスターでホストされていない仮想マシン上でも実行できます。</li><li> 高可用性を保証するために、ファイル サーバーを別個のフェールオーバー クラスター上で構成できます。 </li>      |

ファイル共有を作成し、アクセス許可を正しく構成したら、クラスター化されたノードにファイル共有をマウントします。 Premium ファイル共有のフェールオーバー クラスター インスタンス ハウツー ガイドの[ファイル共有のマウント](failover-cluster-instance-premium-file-share-manually-configure.md)に関するセクションで説明されているのと同じ一般的な手順に従って、ファイル共有をマウントすることができます。 

ファイル共有が正しく構成およびマウントされた後、PowerShell を使用して、クォーラム監視リソースとしてファイル共有を追加します。 

```powershell
Set-ClusterQuorum -FileShareWitness <UNC path to file share> -Credential $(Get-Credential)
```

共有に対する完全な管理者権限を持つ (ファイル共有に対して) ローカルの非管理者アカウント用のアカウントとパスワードを入力するように求められます。  クラスターでは名前とパスワードの暗号化が維持され、だれでもアクセスすることはできません。

フェールオーバー クラスター マネージャーを使用することもできます。クラウド監視の場合と同じ手順に従いますが、代わりにクォーラム オプションとしてファイル共有監視を選択します。 

## <a name="change-quorum-voting"></a>クォーラム投票を変更する


Windows Server フェールオーバー クラスターに参加しているノードのクォーラム投票を変更することができます。 

ノードの投票設定を変更するときは、これらのガイドラインに従ってください。 

| クォーラム投票のガイドライン |
|-|
| 既定では最初は各ノードで投票は行いません。 各ノードでは正当性が明白である場合にのみ投票を行います。|
| 可用性グループのプライマリ レプリカをホストするクラスター ノード、またはフェールオーバー クラスター インスタンスの優先所有者の投票を有効にします。 |
| 自動フェールオーバー所有者の投票を有効にします。 自動フェールオーバーの結果として、プライマリ レプリカまたは FCI をホストする可能性がある各ノードでは投票を行う必要があります。 | 
| 可用性グループに複数のセカンダリ レプリカがある場合は、自動フェールオーバーが設定されているレプリカの投票のみを有効にします。 | 
| セカンダリ ディザスター リカバリー サイトにあるノードの投票を無効にします。 プライマリ サイトに問題がない場合は、セカンダリ サイト内のノードがクラスターをオフラインにするかどうかの判断に影響すべきではありません。 | 
| 投票数を奇数にし、クォーラム投票が 3 つ以上になるようにします。 2 ノード クラスターで必要に応じて、追加の投票のために[クォーラム監視](hadr-cluster-quorum-configure-how-to.md)を追加します。 | 
| フェールオーバー後の投票割り当てを再評価します。 正常なクォーラムをサポートしていないクラスター構成にフェールオーバーすることは避けてください。 |




## <a name="next-steps"></a>次の手順

詳細については、以下をご覧ください。

- [Azure VM 上の SQL Server に対する HADR 設定](hadr-cluster-best-practices.md)
- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [AlwaysOn 可用性グループと Azure VM 上の SQL Server](availability-group-overview.md)
- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM 上の SQL Server を使用したフェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)
- [フェールオーバー クラスター インスタンスの概要](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
