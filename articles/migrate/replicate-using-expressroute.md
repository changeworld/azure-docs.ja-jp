---
title: Azure Migrate Server Migration を使用して ExpressRoute 経由でデータをレプリケートする
description: Azure Migrate Server Migration によるレプリケーションに Azure ExpressRoute を使用します。
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: a1752e1ea264f8abacfee575546f7bdf6b5eb966
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624085"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Azure Migrate: Server Migration を使用して ExpressRoute 経由でデータをレプリケートする

この記事では、サーバーを Azure に移行するときに、Azure ExpressRoute 回線経由でデータをレプリケートするための [Azure Migrate: Server Migration](./migrate-services-overview.md#azure-migrate-server-migration-tool) ツールの構成方法について説明します。

## <a name="understand-azure-expressroute-circuits"></a>Azure ExpressRoute 回線について理解する

ExpressRoute 回線により、オンプレミスのインフラストラクチャは接続プロバイダー経由で Microsoft に接続されます。 プライベート ピアリング、Microsoft ピアリング、またはその両方を使用するように、ExpressRoute 回線を構成できます。 ExpressRoute でのピアリング オプションの詳細については、「[ExpressRoute 回線とピアリング](../expressroute/expressroute-circuit-peerings.md#peeringcompare)」を参照してください。

Azure Migrate: Server Migration ツールを使用すると、オンプレミスのサーバーと他のクラウドのサーバーを Azure Virtual Machines に簡単に移行できます。 このツールを使用すると、移行対象サーバーから Azure サブスクリプションのマネージド ディスクにデータをレプリケートするための、継続的なレプリケーション ストリームが設定されます。 サーバーを移行する準備ができたら、Azure 内のレプリケート済みデータを使用してサーバーを移行します。

オンプレミスのサーバーからレプリケートされたデータが、インターネットまたは ExpressRoute 接続経由で Azure サブスクリプションに送信されるように構成できます。 インターネット経由で送信されるデータには、セキュリティで保護された暗号化接続が使用されます。 移行するサーバーの数が多い場合、レプリケーションに ExpressRoute を使用すれば、ExpressRoute 回線で使用可能なプロビジョニング済みの帯域幅を使用することによって、より効率的に移行できます。

この記事では、次のものを使用してデータをレプリケートする方法について説明します。
> [!div class="checklist"]
>
> * プライベート ピアリングを使用する ExpressRoute 回線。
> * Microsoft のピアリングを使用する ExpressRoute 回線。

## <a name="replicate-data-by-using-an-expressroute-circuit-with-private-peering"></a>ExpressRoute 回線とプライベート ピアリングを使用してデータをレプリケートする

> [!Note]
> この記事では、[VMware 仮想マシンを Azure にエージェントレスで移行する](./tutorial-migrate-vmware.md)ためにプライベート ピアリング回線を使用してレプリケートする方法を示します。 [他のレプリケーション方法](./migrate-services-overview.md#azure-migrate-server-migration-tool)にプライベート エンドポイントのサポートを使用するには、「[プライベート エンドポイントで Azure Migrate を使用する](./how-to-use-azure-migrate-with-private-endpoints.md)」を参照してください。
 
VMware 仮想マシンを Azure に移行するエージェントレス方式では、最初に、Azure Migrate アプライアンスにより、サブスクリプションのストレージ アカウント (キャッシュ ストレージ アカウント) にレプリケーション データがアップロードされます。 次に、Azure Migrate によって、キャッシュ ストレージ アカウントのレプリケート済みデータが、サブスクリプションのレプリカ マネージド ディスクに移動されます。

プライベート ピアリング回線をレプリケーションに使用するには、プライベート エンドポイントを作成し、キャッシュ ストレージ アカウントにアタッチします。 プライベート エンドポイントによって、仮想ネットワークの 1 つ以上のプライベート IP アドレスを使用して、ストレージ アカウントが効率的に VNet に取り込まれます。 プライベート エンドポイントを使用すると、Azure Migrate アプライアンスは ExpressRoute プライベート ピアリングを使用してキャッシュ ストレージ アカウントに接続できます。 その後、プライベート IP アドレスでデータを直接転送できます。 <br/>

![レプリケーション プロセスを示すスクリーンショット。](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
> - レプリケーション データに加えて、Azure Migrate アプライアンスではコントロール プレーン アクティビティのための Azure Migrate サービスとの通信も行われます。 これらのアクティビティには、レプリケーションの調整が含まれます。 Azure Migrate アプライアンスと Azure Migrate サービスの間のコントロール プレーン通信は、Azure Migrate サービスのパブリック エンドポイントで引き続きインターネット経由で行われます。
> - ストレージ アカウントのプライベート エンドポイントは、Azure Migrate アプライアンスが配置されているネットワークからアクセスできる必要があります。
> - キャッシュ ストレージ アカウントの BLOB サービス エンドポイントについての Azure Migrate アプライアンスによる DNS クエリが、キャッシュ ストレージ アカウントにアタッチされたプライベート エンドポイントのプライベート IP アドレスに解決されるように DNS を構成する必要があります。
> - キャッシュ ストレージ アカウントは、パブリック エンドポイントでアクセス可能である必要があります。 Azure Migrate によって、キャッシュ ストレージ アカウントのパブリック エンドポイントを使用して、ストレージ アカウントのデータがレプリカ マネージド ディスクに移動されます。

### <a name="prerequisites"></a>前提条件

プライベート エンドポイントが作成されるリソース グループと仮想ネットワークにおいて、次のアクセス許可が必要です。

使用事例 | アクセス許可
--- | --- 
 プライベート エンドポイントを作成および管理する。 | Microsoft.Network/privateEndpoint/write/action<br/>Microsoft.Network/privateEndpoint/read/action 
|プライベート エンドポイントを仮想ネットワークまたはサブネットに接続します。<br/>このアクセス許可は、プライベート エンドポイントが作成される仮想ネットワークに対して必要です。| Microsoft.Network/virtualNetworks/subnet/join/action <br/> Microsoft.Network/virtualNetworks/join/action
|プライベート エンドポイントをストレージ アカウントにリンクする。 <br/>| Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnectionApproval/action <br/> Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnections/read
|ネットワーク インターフェイスを作成してネットワーク セキュリティ グループに参加させる。 | Microsoft.Network/networkInterfaces/read <br/> Microsoft.Network/networkInterfaces/subnets/write <br/> Microsoft.Network/networkInterfaces/subnets/read<br/> Microsoft.Network/networkSecurityGroups/join/action (省略可能)
プライベート DNS ゾーンを作成および管理する。| プライベート DNS ゾーンの共同作成者ロール <br/> _Or_ <br/> Microsoft.Network/privateDnsZones/A/* <br/> Microsoft.Network/privateDnsZones/write Microsoft.Network/privateDnsZones/read <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/write <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft.Network/virtualNetworks/join/action 

### <a name="identify-the-cache-storage-account"></a>キャッシュ ストレージ アカウントを特定する

 Azure Migrate プロジェクトで (Azure portal のエクスペリエンスを使用して) 仮想マシンのレプリケーションを初めて構成するときに、キャッシュ ストレージ アカウントが自動的に作成されます。 ストレージ アカウントは、Azure Migrate プロジェクトを作成したのと同じサブスクリプションとリソース グループに作成されます。

ストレージ アカウントを作成して見つけるには、次のようにします。

1. Azure portal を使用して、Azure Migrate プロジェクトで 1 つ以上の仮想マシンをレプリケートします。
1. Azure Migrate プロジェクトのリソース グループに移動します。
1. ストレージ アカウント名に **lsa** というプレフィックスを指定して、キャッシュ ストレージ アカウントを見つけます。

   ![リソース グループ ビューを示すスクリーンショット。](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> リソース グループ内に **lsa** というプレフィックスが付いたストレージ アカウントが複数ある場合は、[レプリケーション設定およびターゲット構成] メニューに移動して、プロジェクト内のいずれかのレプリケートされた VM を調べることによって、ストレージ アカウントを確認できます。
>
> ![レプリケーション設定の概要を示すスクリーンショット。](./media/replicate-using-expressroute/storage-account.png)

### <a name="upgrade-the-cache-storage-account-to-general-purpose-v2"></a>キャッシュ ストレージ アカウントを汎用 v2 にアップグレードする

プライベート エンドポイントは、汎用 v2 のストレージ アカウントでのみ作成できます。 キャッシュ ストレージ アカウントが汎用 v2 ストレージ アカウントでない場合は、アップグレードします。

1. ストレージ アカウントに移動します。
1. **[構成]** を選択します。
1. **[アカウントの種類]** の **[アップグレード]** を選択します。
1. **[アップグレードの確認]** で、アカウントの名前を入力します。
1. ページの下部にある **[アップグレード]** を選択します。

   ![ストレージ アカウントのアップグレード方法を示すスクリーンショット。](./media/replicate-using-expressroute/upgrade-storage-account.png) 

### <a name="create-a-private-endpoint-for-the-storage-account"></a>ストレージ アカウントのプライベート エンドポイントを作成する

1. ストレージ アカウントに移動し、左側のメニューから **[ネットワーク]** を選択し、 **[プライベート エンドポイント接続]** タブを選択します。
1. **[+ プライベート エンドポイント]** を選択します。

    1. **[プライベート エンドポイントの作成]** ウィンドウで、 **[サブスクリプション]** と **[リソース グループ]** を選択します。 プライベート エンドポイントの名前を入力し、ストレージ アカウントのリージョンを選択します。
     
       ![プライベート エンドポイント構成ウィンドウを示すスクリーンショット。](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    1. **[リソース]** タブで、ストレージ アカウントが含まれる **[サブスクリプション名]** を入力します。 **[リソースの種類]** として **[Microsoft.Storage/storageAccounts]** を選択します。 **[リソース]** で、汎用 v2 タイプのレプリケーション ストレージ アカウントの名前を入力します。 **[ターゲット サブリソース]** として **[BLOB]** を選択します。
     
       ![ストレージ アカウントのプライベート エンドポイントの設定を示すスクリーンショット。](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    1. **[構成]** タブで、ストレージ アカウントのプライベート エンドポイントの **[仮想ネットワーク]** と **[サブネット]** を選択します。

       > [!Note]
       > この仮想ネットワークは、ExpressRoute ゲートウェイ エンドポイントが含まれているか、ExpressRoute ゲートウェイを持つ仮想ネットワークに接続されている必要があります。

       **[プライベート DNS の統合]** セクションで **[はい]** を選択して、プライベート DNS ゾーンと統合します。 **[はい]** を選択すると、DNS ゾーンが選択した仮想ネットワークにリンクされます。 また、プライベート エンドポイント用に作成された新しい IP と完全修飾ドメイン名 (FQDN) の DNS 解決に必要な DNS レコードが追加されます。 [プライベート DNS ゾーン](../dns/private-dns-overview.md)の詳細を参照してください。

       ![プライベート DNS ゾーンを示すスクリーンショット。](./media/replicate-using-expressroute/private-dns-zone.png)

    1. プライベート エンドポイントに **タグ** を追加することもできます。

    1. 詳細情報の入力が終わったら、 **[確認と作成]** タブを選択します。検証が済んだら、 **[作成]** を選択してプライベート エンドポイントを作成します。

> [!Note]
> プライベート エンドポイントを作成したユーザーがストレージ アカウントの所有者でもある場合、プライベート エンドポイントは自動承認されます。 そうでない場合、所有者がプライベート エンドポイントの使用を承認する必要があります。

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>プライベート DNS ゾーンを作成して DNS レコードを手動で追加する (省略可能)

プライベート エンドポイントを作成するときに、プライベート DNS ゾーンと統合するオプションを選択しなかった場合は、プライベート DNS ゾーンを手動で作成する必要があります。

> [!Note]
> プライベート DNS ゾーンと統合することに対して **[はい]** を選択した場合は、このセクションをスキップできます。

手動でプライベート DNS ゾーンを作成するには:

1. **[プライベート DNS ゾーン]** を選択します。

    ![プライベート DNS ゾーンの作成を示すスクリーンショット。](./media/replicate-using-expressroute/create-private-dns.png)

    1. **[プライベート DNS ゾーン]** ページで、 **[+ 追加]** を選択して新しいゾーンを作成します。
    1. **[プライベート DNS ゾーンの作成]** ページで、必要な詳細情報を入力します。 プライベート DNS ゾーンの名前を「**_privatelink_.blob.core.windows.net**」と入力します。
    1. **[確認と作成]** タブで、DNS ゾーンを確認して作成します。

1. 仮想ネットワークにプライベート DNS ゾーンをリンクします。

    作成したプライベート DNS ゾーンは、プライベート エンドポイントがアタッチされている仮想ネットワークにリンクされている必要があります。

    1. 前の手順で作成したプライベート DNS ゾーンに移動し、ページの左側にある仮想ネットワーク リンクに移動します。 **[+ 追加]** を選択します。
    1. 必須の詳細を入力します。 **[サブスクリプション]** と **[仮想ネットワーク]** の各フィールドには、プライベート エンドポイントがアタッチされている仮想ネットワークについて、対応する詳細を入力する必要があります。 その他のフィールドはそのままでかまいません。

1. 次の手順では、DNS ゾーンに DNS レコードを追加します。 ストレージ アカウントの FQDN のエントリをプライベート DNS ゾーンに追加します。

    1. プライベート DNS ゾーンに移動し、ページの左側にある **[概要]** セクションに移動します。 **[+ レコード]** を選択してレコードの追加を開始します。
    1. **[レコード セットの追加]** ページで、FQDN とプライベート IP のエントリを A タイプのレコードとして追加します。

> [!Important]
> ソース環境からストレージ アカウントのプライベート エンドポイントのプライベート IP アドレスを解決するために、追加の DNS 設定が必要になる場合があります。 必要な DNS 構成については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)」を参照してください。  

### <a name="verify-network-connectivity-to-the-storage-account"></a>ストレージ アカウントへのネットワーク接続の検証

プライベート リンク接続を検証するには、Azure Migrate アプライアンスのホストとなるオンプレミス VM からキャッシュ ストレージ アカウントのリソース エンドポイントの DNS 解決を実行し、プライベート IP アドレスに解決されることを確認します。

キャッシュ ストレージ アカウントの DNS 解決の具体的な例 

- nslookup _storageaccountname_.blob.core.windows.net を入力します。 \<storage-account-name\> を Azure Migrate によって作成されたキャッシュ ストレージ アカウントの名前に置き換えます。  

    このようなメッセージが返されます。  

   ![DNS 解決の例](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- ストレージ アカウントに対応する 10.1.0.5 というプライベート IP アドレスが返されます。 このアドレスは、ストレージ アカウントのプライベート エンドポイントに属している必要があります。 

DNS 解決が正しくない場合は、これらの手順を実行します。  

- **ヒント:** ソース環境の DNS レコードを手動で更新するには、ストレージ アカウントの FQDN リンク _storageaccountname_.blob.core.windows.net および関連付けられているプライベート IP アドレスを使用して、オンプレミス アプライアンス上の DNS ホスト ファイルを編集します。 この方法は、テスト目的でのみ推奨されます。 
- カスタム DNS を使用している場合は、カスタム DNS の設定を確認し、DNS 構成が正しいことを確認します。 ガイダンスについては、[プライベート エンドポイントの概要に関する記事の「DNS の構成」](../private-link/private-endpoint-overview.md#dns-configuration)を参照してください。 
- Azure 提供の DNS サーバーを使用する場合は、このガイドを参考に、[、さらに ](./troubleshoot-network-connectivity.md#validate-the-private-dns-zone) のトラブルシューティングを行います 。   

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-expressroute-private-peering-connectivity"></a>Azure Migrate アプライアンス上でプロキシ バイパス ルールを構成する (ExpressRoute プライベート ピアリング接続の場合) 
プロキシ バイパスの場合は、次のようにキャッシュ ストレージ アカウントのプロキシ バイパス ルールを追加できます。 
- _storageaccountname_.blob.core.windows.net.

> [!Important]
>  Azure Migrate はインターネットへのアクセスを必要とする別のストレージ アカウントを使用するため、*.blob.core.windows.net をバイパスしないでください。 このストレージ アカウント (ゲートウェイ ストレージ アカウント) は、レプリケートされる VM に関する状態情報を格納する目的でのみ使用されます。 ゲートウェイ ストレージ アカウントを見つけるには、Azure Migrate プロジェクトのリソース グループ内のストレージ アカウント名でプレフィックス _**gwsa**_ を探します。 

## <a name="replicate-data-by-using-an-expressroute-circuit-with-microsoft-peering"></a>ExpressRoute 回線を使用して Microsoft ピアリングでデータをレプリケートする

Microsoft ピアリングまたは既存のパブリック ピアリング ドメイン (新しい ExpressRoute 接続では非推奨) を使用して、ExpressRoute 回線経由でレプリケーション トラフィックをルーティングできます。

![Microsoft ピアリングを使用したレプリケーションを示す図。](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

レプリケーション データが Microsoft ピアリング回線を通る場合でも、コントロール プレーン トラフィックと、ExpressRoute 経由ではアクセスできないその他の URL についてはオンプレミス サイトからのインターネット接続が必要になります。 レプリケーション アプライアンスまたは Hyper-V ホストは、レプリケーション プロセスを調整するために URL にアクセスする必要があります。 [VMware エージェントレス移行](./migrate-appliance.md#public-cloud-urls)または[エージェントベース移行](./migrate-replication-appliance.md)の移行シナリオに基づいて、URL の要件を確認します。 

 Microsoft ピアリング経由でレプリケーション データを転送する場合は、Azure Storage エンドポイントのルートをアドバタイズするようにルート フィルターを構成します。 これが、ターゲット Azure リージョンのリージョン BGP コミュニティ (移行用リージョン) となります。 Microsoft ピアリング経由でコントロール プレーン トラフィックをルーティングするには、必要に応じて他のパブリック エンドポイントのルートをアドバタイズするようにルート フィルターを構成します。  

- ソース Azure リージョンのリージョン BGP コミュニティ (Azure Migrate プロジェクト リージョン)
- ターゲット Azure リージョンのリージョン BGP コミュニティ (移行用リージョン)
- Azure Active Directory の BGP コミュニティ (12076:5060)

詳細については、[ルート フィルター](../expressroute/how-to-routefilter-portal.md)と、[ExpressRoute の BGP コミュニティ](../expressroute/expressroute-routing.md#bgp)の一覧に関するページを参照してください。

### <a name="proxy-configuration-for-expressroute-microsoft-peering"></a>ExpressRoute Microsoft ピアリングのためのプロキシ構成

アプライアンスがインターネット接続にプロキシを使用する場合、特定の URL に対してプロキシ バイパスを構成して、それらが Microsoft ピアリング回線経由でルーティングされるようにすることが必要な場合があります。 

#### <a name="configure-proxy-bypass-rules-for-expressroute-microsoft-peering-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Azure Migrate アプライアンス上で ExpressRoute Microsoft ピアリングのためのプロキシ バイパス ルールを構成する (VMware エージェントレス移行の場合)

1. リモート デスクトップ経由で Azure Migrate アプライアンスにサインインします。
2.  メモ帳を使用して、*C:/ProgramData/MicrosoftAzure/Config/appliance.json* ファイルを開きます。
3. そのファイルで、`"EnableProxyBypassList": "false"` という行を `"EnableProxyBypassList": "true"` に変更します。 変更を保存し、アプライアンスを再起動します。
4. 再起動後、アプライアンス構成マネージャーを開くと、Web アプリの UI にプロキシ バイパス オプションが表示されます。 
5. レプリケーション トラフィックの場合は、".*.blob.core.windows.net" に対してプロキシ バイパス ルールを構成できます。 必要に応じて、他のコントロール プレーン エンドポイントに対してプロキシ バイパス ルールを構成できます。 たとえば、次のエンドポイントが該当します。 

    - .*.vault.azure.net
    - .*.servicebus.windows.net
    - .*.discoverysrv.windowsazure.com
    - .*.migration.windowsazure.com
    - .*.hypervrecoverymanager.windowsazure.com

> [!Note]
> 以下の URL には ExpressRoute 経由でアクセスできないため、インターネット接続が必要です。*.portal.azure.com、*.windows.net、*.msftauth.net、*.msauth.net、*.microsoft.com、*.live.com、*.office.com、*.microsoftonline.com、*.microsoftonline-p.com、*.microsoftazuread-sso.com、management.azure.com、 *.services.visualstudio.com (オプション)、aka.ms/* (オプション)、download.microsoft.com/download


#### <a name="configure-proxy-bypass-rules-expressroute-microsoft-peering-on-the-replication-appliance-for-agent-based-migrations"></a>レプリケーション アプライアンス上で ExpressRoute Microsoft ピアリングのためのプロキシ バイパス ルールを構成する (エージェントベース移行の場合)

構成サーバーとプロセス サーバー上でプロキシ バイパス リストを構成するには:

1. システム ユーザーのコンテキストにアクセスするための [PsExec ツール](/sysinternals/downloads/psexec)をダウンロードします。
2. 次のコマンド行を実行して、システム ユーザーのコンテキストで Internet Explorer を開きます: `psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"`。
3. Internet Explorer でプロキシの設定を追加します。
4. レプリケーション トラフィックの場合は、".*.blob.core.windows.net" に対してプロキシ バイパス ルールを構成できます。 必要に応じて、他のコントロール プレーン エンドポイントに対してプロキシ バイパス ルールを構成できます。 たとえば、次のエンドポイントが該当します。 

    - .*.backup.windowsazure.com
    - .*.hypervrecoverymanager.windowsazure.com

Azure Storage エンドポイントに対してバイパス ルールを構成することで、レプリケーション トラフィックは ExpressRoute を経由し、コントロール プレーン通信はインターネットのプロキシを経由するようになります。 

> [!Note]
> 以下の URL には ExpressRoute 経由でアクセスできないため、インターネット接続が必要です。*.portal.azure.com、*.windows.net、*.msftauth.net、*.msauth.net、*.microsoft.com、*.live.com、*.office.com、*.microsoftonline.com、*.microsoftonline-p.com、*.microsoftazuread-sso.com、management.azure.com、 *.services.visualstudio.com (オプション)、aka.ms/* (オプション)、download.microsoft.com/download、dev.mysql.com

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [ExpressRoute 回線](../expressroute/expressroute-circuit-peerings.md)
- [ExpressRoute のルーティング ドメイン](../expressroute/expressroute-circuit-peerings.md#peeringcompare)
- [プライベート エンドポイント](../private-link/private-endpoint-overview.md)
